```php
<!-- This code would be added to your layout.php or navigation section -->
<div class="sidebar">
    <div class="logo">Store Management System</div>
    <ul class="nav-links">
        <li class="<?php echo $active_page == 'dashboard' ? 'active' : ''; ?>">
            <a href="index.php">
                <i class="fas fa-tachometer-alt"></i>
                <span>Dashboard</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'products' ? 'active' : ''; ?>">
            <a href="products.php">
                <i class="fas fa-box"></i>
                <span>Products</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'categories' ? 'active' : ''; ?>">
            <a href="categories.php">
                <i class="fas fa-tags"></i>
                <span>Categories</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'customers' ? 'active' : ''; ?>">
            <a href="customers.php">
                <i class="fas fa-users"></i>
                <span>Customers</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'suppliers' ? 'active' : ''; ?>">
            <a href="suppliers.php">
                <i class="fas fa-truck"></i>
                <span>Suppliers</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'purchases' ? 'active' : ''; ?>">
            <a href="purchases.php">
                <i class="fas fa-shopping-cart"></i>
                <span>Purchases</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'pos' ? 'active' : ''; ?>">
            <a href="pos.php">
                <i class="fas fa-cash-register"></i>
                <span>Point of Sale</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'sales' ? 'active' : ''; ?>">
            <a href="sales.php">
                <i class="fas fa-chart-line"></i>
                <span>Sales Report</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'users' ? 'active' : ''; ?>">
            <a href="users.php">
                <i class="fas fa-user-shield"></i>
                <span>Users</span>
            </a>
        </li>
        <li class="<?php echo $active_page == 'settings' ? 'active' : ''; ?>">
            <a href="settings.php">
                <i class="fas fa-cog"></i>
                <span>Settings</span>
            </a>
        </li>
    </ul>
    <div class="logout">
        <a href="logout.php">
            <i class="fas fa-sign-out-alt"></i>
            <span>Logout</span>
        </a>
    </div>
</div>
```