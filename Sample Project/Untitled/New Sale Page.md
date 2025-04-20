```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'New Sale';
$active_page = 'sales';

// Get all products for the product selection
try {
    $stmt = $conn->query("SELECT p.product_id, p.product_name, p.price, p.quantity, c.category_name 
                         FROM products p 
                         LEFT JOIN categories c ON p.category_id = c.category_id 
                         WHERE p.quantity > 0
                         ORDER BY p.product_name");
    $available_products = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading products: ' . $e->getMessage(), 'danger');
    $available_products = [];
}

// Get all customers for dropdown
try {
    $stmt = $conn->query("SELECT customer_id, customer_name FROM customers ORDER BY customer_name");
    $customers = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading customers: ' . $e->getMessage(), 'danger');
    $customers = [];
}

// Process sale form submission
if($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_POST['complete_sale'])) {
    try {
        // Start transaction
        $conn->beginTransaction();
        
        $customer_id = !empty($_POST['customer_id']) ? $_POST['customer_id'] : null;
        $payment_method = $_POST['payment_method'];
        $total_amount = $_POST['total_amount'];
        $user_id = $_SESSION['user_id'];
        
        // Insert into sales table
        $stmt = $conn->prepare("INSERT INTO sales (customer_id, user_id, total_amount, payment_method) 
                              VALUES (?, ?, ?, ?)");
        $stmt->execute([$customer_id, $user_id, $total_amount, $payment_method]);
        
        $sale_id = $conn->lastInsertId();
        
        // Process each item in the sale
        $product_ids = $_POST['product_id'];
        $quantities = $_POST['quantity'];
        $prices = $_POST['price'];
        $subtotals = $_POST['subtotal'];
        
        for($i = 0; $i < count($product_ids); $i++) {
            // Insert into sale_items table
            $stmt = $conn->prepare("INSERT INTO sale_items (sale_id, product_id, quantity, price_each, subtotal) 
                                  VALUES (?, ?, ?, ?, ?)");
            $stmt->execute([$sale_id, $product_ids[$i], $quantities[$i], $prices[$i], $subtotals[$i]]);
            
            // Update product quantity in products table
            $stmt = $conn->prepare("UPDATE products SET quantity = quantity - ? WHERE product_id = ?");
            $stmt->execute([$quantities[$i], $product_ids[$i]]);
        }
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Sale completed successfully', 'success');
        redirect('sales.php');
        
    } catch(PDOException $e) {
        // Rollback transaction on error
        $conn->rollBack();
        setMessage('Error processing sale: ' . $e->getMessage(), 'danger');
    }
}

// Include the layout
$content_file = 'views/new_sale_view.php';
include('layout.php');
?>
```