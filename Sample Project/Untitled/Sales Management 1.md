```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Sales Management';
$active_page = 'sales';

// Process delete sale if requested
if(isset($_POST['delete_sale']) && isset($_POST['sale_id'])) {
    try {
        $sale_id = $_POST['sale_id'];
        
        // Start transaction
        $conn->beginTransaction();
        
        // Get items from the sale to restore inventory
        $stmt = $conn->prepare("SELECT product_id, quantity FROM sale_items WHERE sale_id = ?");
        $stmt->execute([$sale_id]);
        $sale_items = $stmt->fetchAll();
        
        // Restore product quantities
        foreach($sale_items as $item) {
            $stmt = $conn->prepare("UPDATE products SET quantity = quantity + ? WHERE product_id = ?");
            $stmt->execute([$item['quantity'], $item['product_id']]);
        }
        
        // Delete sale items first (foreign key constraint)
        $stmt = $conn->prepare("DELETE FROM sale_items WHERE sale_id = ?");
        $stmt->execute([$sale_id]);
        
        // Delete the sale
        $stmt = $conn->prepare("DELETE FROM sales WHERE sale_id = ?");
        $stmt->execute([$sale_id]);
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Sale deleted successfully', 'success');
    } catch(PDOException $e) {
        // Rollback on error
        $conn->rollBack();
        setMessage('Error deleting sale: ' . $e->getMessage(), 'danger');
    }
    
    redirect('sales.php');
}

// Get all sales with customer names
try {
    $stmt = $conn->query("SELECT s.*, c.customer_name, u.username 
                         FROM sales s 
                         LEFT JOIN customers c ON s.customer_id = c.customer_id 
                         LEFT JOIN users u ON s.user_id = u.user_id
                         ORDER BY s.sale_date DESC");
    $sales = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading sales: ' . $e->getMessage(), 'danger');
    $sales = [];
}

// Include the layout
$content_file = 'views/sales_view.php';
include('layout.php');
?>
```