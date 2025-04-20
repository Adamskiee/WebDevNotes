```php
<div class="card">
    <div class="card-header">
        <h3>Sale #<?php echo $sale_id; ?> Details</h3>
        <div>
            <a href="sales.php" class="btn btn-secondary">Back to Sales</a>
            <button onclick="printInvoice()" class="btn btn-primary"><i class="fas fa-print"></i> Print Invoice</button>
        </div>
    </div>
    
    <div class="card-body" id="invoice-section">
        <div class="invoice-header">
            <div class="invoice-title">
                <h2>Invoice</h2>
                <p>Sale #<?php echo $sale_id; ?></p>
            </div>
            <div class="invoice-info">
                <p><strong>Date:</strong> <?php echo date('M d, Y H:i', strtotime($sale['sale_date'])); ?></p>
                <p><strong>Payment Method:</strong> <?php echo ucfirst(str_replace('_', ' ', $sale['payment_method'])); ?></p>
                <p><strong>Sales Person:</strong> <?php echo $sale['username']; ?></p>
            </div>
        </div>
        
        <div class="customer-info">
            <h4>Customer Information</h4>
            <div class="info-box">
                <?php if ($sale['customer_id']): ?>
                    <p><strong>Name:</strong> <?php echo $sale['customer_name']; ?></p>
                    <p><strong>Phone:</strong> <?php echo $sale['phone'] ?? 'N/A'; ?></p>
                    <p><strong>Email:</strong> <?php echo $sale['email'] ?? 'N/A'; ?></p>
                    <p><strong>Address:</strong> <?php echo $sale['address'] ?? 'N/A'; ?></p>
                <?php else: ?>
                    <p>Walk-in Customer</p>
                <?php endif; ?>
            </div>
        </div>
        
        <div class="items-info mt-2">
            <h4>Purchased Items</h4>
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>Product</th>
                            <th>Quantity</th>
                            <th>Price Each</th>
                            <th>Subtotal</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php 
                        $counter = 1;
                        foreach ($sale_items as $item): 
                        ?>
                            <tr>
                                <td><?php echo $counter++; ?></td>
                                <td><?php echo $item['product_name']; ?></td>
                                <td><?php echo $item['quantity']; ?></td>
                                <td>$<?php echo number_format($item['price_each'], 2); ?></td>
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
        </div>
    </div>
</div>

<script>
function printInvoice() {
    // Create a new window for printing
    const printWindow = window.open('', '_blank');
    
    // Get the invoice content
    const invoiceContent = document.getElementById('invoice-section').innerHTML;
    
    // Create HTML for print
    printWindow.document.write(`
        <!DOCTYPE html>
        <html>
        <head>
            <title>Invoice #<?php echo $sale_id; ?></title>
            <style>
                body { font-family: Arial, sans-serif; }
                .invoice-header { display: flex; justify-content: space-between; margin-bottom: 20px; }
                .invoice-title h2 { margin-bottom: 5px; }
                .invoice-info p { margin: 5px 0; }
                .customer-info { margin-bottom: 20px; }
                .info-box { border: 1px solid #ddd; padding: 10px; border-radius: 4px; }
                table { width: 100%; border-collapse: collapse; }
                th, td { padding: 8px; text-align: left; border-bottom: 1px solid #ddd; }
                th { background-color: #f2f2f2; }
                .text-right { text-align: right; }
                tfoot tr td { font-weight: bold; }
                @media print {
                    .no-print { display: none; }
                }
            </style>
        </head>
        <body>
            <div class="no-print" style="margin-bottom: 20px;">
                <button onclick="window.print()">Print Invoice</button>
                <button onclick="window.close()">Close</button>
            </div>
            ${invoiceContent}
        </body>
        </html>
    `);
    
    // Focus the new window
    printWindow.document.close();
    printWindow.focus();
}
</script>
```