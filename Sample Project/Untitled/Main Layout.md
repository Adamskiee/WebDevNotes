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