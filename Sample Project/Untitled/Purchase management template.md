```php
<div class="card">
    <div class="card-header">
        <h3>Purchase Orders</h3>
        <a href="new_purchase.php" class="btn btn-success">New Purchase</a>
    </div>
    <div class="card-body">
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Date</th>
                        <th>Supplier</th>
                        <th>Amount</th>
                        <th>Status</th>
                        <th>Created By</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <?php if(empty($purchases)): ?>
                        <tr>
                            <td colspan="7">No purchases found</td>
                        </tr>
                    <?php else: ?>
                        <?php foreach($purchases as $purchase): ?>
                            <tr>
                                <td><?php echo $purchase['purchase_id']; ?></td>
                                <td><?php echo date('M d, Y H:i', strtotime($purchase['purchase_date'])); ?></td>
                                <td><?php echo $purchase['supplier_name']; ?></td>
                                <td>$<?php echo number_format($purchase['total_amount'], 2); ?></td>
                                <td>
                                    <span class="badge <?php 
                                        echo $purchase['status'] == 'pending' ? 'badge-warning' : 
                                             ($purchase['status'] == 'received' ? 'badge-success' : 'badge-danger'); 
                                    ?>">
                                        <?php echo ucfirst($purchase['status']); ?>
                                    </span>
                                </td>
                                <td><?php echo $purchase['username']; ?></td>
                                <td>
                                    <a href="view_purchase.php?id=<?php echo $purchase['purchase_id']; ?>" class="btn btn-primary btn-sm">View</a>
                                    
                                    <?php if($purchase['status'] != 'cancelled'): ?>
                                    <form method="POST" action="" style="display: inline;">
                                        <input type="hidden" name="purchase_id" value="<?php echo $purchase['purchase_id']; ?>">
                                        <input type="hidden" name="old_status" value="<?php echo $purchase['status']; ?>">
                                        <select name="status" class="status-select">
                                            <option value="pending" <?php echo $purchase['status'] == 'pending' ? 'selected' : ''; ?>>Pending</option>
                                            <option value="received" <?php echo $purchase['status'] == 'received' ? 'selected' : ''; ?>>Received</option>
                                            <option value="cancelled" <?php echo $purchase['status'] == 'cancelled' ? 'selected' : ''; ?>>Cancelled</option>
                                        </select>
                                        <button type="submit" name="update_status" class="btn btn-secondary btn-sm">Update</button>
                                    </form>
                                    <?php endif; ?>
                                    
                                    <?php if(isAdmin()): ?>
                                    <form method="POST" action="" style="display: inline;">
                                        <input type="hidden" name="purchase_id" value="<?php echo $purchase['purchase_id']; ?>">
                                        <button type="submit" name="delete_purchase" class="btn btn-danger btn-sm" onclick="return confirm('Are you sure you want to delete this purchase?')">Delete</button>
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

<style>
    .badge {
        padding: 5px 10px;
        border-radius: 4px;
        font-size: 12px;
        color: white;
    }
    
    .badge-success {
        background-color: #28a745;
    }
    
    .badge-warning {
        background-color: #ffc107;
        color: #212529;
    }
    
    .badge-danger {
        background-color: #dc3545;
    }
    
    .status-select {
        padding: 2px 5px;
        margin-right: 5px;
    }
</style>

<script>
    // Auto-submit status update form when the status is changed
    document.addEventListener('DOMContentLoaded', function() {
        const statusSelects = document.querySelectorAll('.status-select');
        statusSelects.forEach(select => {
            const originalValue = select.value;
            select.addEventListener('change', function() {
                const newValue = this.value;
                if (newValue !== originalValue) {
                    if (confirm('Are you sure you want to update the status to ' + this.options[this.selectedIndex].text + '?')) {
                        this.closest('form').submit();
                    } else {
                        this.value = originalValue;
                    }
                }
            });
        });
    });
</script>
```

