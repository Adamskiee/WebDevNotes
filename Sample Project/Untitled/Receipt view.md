```php
<div class="receipt-container">
    <div class="card">
        <div class="card-header">
            <h3>Sales Receipt</h3>
            <div class="receipt-actions">
                <button onclick="window.print();" class="btn btn-primary">Print Receipt</button>
                <a href="pos.php" class="btn btn-secondary">New Sale</a>
            </div>
        </div>
        <div class="card-body" id="printable-receipt">
            <div class="receipt-header">
                <h2>Store Management System</h2>
                <p>123 Main Street, Your City</p>
                <p>Phone: (123) 456-7890</p>
                <p>Email: contact@store.com</p>
            </div>
            
            <div class="receipt-details">
                <div class="receipt-info">
                    <p><strong>Receipt #:</strong> <?php echo $sale['sale_id']; ?></p>
                    <p><strong>Date:</strong> <?php echo date('M d, Y H:i', strtotime($sale['sale_date'])); ?></p>
                    <p><strong>Cashier:</strong> <?php echo $sale['username']; ?></p>
                    <p><strong>Payment Method:</strong> <?php echo ucfirst(str_replace('_', ' ', $sale['payment_method'])); ?></p>
                </div>
                
                <?php if(!empty($sale['customer_name'])): ?>
                <div class="customer-info">
                    <h4>Customer Information</h4>
                    <p><strong>Name:</strong> <?php echo $sale['customer_name']; ?></p>
                    <?php if(!empty($sale['phone'])): ?>
                    <p><strong>Phone:</strong> <?php echo $sale['phone']; ?></p>
                    <?php endif; ?>
                    <?php if(!empty($sale['email'])): ?>
                    <p><strong>Email:</strong> <?php echo $sale['email']; ?></p>
                    <?php endif; ?>
                </div>
                <?php endif; ?>
            </div>
            
            <div class="receipt-items">
                <h4>Items Purchased</h4>
                <table>
                    <thead>
                        <tr>
                            <th>Item</th>
                            <th>Price</th>
                            <th>Qty</th>
                            <th>Subtotal</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php foreach($sale_items as $item): ?>
                            <tr>
                                <td><?php echo $item['product_name']; ?></td>
                                <td>$<?php echo number_format($item['price_each'], 2); ?></td>
                                <td><?php echo $item['quantity']; ?></td>
                                <td>$<?php echo number_format($item['subtotal'], 2); ?></td>
                            </tr>
                        <?php endforeach; ?>
                    </tbody>
                    <tfoot>
                        <tr>
                            <td colspan="3" class="text-right"><strong>Total:</strong></td>
                            <td>$<?php echo number_format($sale['total_amount'], 2); ?></td>
                        </tr>
                    </tfoot>
                </table>
            </div>
            
            <div class="receipt-footer">
                <p>Thank you for your business!</p>
                <p>Return Policy: Items can be returned within 14 days with receipt.</p>
            </div>
        </div>
    </div>
</div>

<style>
    .receipt-container {
        max-width: 800px;
        margin: 0 auto;
    }
    
    .receipt-actions {
        display: flex;
        gap: 10px;
    }
    
    .receipt-header {
        text-align: center;
        margin-bottom: 20px;
        padding-bottom: 10px;
        border-bottom: 1px solid #ddd;
    }
    
    .receipt-header h2 {
        margin-top: 0;
        margin-bottom: 5px;
    }
    
    .receipt-header p {
        margin: 5px 0;
    }
    
    .receipt-details {
        display: flex;
        justify-content: space-between;
        margin-bottom: 20px;
    }
    
    .receipt-info, .customer-info {
        flex: 1;
    }
    
    .receipt-items {
        margin-bottom: 20px;
    }
    
    .receipt-footer {
        text-align: center;
        margin-top: 30px;
        padding-top: 10px;
        border-top: 1px solid #ddd;
    }
    
    @media print {
        .card-header, .receipt-actions, .navbar, .sidebar {
            display: none !important;
        }
        
        body, html {
            width: 100%;
            margin: 0;
            padding: 0;
        }
        
        .card {
            border: none !important;
            box-shadow: none !important;
        }
        
        .receipt-header {
            text-align: center;
        }
    }
</style>
```