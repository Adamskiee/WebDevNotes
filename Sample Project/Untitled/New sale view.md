```php
<div class="card">
    <div class="card-header">
        <h3>New Sale</h3>
        <a href="sales.php" class="btn btn-secondary">Back to Sales</a>
    </div>
    <div class="card-body">
        <form id="saleForm" method="POST" action="">
            <div class="form-grid">
                <div class="input-group">
                    <label for="customer_id">Customer (Optional)</label>
                    <select id="customer_id" name="customer_id">
                        <option value="">Walk-in Customer</option>
                        <?php foreach($customers as $customer): ?>
                            <option value="<?php echo $customer['customer_id']; ?>">
                                <?php echo $customer['customer_name']; ?>
                            </option>
                        <?php endforeach; ?>
                    </select>
                </div>
                
                <div class="input-group">
                    <label for="payment_method">Payment Method</label>
                    <select id="payment_method" name="payment_method" required>
                        <option value="cash">Cash</option>
                        <option value="credit_card">Credit Card</option>
                        <option value="debit_card">Debit Card</option>
                        <option value="online">Online Payment</option>
                    </select>
                </div>
            </div>
            
            <div class="card mt-2">
                <div class="card-header">
                    <h3>Sale Items</h3>
                    <button type="button" id="addItemBtn" class="btn btn-primary">Add Item</button>
                </div>
                <div class="card-body">
                    <div class="table-container">
                        <table id="saleItemsTable">
                            <thead>
                                <tr>
                                    <th>Product</th>
                                    <th>Price</th>
                                    <th>Quantity</th>
                                    <th>Subtotal</th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody id="saleItemsBody">
                                <!-- Sale items will be added here by JavaScript -->
                            </tbody>
                            <tfoot>
                                <tr>
                                    <td colspan="3" class="text-right"><strong>Total:</strong></td>
                                    <td>$<span id="totalAmount">0.00</span></td>
                                    <td></td>
                                </tr>
                            </tfoot>
                        </table>
                        <input type="hidden" name="total_amount" id="total_amount" value="0">
                    </div>
                </div>
            </div>
            
            <div class="text-right mt-2">
                <button type="submit" name="complete_sale" id="completeSaleBtn" class="btn btn-success" disabled>Complete Sale</button>
            </div>
        </form>
    </div>
</div>

<!-- Product selection modal -->
<div id="productModal" class="modal" style="display: none;">
    <div class="modal-content">
        <div class="modal-header">
            <h3>Select Product</h3>
            <span class="close">&times;</span>
        </div>
        <div class="modal-body">
            <input type="text" id="productSearch" placeholder="Search products..." class="form-control">
            <div class="table-container mt-2">
                <table id="productsTable">
                    <thead>
                        <tr>
                            <th>Name</th>
                            <th>Category</th>
                            <th>Price</th>
                            <th>In Stock</th>
                            <th>Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <?php foreach($available_products as $product): ?>
                            <tr>
                                <td><?php echo $product['product_name']; ?></td>
                                <td><?php echo $product['category_name'] ?? 'Uncategorized'; ?></td>
                                <td>$<?php echo number_format($product['price'], 2); ?></td>
                                <td><?php echo $product['quantity']; ?></td>
                                <td>
                                    <button type="button" class="btn btn-primary select-product" 
                                            data-id="<?php echo $product['product_id']; ?>"
                                            data-name="<?php echo $product['product_name']; ?>"
                                            data-price="<?php echo $product['price']; ?>"
                                            data-stock="<?php echo $product['quantity']; ?>">
                                        Select
                                    </button>
                                </td>
                            </tr>
                        <?php endforeach; ?>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>
```