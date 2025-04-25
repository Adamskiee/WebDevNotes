```php
<div class="card">
    <div class="card-header">
        <h3>Create New Purchase Order</h3>
        <a href="purchases.php" class="btn btn-secondary">Back to Purchases</a>
    </div>
    <div class="card-body">
        <form method="POST" action="" id="purchaseForm">
            <div class="input-group">
                <label for="supplier_id">Supplier</label>
                <select name="supplier_id" id="supplier_id" required>
                    <option value="">Select Supplier</option>
                    <?php foreach($suppliers as $supplier): ?>
                        <option value="<?php echo $supplier['supplier_id']; ?>"><?php echo $supplier['supplier_name']; ?></option>
                    <?php endforeach; ?>
                </select>
            </div>
            
            <div class="card mt-2">
                <div class="card-header">
                    <h4>Purchase Items</h4>
                    <button type="button" id="addItemBtn" class="btn btn-primary">Add Item</button>
                </div>
                <div class="card-body">
                    <div class="table-container">
                        <table id="purchaseItemsTable">
                            <thead>
                                <tr>
                                    <th>Product</th>
                                    <th>Price</th>
                                    <th>Quantity</th>
                                    <th>Subtotal</th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr class="item-row">
                                    <td>
                                        <select name="product_id[]" class="product-select" required>
                                            <option value="">Select Product</option>
                                            <?php foreach($products as $product): ?>
                                                <option value="<?php echo $product['product_id']; ?>" 
                                                        data-price="<?php echo $product['cost_price']; ?>">
                                                    <?php echo $product['product_name']; ?>
                                                </option>
                                            <?php endforeach; ?>
                                        </select>
                                    </td>
                                    <td>
                                        <input type="number" name="price[]" class="price-input" step="0.01" min="0.01" required>
                                    </td>
                                    <td>
                                        <input type="number" name="quantity[]" class="quantity-input" min="1" value="1" required>
                                    </td>
                                    <td>
                                        <span class="subtotal">0.00</span>
                                    </td>
                                    <td>
                                        <button type="button" class="btn btn-danger btn-sm remove-item">Remove</button>
                                    </td>
                                </tr>
                            </tbody>
                            <tfoot>
                                <tr>
                                    <td colspan="3" class="text-right"><strong>Total:</strong></td>
                                    <td><span id="total">0.00</span></td>
                                    <td></td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>
                </div>
            </div>
            
            <div class="mt-2">
                <button type="submit" class="btn btn-success">Create Purchase Order</button>
            </div>
        </form>
    </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
    // Add new item row
    document.getElementById('addItemBtn').addEventListener('click', function() {
        const tbody = document.querySelector('#purchaseItemsTable tbody');
        const firstRow = document.querySelector('.item-row');
        const newRow = firstRow.cloneNode(true);
        
        // Clear values
        newRow.querySelector('.product-select').value = '';
        newRow.querySelector('.price-input').value = '';
        newRow.querySelector('.quantity-input').value = '1';
        newRow.querySelector('.subtotal').textContent = '0.00';
        
        // Add event listeners to new row
        addRowEventListeners(newRow);
        
        tbody.appendChild(newRow);
    });
    
    // Add event listeners to existing row
    const firstRow = document.querySelector('.item-row');
    addRowEventListeners(firstRow);
    
    function addRowEventListeners(row) {
        // Product selection change
        const productSelect = row.querySelector('.product-select');
        productSelect.addEventListener('change', function() {
            const selectedOption = this.options[this.selectedIndex];
            const price = selectedOption.getAttribute('data-price');
            const priceInput = row.querySelector('.price-input');
            
            if (price) {
                priceInput.value = price;
                updateSubtotal(row);
            } else {
                priceInput.value = '';
            }
        });
        
        // Price or quantity change
        const priceInput = row.querySelector('.price-input');
        const quantityInput = row.querySelector('.quantity-input');
        
        priceInput.addEventListener('input', function() {
            updateSubtotal(row);
        });
        
        quantityInput.addEventListener('input', function() {
            updateSubtotal(row);
        });
        
        // Remove item
        const removeBtn = row.querySelector('.remove-item');
        removeBtn.addEventListener('click', function() {
            // Don't remove if it's the only row
            const rows = document.querySelectorAll('.item-row');
            if (rows.length > 1) {
                row.remove();
                updateTotal();
            }
        });
    }
    
    function updateSubtotal(row) {
        const price = parseFloat(row.querySelector('.price-input').value) || 0;
        const quantity = parseInt(row.querySelector('.quantity-input').value) || 0;
        const subtotal = price * quantity;
        
        row.querySelector('.subtotal').textContent = subtotal.toFixed(2);
        updateTotal();
    }
    
    function updateTotal() {
        const subtotals = document.querySelectorAll('.subtotal');
        let total = 0;
        
        subtotals.forEach(function(element) {
            total += parseFloat(element.textContent) || 0;
        });
        
        document.getElementById('total').textContent = total.toFixed(2);
    }
});
</script>
```