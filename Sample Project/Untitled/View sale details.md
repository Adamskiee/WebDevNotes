```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Sale Details';
$active_page = 'sales';

// Check if sale ID is provided
if (!isset($_GET['id']) || !is_numeric($_GET['id'])) {
    setMessage('Invalid sale ID', 'danger');
    redirect('sales.php');
}

$sale_id = $_GET['id'];

try {
    // Get sale details
    $stmt = $conn->prepare("SELECT s.*, c.customer_name, c.phone, c.email, c.address, u.username 
                           FROM sales s 
                           LEFT JOIN customers c ON s.customer_id = c.customer_id 
                           LEFT JOIN users u ON s.user_id = u.user_id 
                           WHERE s.sale_id = ?");
    $stmt->execute([$sale_id]);
    $sale = $stmt->fetch();
    
    if (!$sale) {
        setMessage('Sale not found', 'danger');
        redirect('sales.php');
    }
    
    // Get sale items
    $stmt = $conn->prepare("SELECT si.*, p.product_name 
                          FROM sale_items si 
                          JOIN products p ON si.product_id = p.product_id 
                          WHERE si.sale_id = ?");
    $stmt->execute([$sale_id]);
    $sale_items = $stmt->fetchAll();
    
} catch(PDOException $e) {
    setMessage('Database error: ' . $e->getMessage(), 'danger');
    redirect('sales.php');
}

// Include the layout
$content_file = 'views/sale_details_view.php';
include('layout.php');
?>
```