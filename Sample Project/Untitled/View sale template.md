```php
<div class="card">
    <div class="card-header">
        <h3>Sale #<?php echo $sale['sale_id']; ?> Details</h3>
        <a href="sales.php" class="btn btn-secondary">Back to Sales</a>
    </div>
    <div class="card-body">
        <div class="row">
            <div class="col-md-6">
                <h4>Sale Information</h4>
                <table class="table-info">
                    <tr>
                        <th>Sale ID:</th>
                        <td><?php echo $sale['sale_id']; ?></td>
                    </tr>
                    <tr>
                        <th>Date:</th>
                        <td><?php echo date('F d, Y H:i', strtotime($sale['sale_date'])); ?></td>
                    </tr>
                    <tr>
                        <th>Payment Method:</th>
                        <td><?php echo ucfirst(str_replace('_', ' ', $sale['payment_method'])); ?></td>
                    </tr>
                    <tr>
                        <th>Total Amount:</th>
                        <td>$<?php echo number_format($sale['total_amount'], 2); ?></td>
                    </tr>
                    <tr>
                        <th>Processed By:</th>
                        <td><?php echo $sale['username']; ?></td>
                    </tr>
                </table>
            </div>
            
            <div class="col-md-6">
                <h4>Customer Information</h4>
                <?php if($sale['customer_id']): ?>
                    <table class="table-info">
                        <tr>
                            <th>Name:</th>
                            <td><?php echo $sale['customer_name']; ?></td>
                        </tr>
                        <?php if($sale['phone']): ?>
                        <tr>
                            <th>Phone:</th>
                            <td><?php echo $sale['phone']; ?></td>
                        </tr>
                        <?php endif; ?>
                        <?php if($sale['email']): ?>
                        <tr>
                            <th>Email:</th>
                            <td><?php echo $sale['email']; ?></td>
                        </tr>
                        <?php endif; ?>
                        <?php if($sale['address']): ?>
                        <tr>
                            <th>Address:</th>
                            <td><?php echo $sale['address']; ?></td>
                        </tr>
                        <?php endif; ?>
                    </table>
                <?php else: ?>
                    <p>Walk-in Customer</p>
                <?php endif; ?>
            </div>
        </div>
        
        <h4 class="mt-4">Sale Items</h4>
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>#</th>
                        <th>Product</th>
                        <th>Price</th>
                        <th>Quantity</th>
                        <th>Subtotal</th>
                    </tr>
                </thead>
                <tbody>
                    <?php $counter = 1; ?>
                    <?php foreach($sale_items as $item): ?>
                        <tr>
                            <td><?php echo $counter++; ?></td>
                            <td><?php echo $item['product_name']; ?></td>
                            <td>$<?php echo number_format($item['price_each'], 2); ?></td>
                            <td><?php echo $item['quantity']; ?></td>
                            <td>$<?php echo number_format($item['subtotal'], 2); ?></td>
                        </tr>
                    <?php endforeach; ?>
                </tbody>
                <tfoot>
                    <tr>
                        <td colspan="4" class="text-right"><strong>Total:</strong></td>
                        <td><strong>$<?php echo number_format($sale['total_amount'], 2); ?></strong></td>
                    </tr>
                </tfoot>
            </table>
        </div>
        
        <div class="mt-4">
            <button onclick="window.print()" class="btn btn-primary">Print Receipt</button>
            <?php if(isAdmin()): ?>
            <form method="POST" action="sales.php" style="display: inline;">
                <input type="hidden" name="sale_id" value="<?php echo $sale['sale_id']; ?>">
                <button type="submit" name="delete_sale" class="btn btn-danger" onclick="return confirm('Are you sure you want to delete this sale? This will restore all inventory items.')">Delete Sale</button>
            </form>
            <?php endif; ?>
        </div>
    </div>
</div>

<style>
    @media print {
        .sidebar, .top-nav, .btn, form {
            display: none !important;
        }
        .card {
            box-shadow: none !important;
            border: none !important;
        }
        body {
            background-color: white !important;
        }
    }
    
    .table-info {
        width: 100%;
        margin-bottom: 20px;
    }
    
    .table-info th {
        width: 35%;
        text-align: left;
        padding: 8px;
        vertical-align: top;
    }
    
    .table-info td {
        padding: 8px;
    }
</style>
```