```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'New Purchase';
$active_page = 'purchases';

// Check if user is logged in
if(!isLoggedIn()) {
    redirect('login.php');
}

// Process form submission
if($_SERVER['REQUEST_METHOD'] == 'POST') {
    try {
        // Start transaction
        $conn->beginTransaction();
        
        $supplier_id = $_POST['supplier_id'];
        $user_id = $_SESSION['user_id'];
        $total_amount = 0;
        
        // Calculate total amount
        foreach($_POST['product_id'] as $key => $product_id) {
            if(!empty($product_id)) {
                $quantity = $_POST['quantity'][$key];
                $price = $_POST['price'][$key];
                $subtotal = $quantity * $price;
                $total_amount += $subtotal;
            }
        }
        
        // Insert purchase order
        $stmt = $conn->prepare("INSERT INTO purchases (supplier_id, user_id, total_amount, status) VALUES (?, ?, ?, 'pending')");
        $stmt->execute([$supplier_id, $user_id, $total_amount]);
        $purchase_id = $conn->lastInsertId();
        
        // Insert purchase items
        foreach($_POST['product_id'] as $key => $product_id) {
            if(!empty($product_id)) {
                $quantity = $_POST['quantity'][$key];
                $price = $_POST['price'][$key];
                $subtotal = $quantity * $price;
                
                $stmt = $conn->prepare("INSERT INTO purchase_items (purchase_id, product_id, quantity, price_each, subtotal) VALUES (?, ?, ?, ?, ?)");
                $stmt->execute([$purchase_id, $product_id, $quantity, $price, $subtotal]);
            }
        }
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Purchase order created successfully', 'success');
        redirect('purchases.php');
    } catch(PDOException $e) {
        // Rollback on error
        $conn->rollBack();
        setMessage('Error creating purchase: ' . $e->getMessage(), 'danger');
    }
}

// Get all suppliers
try {
    $stmt = $conn->query("SELECT supplier_id, supplier_name FROM suppliers ORDER BY supplier_name");
    $suppliers = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading suppliers: ' . $e->getMessage(), 'danger');
    $suppliers = [];
}

// Get all products
try {
    $stmt = $conn->query("SELECT product_id, product_name, cost_price, quantity FROM products ORDER BY product_name");
    $products = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading products: ' . $e->getMessage(), 'danger');
    $products = [];
}

// Include the layout
$content_file = 'views/new_purchase_view.php';
include('layout.php');
?>
```