```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Sales Management';
$active_page = 'sales';

// Initialize variables
$sales = [];
$products = [];
$customers = [];

try {
    // Get all sales with customer info
    $stmt = $conn->query("SELECT s.*, c.customer_name 
                         FROM sales s 
                         LEFT JOIN customers c ON s.customer_id = c.customer_id 
                         ORDER BY s.sale_date DESC");
    $sales = $stmt->fetchAll();
    
    // Get all products for new sale form
    $stmt = $conn->query("SELECT product_id, product_name, price, quantity FROM products WHERE quantity > 0 ORDER BY product_name");
    $products = $stmt->fetchAll();
    
    // Get all customers for new sale form
    $stmt = $conn->query("SELECT customer_id, customer_name FROM customers ORDER BY customer_name");
    $customers = $stmt->fetchAll();
    
} catch(PDOException $e) {
    setMessage('Database error: ' . $e->getMessage(), 'danger');
}

// Process form submission for adding a new sale
if ($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_POST['add_sale'])) {
    $customer_id = !empty($_POST['customer_id']) ? $_POST['customer_id'] : null;
    $payment_method = $_POST['payment_method'];
    $product_ids = $_POST['product_id'];
    $quantities = $_POST['quantity'];
    $prices = $_POST['price'];
    $subtotals = $_POST['subtotal'];
    $total_amount = 0;
    
    // Calculate total amount
    foreach ($subtotals as $subtotal) {
        $total_amount += $subtotal;
    }
    
    try {
        // Start transaction
        $conn->beginTransaction();
        
        // Insert sale record
        $stmt = $conn->prepare("INSERT INTO sales (customer_id, user_id, total_amount, payment_method) VALUES (?, ?, ?, ?)");
        $stmt->execute([$customer_id, $_SESSION['user_id'], $total_amount, $payment_method]);
        $sale_id = $conn->lastInsertId();
        
        // Insert sale items and update product quantities
        for ($i = 0; $i < count($product_ids); $i++) {
            if (empty($product_ids[$i]) || empty($quantities[$i])) {
                continue;
            }
            
            $product_id = $product_ids[$i];
            $quantity = $quantities[$i];
            $price = $prices[$i];
            $subtotal = $subtotals[$i];
            
            // Insert sale item
            $stmt = $conn->prepare("INSERT INTO sale_items (sale_id, product_id, quantity, price_each, subtotal) VALUES (?, ?, ?, ?, ?)");
            $stmt->execute([$sale_id, $product_id, $quantity, $price, $subtotal]);
            
            // Update product quantity
            $stmt = $conn->prepare("UPDATE products SET quantity = quantity - ? WHERE product_id = ?");
            $stmt->execute([$quantity, $product_id]);
        }
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Sale added successfully', 'success');
        redirect('sale_details.php?id=' . $sale_id);
        
    } catch(PDOException $e) {
        // Rollback transaction on error
        $conn->rollBack();
        setMessage('Error: ' . $e->getMessage(), 'danger');
    }
}

// Include the layout
$content_file = 'views/sales_view.php';
include('layout.php');
?>
```