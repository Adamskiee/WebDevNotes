```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Purchase Details';
$active_page = 'purchases';

// Check if user is logged in
if(!isLoggedIn()) {
    redirect('login.php');
}

// Check if ID is provided
if(!isset($_GET['id']) || empty($_GET['id'])) {
    setMessage('Invalid purchase ID', 'danger');
    redirect('purchases.php');
}

$purchase_id = $_GET['id'];

// Get purchase details
try {
    $stmt = $conn->prepare("SELECT p.*, s.supplier_name, s.contact_person, s.phone, s.email, u.username 
                           FROM purchases p 
                           LEFT JOIN suppliers s ON p.supplier_id = s.supplier_id 
                           LEFT JOIN users u ON p.user_id = u.user_id
                           WHERE p.purchase_id = ?");
    $stmt->execute([$purchase_id]);
    $purchase = $stmt->fetch();
    
    if(!$purchase) {
        setMessage('Purchase not found', 'danger');
        redirect('purchases.php');
    }
    
    // Get purchase items
    $stmt = $conn->prepare("SELECT pi.*, p.product_name 
                           FROM purchase_items pi 
                           JOIN products p ON pi.product_id = p.product_id 
                           WHERE pi.purchase_id = ?");
    $stmt->execute([$purchase_id]);
    $purchase_items = $stmt->fetchAll();
    
} catch(PDOException $e) {
    setMessage('Error loading purchase details: ' . $e->getMessage(), 'danger');
    redirect('purchases.php');
}

// Include the layout
$content_file = 'views/view_purchase_view.php';
include('layout.php');
?>
```