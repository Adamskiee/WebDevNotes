I'll create a Store Management System for you using HTML, CSS, JavaScript, PHP, and MySQL. This system will include key features like inventory management, sales tracking, user authentication, and basic reporting.

Let's start with the database structure and then move on to the implementation:

```sql
-- Create database
CREATE DATABASE store_management;
USE store_management;

-- Users table for authentication
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    role ENUM('admin', 'employee') NOT NULL DEFAULT 'employee',
    email VARCHAR(100) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories table
CREATE TABLE categories (
    category_id INT AUTO_INCREMENT PRIMARY KEY,
    category_name VARCHAR(50) NOT NULL UNIQUE,
    description TEXT
);

-- Products table
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category_id INT,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    cost_price DECIMAL(10, 2) NOT NULL,
    quantity INT NOT NULL DEFAULT 0,
    reorder_level INT DEFAULT 10,
    FOREIGN KEY (category_id) REFERENCES categories(category_id) ON DELETE SET NULL
);

-- Customers table
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL,
    email VARCHAR(100),
    phone VARCHAR(20),
    address TEXT
);

-- Sales table
CREATE TABLE sales (
    sale_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    user_id INT NOT NULL,
    sale_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL,
    payment_method ENUM('cash', 'credit_card', 'debit_card', 'online') NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE SET NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Sale items table (for individual items in a sale)
CREATE TABLE sale_items (
    sale_item_id INT AUTO_INCREMENT PRIMARY KEY,
    sale_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price_each DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (sale_id) REFERENCES sales(sale_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Suppliers table
CREATE TABLE suppliers (
    supplier_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_name VARCHAR(100) NOT NULL,
    contact_person VARCHAR(100),
    email VARCHAR(100),
    phone VARCHAR(20),
    address TEXT
);

-- Purchase orders table
CREATE TABLE purchases (
    purchase_id INT AUTO_INCREMENT PRIMARY KEY,
    supplier_id INT NOT NULL,
    user_id INT NOT NULL,
    purchase_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL,
    status ENUM('pending', 'received', 'cancelled') DEFAULT 'pending',
    FOREIGN KEY (supplier_id) REFERENCES suppliers(supplier_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- Purchase items table
CREATE TABLE purchase_items (
    purchase_item_id INT AUTO_INCREMENT PRIMARY KEY,
    purchase_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price_each DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (purchase_id) REFERENCES purchases(purchase_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- Insert default admin user (password is 'admin123')
INSERT INTO users (username, password, full_name, role, email) 
VALUES ('admin', '$2y$10$fwknoIzST3blylHAZ82sN.RyQA7vL9lTdwJT8AXmxVrHBhTHpjlym', 'Admin User', 'admin', 'admin@example.com');

-- Insert sample categories
INSERT INTO categories (category_name, description) VALUES
('Electronics', 'Electronic devices and accessories'),
('Clothing', 'All types of clothing items'),
('Groceries', 'Food and household items'),
('Stationery', 'Office and school supplies');

-- Insert sample products
INSERT INTO products (product_name, category_id, description, price, cost_price, quantity, reorder_level) VALUES
('Laptop', 1, '15-inch laptop with 8GB RAM', 899.99, 700.00, 20, 5),
('Smartphone', 1, 'Latest model smartphone', 699.99, 500.00, 30, 10),
('T-shirt', 2, 'Cotton t-shirt, various colors', 19.99, 8.00, 100, 20),
('Jeans', 2, 'Denim jeans, various sizes', 45.99, 25.00, 50, 15),
('Rice (5kg)', 3, '5kg bag of white rice', 12.99, 8.50, 40, 15),
('Notebook', 4, 'Spiral-bound notebook, 100 pages', 4.99, 2.00, 200, 50);

-- Insert sample customers
INSERT INTO customers (customer_name, email, phone, address) VALUES
('John Doe', 'john@example.com', '555-1234', '123 Main St, Anytown'),
('Jane Smith', 'jane@example.com', '555-5678', '456 Oak Ave, Somewhere');

-- Insert sample suppliers
INSERT INTO suppliers (supplier_name, contact_person, email, phone, address) VALUES
('Tech Supplies Inc.', 'Mark Johnson', 'mark@techsupplies.com', '555-9876', '789 Tech Blvd, Techville'),
('Fashion Wholesalers', 'Sarah Brown', 'sarah@fashionwholesalers.com', '555-4321', '321 Fashion St, Styletown');

-- Create indexes for performance
CREATE INDEX idx_product_category ON products(category_id);
CREATE INDEX idx_sale_user ON sales(user_id);
CREATE INDEX idx_sale_customer ON sales(customer_id);
CREATE INDEX idx_purchase_supplier ON purchases(supplier_id);

```

