```php
<div class="card">
    <div class="card-header">
        <h3>Sales List</h3>
        <a href="new_sale.php" class="btn btn-success">New Sale</a>
    </div>
    <div class="card-body">
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>Sale ID</th>
                        <th>Date</th>
                        <th>Customer</th>
                        <th>Amount</th>
                        <th>Payment</th>
                        <th>Processed By</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <?php if(empty($sales)): ?>
                        <tr>
                            <td colspan="7">No sales found</td>
                        </tr>
                    <?php else: ?>
                        <?php foreach($sales as $sale): ?>
                            <tr>
                                <td><?php echo $sale['sale_id']; ?></td>
                                <td><?php echo date('M d, Y H:i', strtotime($sale['sale_date'])); ?></td>
                                <td><?php echo $sale['customer_name'] ?? 'Walk-in Customer'; ?></td>
                                <td>$<?php echo number_format($sale['total_amount'], 2); ?></td>
                                <td><?php echo ucfirst(str_replace('_', ' ', $sale['payment_method'])); ?></td>
                                <td><?php echo $sale['username']; ?></td>
                                <td>
                                    <a href="view_sale.php?id=<?php echo $sale['sale_id']; ?>" class="btn btn-primary btn-sm">View</a>
                                    <?php if(isAdmin()): ?>
                                    <form method="POST" action="" style="display: inline;">
                                        <input type="hidden" name="sale_id" value="<?php echo $sale['sale_id']; ?>">
                                        <button type="submit" name="delete_sale" class="btn btn-danger btn-sm" onclick="return confirm('Are you sure you want to delete this sale? This will restore all inventory items.')">Delete</button>
                                    </form>
                                    <?php endif; ?>
                                </td>
                            </tr>
                        <?php endforeach; ?>
                    <?php endif; ?>
                </tbody>
            </table>
        </div>
    </div>
</div>
```