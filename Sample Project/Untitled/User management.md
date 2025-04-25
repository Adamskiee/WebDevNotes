```php
<?php
require_once 'config/db.php';

// Check if user is admin
if(!isAdmin()) {
    setMessage('Access denied. Admin privileges required.', 'danger');
    redirect('index.php');
}

// Define page variables for template
$page_title = 'User Management';
$active_page = 'users';

// Process form submission for add/edit user
if($_SERVER['REQUEST_METHOD'] == 'POST') {
    // Check if it's an add or edit operation
    if(isset($_POST['add_user']) || isset($_POST['edit_user'])) {
        $username = sanitize($_POST['username']);
        $full_name = sanitize($_POST['full_name']);
        $email = sanitize($_POST['email']);
        $role = $_POST['role'];
        
        try {
            // If editing existing user
            if(isset($_POST['edit_user'])) {
                $user_id = $_POST['user_id'];
                
                // Check if changing password
                if(!empty($_POST['password'])) {
                    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);
                    $stmt = $conn->prepare("UPDATE users SET username = ?, full_name = ?, email = ?, role = ?, password = ? WHERE user_id = ?");
                    $stmt->execute([$username, $full_name, $email, $role, $password, $user_id]);
                } else {
                    $stmt = $conn->prepare("UPDATE users SET username = ?, full_name = ?, email = ?, role = ? WHERE user_id = ?");
                    $stmt->execute([$username, $full_name, $email, $role, $user_id]);
                }
                
                setMessage('User updated successfully', 'success');
            } 
            // If adding new user
            else {
                // Password is required for new users
                if(empty($_POST['password'])) {
                    setMessage('Password is required for new users', 'danger');
                } else {
                    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);
                    $stmt = $conn->prepare("INSERT INTO users (username, password, full_name, email, role) VALUES (?, ?, ?, ?, ?)");
                    $stmt->execute([$username, $password, $full_name, $email, $role]);
                    setMessage('User added successfully', 'success');
                }
            }
            
            // Redirect to avoid form resubmission
            redirect('users.php');
            
        } catch(PDOException $e) {
            // Check for duplicate username error
            if($e->getCode() == 23000) {
                setMessage('Username already exists. Please choose another username.', 'danger');
            } else {
                setMessage('Error: ' . $e->getMessage(), 'danger');
            }
        }
    }
    
    // Process delete user
    if(isset($_POST['delete_user'])) {
        try {
            $user_id = $_POST['user_id'];
            
            // Prevent deleting your own account
            if($user_id == $_SESSION['user_id']) {
                setMessage('You cannot delete your own account.', 'danger');
                redirect('users.php');
                exit;
            }
            
            // Check if user has any sales or purchases
            $stmt = $conn->prepare("SELECT COUNT(*) as count FROM sales WHERE user_id = ?");
            $stmt->execute([$user_id]);
            $sales_count = $stmt->fetch()['count'];
            
            $stmt = $conn->prepare("SELECT COUNT(*) as count FROM purchases WHERE user_id = ?");
            $stmt->execute([$user_id]);
            $purchase_count = $stmt->fetch()['count'];
            
            if($sales_count > 0 || $purchase_count > 0) {
                setMessage('Cannot delete user as they have associated sales or purchases. Consider deactivating instead.', 'danger');
            } else {
                $stmt = $conn->prepare("DELETE FROM users WHERE user_id = ?");
                $stmt->execute([$user_id]);
                setMessage('User deleted successfully', 'success');
            }
            
            redirect('users.php');
            
        } catch(PDOException $e) {
            setMessage('Error: ' . $e->getMessage(), 'danger');
        }
    }
}

// Get single user for editing
$edit_user = null;
if(isset($_GET['edit']) && is_numeric($_GET['edit'])) {
    try {
        $stmt = $conn->prepare("SELECT * FROM users WHERE user_id = ?");
        $stmt->execute([$_GET['edit']]);
        $edit_user = $stmt->fetch();
        
        if(!$edit_user) {
            setMessage('User not found', 'danger');
            redirect('users.php');
        }
    } catch(PDOException $e) {
        setMessage('Error: ' . $e->getMessage(), 'danger');
    }
}

// Get all users
try {
    $stmt = $conn->query("SELECT * FROM users ORDER BY username");
    $users = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading users: ' . $e->getMessage(), 'danger');
    $users = [];
}

// Include the layout
$content_file = 'views/users_view.php';
include('layout.php');
?>
```