Now let's create the connection file to connect to the database:

```php
<?php
// Database configuration
$db_host = "localhost";
$db_name = "store_management";
$db_user = "root";
$db_pass = "";

// Create connection
try {
    $conn = new PDO("mysql:host=$db_host;dbname=$db_name", $db_user, $db_pass);
    // Set the PDO error mode to exception
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    // Set default fetch mode to associative array
    $conn->setAttribute(PDO::ATTR_DEFAULT_FETCH_MODE, PDO::FETCH_ASSOC);
} catch(PDOException $e) {
    die("Connection failed: " . $e->getMessage());
}

// Function to sanitize user inputs
function sanitize($data) {
    $data = trim($data);
    $data = stripslashes($data);
    $data = htmlspecialchars($data);
    return $data;
}

// Start session if not already started
if (session_status() == PHP_SESSION_NONE) {
    session_start();
}

// Function to check if user is logged in
function isLoggedIn() {
    return isset($_SESSION['user_id']);
}

// Function to check if user is admin
function isAdmin() {
    return (isset($_SESSION['role']) && $_SESSION['role'] == 'admin');
}

// Function to redirect user
function redirect($url) {
    header("Location: $url");
    exit();
}

// Function to show message
function setMessage($message, $type = 'info') {
    $_SESSION['message'] = $message;
    $_SESSION['message_type'] = $type;
}

// Function to display message and clear it
function showMessage() {
    if(isset($_SESSION['message'])) {
        $message = $_SESSION['message'];
        $type = $_SESSION['message_type'];
        unset($_SESSION['message']);
        unset($_SESSION['message_type']);
        return "<div class='alert alert-$type'>$message</div>";
    }
    return "";
}
?>

```

Let's create the login page:

```php
<?php
require_once 'config/db.php';

// Redirect if already logged in
if(isLoggedIn()) {
    redirect('index.php');
}

// Process login form
if($_SERVER['REQUEST_METHOD'] == 'POST') {
    $username = sanitize($_POST['username']);
    $password = $_POST['password'];
    
    if(empty($username) || empty($password)) {
        setMessage('Please enter both username and password', 'danger');
    } else {
        try {
            $stmt = $conn->prepare("SELECT user_id, username, password, role, full_name FROM users WHERE username = ?");
            $stmt->execute([$username]);
            $user = $stmt->fetch();
            
            if($user && password_verify($password, $user['password'])) {
                // Login successful
                $_SESSION['user_id'] = $user['user_id'];
                $_SESSION['username'] = $user['username'];
                $_SESSION['role'] = $user['role'];
                $_SESSION['full_name'] = $user['full_name'];
                
                setMessage('Login successful', 'success');
                redirect('index.php');
            } else {
                setMessage('Invalid username or password', 'danger');
            }
        } catch(PDOException $e) {
            setMessage('Error: ' . $e->getMessage(), 'danger');
        }
    }
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login - Store Management System</title>
    <link rel="stylesheet" href="assets/css/style.css">
</head>
<body class="login-page">
    <div class="login-container">
        <div class="login-box">
            <h2>Store Management System</h2>
            <?php echo showMessage(); ?>
            <form method="POST" action="">
                <div class="input-group">
                    <label for="username">Username</label>
                    <input type="text" id="username" name="username" required>
                </div>
                <div class="input-group">
                    <label for="password">Password</label>
                    <input type="password" id="password" name="password" required>
                </div>
                <button type="submit" class="btn btn-primary btn-block">Login</button>
            </form>
        </div>
    </div>
</body>
</html>

```

Now, let's create the CSS styles for our application:

