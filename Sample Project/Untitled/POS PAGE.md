```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Point of Sale';
$active_page = 'pos';

// Check if user is logged in
if(!isLoggedIn()) {
    redirect('login.php');
}

// Process sale form submission
if($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_POST['process_sale'])) {
    try {
        // Start transaction
        $conn->beginTransaction();
        
        $customer_id = !empty($_POST['customer_id']) ? $_POST['customer_id'] : null;
        $user_id = $_SESSION['user_id'];
        $payment_method = $_POST['payment_method'];
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
        
        // Insert sale
        $stmt = $conn->prepare("INSERT INTO sales (customer_id, user_id, total_amount, payment_method) VALUES (?, ?, ?, ?)");
        $stmt->execute([$customer_id, $user_id, $total_amount, $payment_method]);
        $sale_id = $conn->lastInsertId();
        
        // Insert sale items and update inventory
        foreach($_POST['product_id'] as $key => $product_id) {
            if(!empty($product_id)) {
                $quantity = $_POST['quantity'][$key];
                $price = $_POST['price'][$key];
                $subtotal = $quantity * $price;
                
                // Insert sale item
                $stmt = $conn->prepare("INSERT INTO sale_items (sale_id, product_id, quantity, price_each, subtotal) VALUES (?, ?, ?, ?, ?)");
                $stmt->execute([$sale_id, $product_id, $quantity, $price, $subtotal]);
                
                // Update product quantity
                $stmt = $conn->prepare("UPDATE products SET quantity = quantity - ? WHERE product_id = ?");
                $stmt->execute([$quantity, $product_id]);
            }
        }
        
        // Commit transaction
        $conn->commit();
        
        // Save sale ID for receipt
        $_SESSION['last_sale_id'] = $sale_id;
        
        setMessage('Sale processed successfully', 'success');
        redirect('receipt.php?id=' . $sale_id);
    } catch(PDOException $e) {
        // Rollback on error
        $conn->rollBack();
        setMessage('Error processing sale: ' . $e->getMessage(), 'danger');
    }
}

// Get all products
try {
    $stmt = $conn->query("SELECT product_id, product_name, price, quantity FROM products WHERE quantity > 0 ORDER BY product_name");
    $products = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading products: ' . $e->getMessage(), 'danger');
    $products = [];
}

// Get all categories for filtering
try {
    $stmt = $conn->query("SELECT category_id, category_name FROM categories ORDER BY category_name");
    $categories = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading categories: ' . $e->getMessage(), 'danger');
    $categories = [];
}

// Get all customers
try {
    $stmt = $conn->query("SELECT customer_id, customer_name FROM customers ORDER BY customer_name");
    $customers = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading customers: ' . $e->getMessage(), 'danger');
    $customers = [];
}

// Include the layout
$content_file = 'views/pos_view.php';
include('layout.php');
?>

```
