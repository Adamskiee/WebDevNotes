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