```css
/* Reset CSS */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background-color: #f5f5f5;
    color: #333;
    line-height: 1.6;
}

/* Login Page Styles */
.login-page {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: linear-gradient(135deg, #6e8efb, #a777e3);
}

.login-container {
    width: 100%;
    max-width: 400px;
    padding: 20px;
}

.login-box {
    background-color: white;
    padding: 30px;
    border-radius: 8px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.login-box h2 {
    text-align: center;
    margin-bottom: 20px;
    color: #333;
}

/* Forms */
.input-group {
    margin-bottom: 20px;
}

.input-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: 500;
}

.input-group input,
.input-group select,
.input-group textarea {
    width: 100%;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
}

.input-group input:focus,
.input-group select:focus,
.input-group textarea:focus {
    outline: none;
    border-color: #6e8efb;
}

/* Buttons */
.btn {
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s;
}

.btn-primary {
    background-color: #6e8efb;
    color: white;
}

.btn-secondary {
    background-color: #6c757d;
    color: white;
}

.btn-success {
    background-color: #28a745;
    color: white;
}

.btn-danger {
    background-color: #dc3545;
    color: white;
}

.btn-block {
    display: block;
    width: 100%;
}

.btn:hover {
    opacity: 0.9;
}

/* Main Layout */
.main-container {
    display: flex;
    min-height: 100vh;
}

.sidebar {
    width: 250px;
    background-color: #343a40;
    color: white;
    padding-top: 20px;
    position: fixed;
    height: 100vh;
    overflow-y: auto;
}

.content {
    flex: 1;
    margin-left: 250px;
    padding: 20px;
}

.sidebar-header {
    padding: 0 20px 20px 20px;
    text-align: center;
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.sidebar-menu {
    padding: 20px 0;
}

.sidebar-menu ul {
    list-style: none;
}

.sidebar-menu li {
    margin-bottom: 5px;
}

.sidebar-menu a {
    display: block;
    padding: 10px 20px;
    color: rgba(255, 255, 255, 0.8);
    text-decoration: none;
    transition: all 0.3s;
}

.sidebar-menu a:hover,
.sidebar-menu a.active {
    background-color: rgba(255, 255, 255, 0.1);
    color: white;
}

.sidebar-menu i {
    margin-right: 10px;
}

/* Top Navigation */
.top-nav {
    background-color: white;
    padding: 15px 20px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.top-nav .user-info {
    display: flex;
    align-items: center;
}

.top-nav .user-name {
    margin-right: 10px;
}

/* Cards */
.card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    margin-bottom: 20px;
}

.card-header {
    padding: 15px 20px;
    border-bottom: 1px solid #eee;
    font-weight: bold;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.card-body {
    padding: 20px;
}

/* Tables */
.table-container {
    overflow-x: auto;
}

table {
    width: 100%;
    border-collapse: collapse;
}

th, td {
    padding: 12px 15px;
    text-align: left;
    border-bottom: 1px solid #ddd;
}

th {
    background-color: #f8f9fa;
    font-weight: bold;
}

tr:hover {
    background-color: #f5f5f5;
}

/* Dashboard */
.dashboard-stats {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    margin-bottom: 20px;
}

.stat-card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    padding: 20px;
    display: flex;
    flex-direction: column;
}

.stat-card h3 {
    font-size: 16px;
    color: #666;
    margin-bottom: 10px;
}

.stat-card .stat-value {
    font-size: 24px;
    font-weight: bold;
    color: #333;
}

/* Forms */
.form-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
}

/* Alerts */
.alert {
    padding: 12px 15px;
    margin-bottom: 20px;
    border-radius: 4px;
}

.alert-success {
    background-color: #d4edda;
    color: #155724;
}

.alert-danger {
    background-color: #f8d7da;
    color: #721c24;
}

.alert-warning {
    background-color: #fff3cd;
    color: #856404;
}

.alert-info {
    background-color: #d1ecf1;
    color: #0c5460;
}

/* Utilities */
.text-right {
    text-align: right;
}

.mt-2 {
    margin-top: 20px;
}

.mb-2 {
    margin-bottom: 20px;
}

/* Responsive */
@media (max-width: 768px) {
    .sidebar {
        width: 70px;
        z-index: 1000;
    }
    
    .sidebar-header h3 {
        display: none;
    }
    
    .sidebar-menu span {
        display: none;
    }
    
    .sidebar-menu i {
        margin-right: 0;
    }
    
    .content {
        margin-left: 70px;
    }
    
    .dashboard-stats {
        grid-template-columns: 1fr;
    }
}

```

Now, let's create the main layout file:

