```php
<?php
// Define page variables for template
$page_title = 'Dashboard';
$active_page = 'dashboard';

// Get statistics data
try {
    // Total products
    $stmt = $conn->query("SELECT COUNT(*) as total FROM products");
    $total_products = $stmt->fetch()['total'];
    
    // Low stock products
    $stmt = $conn->query("SELECT COUNT(*) as total FROM products WHERE quantity <= reorder_level");
    $low_stock = $stmt->fetch()['total'];
    
    // Today's sales
    $stmt = $conn->query("SELECT SUM(total_amount) as total FROM sales WHERE DATE(sale_date) = CURDATE()");
    $today_sales = $stmt->fetch()['total'] ?? 0;
    
    // Total customers
    $stmt = $conn->query("SELECT COUNT(*) as total FROM customers");
    $total_customers = $stmt->fetch()['total'];
    
    // Recent sales
    $stmt = $conn->query("SELECT s.sale_id, s.sale_date, s.total_amount, c.customer_name 
                         FROM sales s 
                         LEFT JOIN customers c ON s.customer_id = c.customer_id 
                         ORDER BY s.sale_date DESC LIMIT 5");
    $recent_sales = $stmt->fetchAll();
    
    // Top selling products
    $stmt = $conn->query("SELECT p.product_name, SUM(si.quantity) as total_sold 
                         FROM sale_items si 
                         JOIN products p ON si.product_id = p.product_id 
                         GROUP BY si.product_id 
                         ORDER BY total_sold DESC LIMIT 5");
    $top_products = $stmt->fetchAll();
    
} catch(PDOException $e) {
    setMessage('Database error: ' . $e->getMessage(), 'danger');
}

// Include the layout
$content_file = 'views/dashboard_view.php';
include('layout.php');
?>
```