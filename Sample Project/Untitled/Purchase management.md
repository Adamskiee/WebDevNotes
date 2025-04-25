```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Purchase Management';
$active_page = 'purchases';

// Process delete purchase if requested
if(isset($_POST['delete_purchase']) && isset($_POST['purchase_id'])) {
    try {
        $purchase_id = $_POST['purchase_id'];
        
        // Start transaction
        $conn->beginTransaction();
        
        // For received purchases, we need to decrease inventory
        $stmt = $conn->prepare("SELECT status FROM purchases WHERE purchase_id = ?");
        $stmt->execute([$purchase_id]);
        $purchase_status = $stmt->fetch()['status'];
        
        if($purchase_status == 'received') {
            // Get items from the purchase to update inventory
            $stmt = $conn->prepare("SELECT product_id, quantity FROM purchase_items WHERE purchase_id = ?");
            $stmt->execute([$purchase_id]);
            $purchase_items = $stmt->fetchAll();
            
            // Decrease product quantities
            foreach($purchase_items as $item) {
                $stmt = $conn->prepare("UPDATE products SET quantity = quantity - ? WHERE product_id = ?");
                $stmt->execute([$item['quantity'], $item['product_id']]);
            }
        }
        
        // Delete purchase items first (foreign key constraint)
        $stmt = $conn->prepare("DELETE FROM purchase_items WHERE purchase_id = ?");
        $stmt->execute([$purchase_id]);
        
        // Delete the purchase
        $stmt = $conn->prepare("DELETE FROM purchases WHERE purchase_id = ?");
        $stmt->execute([$purchase_id]);
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Purchase deleted successfully', 'success');
    } catch(PDOException $e) {
        // Rollback on error
        $conn->rollBack();
        setMessage('Error deleting purchase: ' . $e->getMessage(), 'danger');
    }
    
    redirect('purchases.php');
}

// Process purchase status update
if(isset($_POST['update_status']) && isset($_POST['purchase_id'])) {
    try {
        $purchase_id = $_POST['purchase_id'];
        $new_status = $_POST['status'];
        $old_status = $_POST['old_status'];
        
        // Start transaction
        $conn->beginTransaction();
        
        // If changing from pending to received, update inventory
        if($old_status == 'pending' && $new_status == 'received') {
            // Get purchase items
            $stmt = $conn->prepare("SELECT product_id, quantity FROM purchase_items WHERE purchase_id = ?");
            $stmt->execute([$purchase_id]);
            $purchase_items = $stmt->fetchAll();
            
            // Update product quantities
            foreach($purchase_items as $item) {
                $stmt = $conn->prepare("UPDATE products SET quantity = quantity + ? WHERE product_id = ?");
                $stmt->execute([$item['quantity'], $item['product_id']]);
            }
        }
        // If changing from received to pending or cancelled, decrease inventory
        elseif($old_status == 'received' && ($new_status == 'pending' || $new_status == 'cancelled')) {
            // Get purchase items
            $stmt = $conn->prepare("SELECT product_id, quantity FROM purchase_items WHERE purchase_id = ?");
            $stmt->execute([$purchase_id]);
            $purchase_items = $stmt->fetchAll();
            
            // Update product quantities
            foreach($purchase_items as $item) {
                $stmt = $conn->prepare("UPDATE products SET quantity = quantity - ? WHERE product_id = ?");
                $stmt->execute([$item['quantity'], $item['product_id']]);
            }
        }
        
        // Update purchase status
        $stmt = $conn->prepare("UPDATE purchases SET status = ? WHERE purchase_id = ?");
        $stmt->execute([$new_status, $purchase_id]);
        
        // Commit transaction
        $conn->commit();
        
        setMessage('Purchase status updated successfully', 'success');
    } catch(PDOException $e) {
        // Rollback on error
        $conn->rollBack();
        setMessage('Error updating purchase status: ' . $e->getMessage(), 'danger');
    }
    
    redirect('purchases.php');
}

// Get all purchases with supplier names
try {
    $stmt = $conn->query("SELECT p.*, s.supplier_name, u.username 
                         FROM purchases p 
                         LEFT JOIN suppliers s ON p.supplier_id = s.supplier_id 
                         LEFT JOIN users u ON p.user_id = u.user_id
                         ORDER BY p.purchase_date DESC");
    $purchases = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading purchases: ' . $e->getMessage(), 'danger');
    $purchases = [];
}

// Include the layout
$content_file = 'views/purchases_view.php';
include('layout.php');
?>
```