```php
<?php
require_once 'config/db.php';

// Check if user is logged in
if(!isLoggedIn()) {
    redirect('login.php');
}

// Handle logout
if(isset($_GET['logout'])) {
    session_destroy();
    redirect('login.php');
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?php echo $page_title ?? 'Store Management System'; ?></title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <link rel="stylesheet" href="assets/css/style.css">
</head>
<body>
    <div class="main-container">
        <!-- Sidebar -->
        <div class="sidebar">
            <div class="sidebar-header">
                <h3>Store Management</h3>
            </div>
            <div class="sidebar-menu">
                <ul>
                    <li>
                        <a href="index.php" class="<?php echo $active_page == 'dashboard' ? 'active' : ''; ?>">
                            <i class="fas fa-tachometer-alt"></i> <span>Dashboard</span>
                        </a>
                    </li>
                    <li>
                        <a href="products.php" class="<?php echo $active_page == 'products' ? 'active' : ''; ?>">
                            <i class="fas fa-box"></i> <span>Products</span>
                        </a>
                    </li>
                    <li>
                        <a href="categories.php" class="<?php echo $active_page == 'categories' ? 'active' : ''; ?>">
                            <i class="fas fa-tags"></i> <span>Categories</span>
                        </a>
                    </li>
                    <li>
                        <a href="sales.php" class="<?php echo $active_page == 'sales' ? 'active' : ''; ?>">
                            <i class="fas fa-shopping-cart"></i> <span>Sales</span>
                        </a>
                    </li>
                    <li>
                        <a href="customers.php" class="<?php echo $active_page == 'customers' ? 'active' : ''; ?>">
                            <i class="fas fa-users"></i> <span>Customers</span>
                        </a>
                    </li>
                    <li>
                        <a href="purchases.php" class="<?php echo $active_page == 'purchases' ? 'active' : ''; ?>">
                            <i class="fas fa-truck"></i> <span>Purchases</span>
                        </a>
                    </li>
                    <li>
                        <a href="suppliers.php" class="<?php echo $active_page == 'suppliers' ? 'active' : ''; ?>">
                            <i class="fas fa-industry"></i> <span>Suppliers</span>
                        </a>
                    </li>
                    <?php if(isAdmin()): ?>
                    <li>
                        <a href="users.php" class="<?php echo $active_page == 'users' ? 'active' : ''; ?>">
                            <i class="fas fa-user-cog"></i> <span>Users</span>
                        </a>
                    </li>
                    <li>
                        <a href="reports.php" class="<?php echo $active_page == 'reports' ? 'active' : ''; ?>">
                            <i class="fas fa-chart-bar"></i> <span>Reports</span>
                        </a>
                    </li>
                    <?php endif; ?>
                </ul>
            </div>
        </div>
        
        <!-- Content area -->
        <div class="content">
            <!-- Top navigation -->
            <div class="top-nav">
                <div class="page-title">
                    <h2><?php echo $page_title ?? 'Dashboard'; ?></h2>
                </div>
                <div class="user-info">
                    <span class="user-name"><?php echo $_SESSION['full_name']; ?> (<?php echo ucfirst($_SESSION['role']); ?>)</span>
                    <a href="?logout" class="btn btn-danger">Logout</a>
                </div>
            </div>
            
            <!-- Main content -->
            <div class="main-content">
                <?php echo showMessage(); ?>
                
                <!-- Content goes here -->
                <?php include $content_file; ?>
            </div>
        </div>
    </div>
    
    <script src="assets/js/script.js"></script>
</body>
</html>

```

Now, let's create the dashboard page:

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

Let's create the dashboard view:

```php
<div class="dashboard-stats">
    <div class="stat-card">
        <h3>Total Products</h3>
        <div class="stat-value"><?php echo $total_products; ?></div>
    </div>
    <div class="stat-card">
        <h3>Low Stock</h3>
        <div class="stat-value"><?php echo $low_stock; ?></div>
    </div>
    <div class="stat-card">
        <h3>Today's Sales</h3>
        <div class="stat-value">$<?php echo number_format($today_sales, 2); ?></div>
    </div>
    <div class="stat-card">
        <h3>Total Customers</h3>
        <div class="stat-value"><?php echo $total_customers; ?></div>
    </div>
</div>

<div class="row">
    <div class="card">
        <div class="card-header">
            <h3>Recent Sales</h3>
            <a href="sales.php" class="btn btn-primary">View All</a>
        </div>
        <div class="card-body">
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Date</th>
                            <th>Customer</th>
                            <th>Amount</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php if(empty($recent_sales)): ?>
                            <tr>
                                <td colspan="4">No sales found</td>
                            </tr>
                        <?php else: ?>
                            <?php foreach($recent_sales as $sale): ?>
                                <tr>
                                    <td><?php echo $sale['sale_id']; ?></td>
                                    <td><?php echo date('M d, Y H:i', strtotime($sale['sale_date'])); ?></td>
                                    <td><?php echo $sale['customer_name'] ?? 'Walk-in Customer'; ?></td>
                                    <td>$<?php echo number_format($sale['total_amount'], 2); ?></td>
                                </tr>
                            <?php endforeach; ?>
                        <?php endif; ?>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>

<div class="row mt-2">
    <div class="card">
        <div class="card-header">
            <h3>Top Selling Products</h3>
            <a href="products.php" class="btn btn-primary">View All Products</a>
        </div>
        <div class="card-body">
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>Product Name</th>
                            <th>Total Sold</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php if(empty($top_products)): ?>
                            <tr>
                                <td colspan="2">No data available</td>
                            </tr>
                        <?php else: ?>
                            <?php foreach($top_products as $product): ?>
                                <tr>
                                    <td><?php echo $product['product_name']; ?></td>
                                    <td><?php echo $product['total_sold']; ?></td>
                                </tr>
                            <?php endforeach; ?>
                        <?php endif; ?>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>

```

