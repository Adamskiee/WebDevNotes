```php
<?php
require_once 'config/db.php';

// Define page variables for template
$page_title = 'Products Management';
$active_page = 'products';

// Process form submission for add/edit product
if($_SERVER['REQUEST_METHOD'] == 'POST') {
    // Check if it's an add or edit operation
    if(isset($_POST['add_product']) || isset($_POST['edit_product'])) {
        $product_name = sanitize($_POST['product_name']);
        $category_id = $_POST['category_id'];
        $description = sanitize($_POST['description']);
        $price = $_POST['price'];
        $cost_price = $_POST['cost_price'];
        $quantity = $_POST['quantity'];
        $reorder_level = $_POST['reorder_level'];
        
        try {
            // If editing existing product
            if(isset($_POST['edit_product'])) {.
            $product_id = $_POST['product_id'];
                $stmt = $conn->prepare("UPDATE products SET 
                    product_name = ?, 
                    category_id = ?, 
                    description = ?, 
                    price = ?, 
                    cost_price = ?, 
                    quantity = ?, 
                    reorder_level = ? 
                    WHERE product_id = ?");
                
                $stmt->execute([$product_name, $category_id, $description, $price, $cost_price, $quantity, $reorder_level, $product_id]);
                setMessage('Product updated successfully', 'success');
            } 
            // If adding new product
            else {
                $stmt = $conn->prepare("INSERT INTO products (product_name, category_id, description, price, cost_price, quantity, reorder_level) 
                    VALUES (?, ?, ?, ?, ?, ?, ?)");
                
                $stmt->execute([$product_name, $category_id, $description, $price, $cost_price, $quantity, $reorder_level]);
                setMessage('Product added successfully', 'success');
            }
            
            // Redirect to avoid form resubmission
            redirect('products.php');
            
        } catch(PDOException $e) {
            setMessage('Error: ' . $e->getMessage(), 'danger');
        }
    }
    
    // Process delete product
    if(isset($_POST['delete_product'])) {
        try {
            $product_id = $_POST['product_id'];
            
            // Check if product is used in any sales or purchases
            $stmt = $conn->prepare("SELECT COUNT(*) as count FROM sale_items WHERE product_id = ?");
            $stmt->execute([$product_id]);
            $sales_count = $stmt->fetch()['count'];
            
            $stmt = $conn->prepare("SELECT COUNT(*) as count FROM purchase_items WHERE product_id = ?");
            $stmt->execute([$product_id]);
            $purchase_count = $stmt->fetch()['count'];
            
            if($sales_count > 0 || $purchase_count > 0) {
                setMessage('Cannot delete product as it is associated with sales or purchases', 'danger');
            } else {
                $stmt = $conn->prepare("DELETE FROM products WHERE product_id = ?");
                $stmt->execute([$product_id]);
                setMessage('Product deleted successfully', 'success');
            }
            
            redirect('products.php');
            
        } catch(PDOException $e) {
            setMessage('Error: ' . $e->getMessage(), 'danger');
        }
    }
}

// Get single product for editing
$edit_product = null;
if(isset($_GET['edit']) && is_numeric($_GET['edit'])) {
    try {
        $stmt = $conn->prepare("SELECT * FROM products WHERE product_id = ?");
        $stmt->execute([$_GET['edit']]);
        $edit_product = $stmt->fetch();
        
        if(!$edit_product) {
            setMessage('Product not found', 'danger');
            redirect('products.php');
        }
    } catch(PDOException $e) {
        setMessage('Error: ' . $e->getMessage(), 'danger');
    }
}

// Get all categories for dropdown
try {
    $stmt = $conn->query("SELECT category_id, category_name FROM categories ORDER BY category_name");
    $categories = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading categories: ' . $e->getMessage(), 'danger');
    $categories = [];
}

// Get all products with category names
try {
    $stmt = $conn->query("SELECT p.*, c.category_name 
                         FROM products p 
                         LEFT JOIN categories c ON p.category_id = c.category_id 
                         ORDER BY p.product_name");
    $products = $stmt->fetchAll();
} catch(PDOException $e) {
    setMessage('Error loading products: ' . $e->getMessage(), 'danger');
    $products = [];
}

// Include the layout
$content_file = 'views/products_view.php';
include('layout.php');
?>
```