## Point of Sale (POS) System

Now, let's create the Point of Sale (POS) system. First, we'll create the main POS page:

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

Now, let's create the POS view:

```php
<div class="pos-container">
    <div class="product-section">
        <div class="card">
            <div class="card-header">
                <h3>Products</h3>
                <div class="search-filter">
                    <input type="text" id="productSearch" placeholder="Search products..." class="search-input">
                    <select id="categoryFilter" class="category-filter">
                        <option value="all">All Categories</option>
                        <?php foreach($categories as $category): ?>
                            <option value="<?php echo $category['category_id']; ?>"><?php echo $category['category_name']; ?></option>
                        <?php endforeach; ?>
                    </select>
                </div>
            </div>
            <div class="card-body">
                <div class="products-grid" id="productsGrid">
                    <?php foreach($products as $product): ?>
                        <div class="product-card" data-id="<?php echo $product['product_id']; ?>" 
                             data-name="<?php echo $product['product_name']; ?>"
                             data-price="<?php echo $product['price']; ?>"
                             data-stock="<?php echo $product['quantity']; ?>">
                            <div class="product-name"><?php echo $product['product_name']; ?></div>
                            <div class="product-price">$<?php echo number_format($product['price'], 2); ?></div>
                            <div class="product-stock"><?php echo $product['quantity']; ?> in stock</div>
                        </div>
                    <?php endforeach; ?>
                </div>
            </div>
        </div>
    </div>
    
    <div class="cart-section">
        <div class="card">
            <div class="card-header">
                <h3>Current Sale</h3>
                <button type="button" id="clearCartBtn" class="btn btn-warning">Clear</button>
            </div>
            <div class="card-body">
                <form method="POST" action="" id="posForm">
                    <div class="cart-items">
                        <table id="cartTable">
                            <thead>
                                <tr>
                                    <th>Product</th>
                                    <th>Price</th>
                                    <th>Qty</th>
                                    <th>Subtotal</th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody id="cartItems">
                                <!-- Cart items will be added here via JavaScript -->
                            </tbody>
                            <tfoot>
                                <tr>
                                    <td colspan="3" class="text-right"><strong>Total:</strong></td>
                                    <td><span id="cartTotal">$0.00</span></td>
                                    <td></td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>
                    
                    <div class="customer-payment mt-2">
                        <div class="input-group">
                            <label for="customer_id">Customer (Optional)</label>
                            <select name="customer_id" id="customer_id">
                                <option value="">Walk-in Customer</option>
                                <?php foreach($customers as $customer): ?>
                                    <option value="<?php echo $customer['customer_id']; ?>"><?php echo $customer['customer_name']; ?></option>
                                <?php endforeach; ?>
                            </select>
                        </div>
                        
                        <div class="input-group">
                            <label for="payment_method">Payment Method</label>
                            <select name="payment_method" id="payment_method" required>
                                <option value="cash">Cash</option>
                                <option value="credit_card">Credit Card</option>
                                <option value="debit_card">Debit Card</option>
                                <option value="online">Online Payment</option>
                            </select>
                        </div>
                        
                        <div class="payment-calc">
                            <div class="input-group">
                                <label for="amount_paid">Amount Paid</label>
                                <input type="number" id="amount_paid" min="0" step="0.01" class="amount-input">
                            </div>
                            <div class="input-group">
                                <label for="change_amount">Change</label>
                                <input type="text" id="change_amount" readonly class="change-input">
                            </div>
                        </div>
                    </div>
                    
                    <div class="checkout-buttons mt-2">
                        <button type="submit" name="process_sale" id="checkoutBtn" class="btn btn-success btn-lg" disabled>Process Sale</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>

<style>
    .pos-container {
        display: flex;
        gap: 20px;
    }
    
    .product-section {
        flex: 2;
    }
    
    .cart-section {
        flex: 1;
    }
    
    .search-filter {
        display: flex;
        gap: 10px;
    }
    
    .search-input {
        flex: 2;
    }
    
    .category-filter {
        flex: 1;
    }
    
    .products-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
        gap: 15px;
        max-height: 70vh;
        overflow-y: auto;
        padding: 10px;
    }
    
    .product-card {
        background-color: white;
        border: 1px solid #ddd;
        border-radius: 5px;
        padding: 15px;
        cursor: pointer;
        transition: all 0.3s;
    }
    
    .product-card:hover {
        border-color: #6e8efb;
        box-shadow: 0 3px 8px rgba(0,0,0,0.1);
    }
    
    .product-name {
        font-weight: bold;
        margin-bottom: 5px;
    }
    
    .product-price {
        color: #28a745;
        font-size: 16px;
        margin-bottom: 5px;
    }
    
    .product-stock {
        font-size: 12px;
        color: #666;
    }
    
    .cart-items {
        max-height: 40vh;
        overflow-y: auto;
    }
    
    .payment-calc {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 10px;
    }
    
    .checkout-buttons {
        text-align: center;
    }
    
    .qty-input {
        width: 60px;
        text-align: center;
    }
    
    @media (max-width: 992px) {
        .pos-container {
            flex-direction: column;
        }
    }
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
    const productsGrid = document.getElementById('productsGrid');
    const cartItems = document.getElementById('cartItems');
    const cartTotal = document.getElementById('cartTotal');
    const checkoutBtn = document.getElementById('checkoutBtn');
    const clearCartBtn = document.getElementById('clearCartBtn');
    const productSearch = document.getElementById('productSearch');
    const categoryFilter = document.getElementById('categoryFilter');
    const amountPaid = document.getElementById('amount_paid');
    const changeAmount = document.getElementById('change_amount');
    
    let cart = [];
    
    // Add product to cart
    productsGrid.addEventListener('click', function(e) {
        const productCard = e.target.closest('.product-card');
        if (productCard) {
            const productId = productCard.getAttribute('data-id');
            const productName = productCard.getAttribute('data-name');
            const productPrice = parseFloat(productCard.getAttribute('data-price'));
            const productStock = parseInt(productCard.getAttribute('data-stock'));
            
            // Check if product already in cart
            const existingItem = cart.find(item => item.id === productId);
            
            if (existingItem) {
                // Increase quantity if not exceeding stock
                if (existingItem.quantity < productStock) {
                    existingItem.quantity++;
                    existingItem.subtotal = existingItem.price * existingItem.quantity;
                } else {
                    alert(`Cannot add more. Only ${productStock} available in stock.`);
                }
            } else {
                // Add new item to cart
                cart.push({
                    id: productId,
                    name: productName,
                    price: productPrice,
                    quantity: 1,
                    stock: productStock,
                    subtotal: productPrice
                });
            }
            
            updateCart();
        }
    });
    
    // Update cart when quantity changes
    cartItems.addEventListener('input', function(e) {
        if (e.target.classList.contains('qty-input')) {
            const row = e.target.closest('tr');
            const productId = row.getAttribute('data-id');
            const quantity = parseInt(e.target.value) || 0;
            
            const cartItem = cart.find(item => item.id === productId);
            if (cartItem) {
                // Validate quantity against stock
                if (quantity > cartItem.stock) {
                    e.target.value = cartItem.stock;
                    cartItem.quantity = cartItem.stock;
                    alert(`Cannot add more than ${cartItem.stock} items (max stock).`);
                } else if (quantity <= 0) {
                    // Remove item if quantity is 0 or negative
                    cart = cart.filter(item => item.id !== productId);
                    row.remove();
                } else {
                    cartItem.quantity = quantity;
                }
                
                cartItem.subtotal = cartItem.price * cartItem.quantity;
                updateCart();
            }
        }
    });
    
    // Remove item from cart
    cartItems.addEventListener('click', function(e) {
        if (e.target.classList.contains('remove-item')) {
            const row = e.target.closest('tr');
            const productId = row.getAttribute('data-id');
            
            cart = cart.filter(item => item.id !== productId);
            updateCart();
        }
    });
    
    // Clear cart
    clearCartBtn.addEventListener('click', function() {
        if (cart.length > 0) {
            if (confirm('Are you sure you want to clear the cart?')) {
```