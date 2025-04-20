```js
// Wait for DOM to be fully loaded
document.addEventListener('DOMContentLoaded', function() {
    // Handle product search in modal
    const productSearch = document.getElementById('productSearch');
    if (productSearch) {
        productSearch.addEventListener('keyup', function() {
            const searchTerm = this.value.toLowerCase();
            const rows = document.querySelectorAll('#productsTable tbody tr');
            
            rows.forEach(row => {
                const productName = row.cells[0].textContent.toLowerCase();
                const categoryName = row.cells[1].textContent.toLowerCase();
                
                if (productName.includes(searchTerm) || categoryName.includes(searchTerm)) {
                    row.style.display = '';
                } else {
                    row.style.display = 'none';
                }
            });
        });
    }
    
    // Modal handling for product selection
    const modal = document.getElementById('productModal');
    const addItemBtn = document.getElementById('addItemBtn');
    const closeBtn = document.querySelector('.close');
    
    if (addItemBtn && modal) {
        addItemBtn.addEventListener('click', function() {
            modal.style.display = 'block';
        });
    }
    
    if (closeBtn && modal) {
        closeBtn.addEventListener('click', function() {
            modal.style.display = 'none';
        });
    }
    
    // Close modal when clicking outside of it
    window.addEventListener('click', function(event) {
        if (event.target === modal) {
            modal.style.display = 'none';
        }
    });
    
    // Handle product selection from modal
    const selectProductButtons = document.querySelectorAll('.select-product');
    if (selectProductButtons) {
        selectProductButtons.forEach(button => {
            button.addEventListener('click', function() {
                const productId = this.dataset.id;
                const productName = this.dataset.name;
                const price = parseFloat(this.dataset.price);
                const maxStock = parseInt(this.dataset.stock);
                
                addProductToSale(productId, productName, price, maxStock);
                modal.style.display = 'none';
            });
        });
    }
    
    // Function to add product to sale table
    function addProductToSale(productId, productName, price, maxStock) {
        const tableBody = document.getElementById('saleItemsBody');
        const completeSaleBtn = document.getElementById('completeSaleBtn');
        
        // Check if product already exists in the table
        const existingRows = tableBody.querySelectorAll('tr');
        for (let i = 0; i < existingRows.length; i++) {
            const existingProductId = existingRows[i].querySelector('input[name="product_id[]"]').value;
            if (existingProductId === productId) {
                alert('This product is already in the sale. Please update the quantity instead.');
                return;
            }
        }
        
        // Create a new row
        const newRow = document.createElement('tr');
        
        // Calculate subtotal
        const quantity = 1;
        const subtotal = price * quantity;
        
        // Add row content
        newRow.innerHTML = `
            <td>
                ${productName}
                <input type="hidden" name="product_id[]" value="${productId}">
            </td>
            <td>
                $${price.toFixed(2)}
                <input type="hidden" name="price[]" value="${price}">
            </td>
            <td>
                <input type="number" name="quantity[]" value="1" min="1" max="${maxStock}" 
                       class="quantity-input" style="width: 60px;" required>
            </td>
            <td>
                $<span class="subtotal">${subtotal.toFixed(2)}</span>
                <input type="hidden" name="subtotal[]" value="${subtotal}">
            </td>
            <td>
                <button type="button" class="btn btn-danger btn-sm remove-item">Remove</button>
            </td>
        `;
        
        // Add the row to the table
        tableBody.appendChild(newRow);
        
        // Add event listener to quantity input
        const quantityInput = newRow.querySelector('.quantity-input');
        quantityInput.addEventListener('change', function() {
            updateSubtotal(this);
        });
        
        // Add event listener to remove button
        const removeButton = newRow.querySelector('.remove-item');
        removeButton.addEventListener('click', function() {
            tableBody.removeChild(newRow);
            updateTotal();
            checkItems();
        });
        
        // Update total and enable Complete Sale button
        updateTotal();
        completeSaleBtn.disabled = false;
    }
    
    // Function to update subtotal when quantity changes
    function updateSubtotal(quantityInput) {
        const row = quantityInput.closest('tr');
        const price = parseFloat(row.querySelector('input[name="price[]"]').value);
        const quantity = parseInt(quantityInput.value);
        const maxStock = parseInt(quantityInput.getAttribute('max'));
        
        // Validate quantity
        if (quantity < 1) {
            quantityInput.value = 1;
        } else if (quantity > maxStock) {
            quantityInput.value = maxStock;
            alert(`Only ${maxStock} in stock`);
        }
        
        // Recalculate subtotal
        const subtotal = price * parseInt(quantityInput.value);
        row.querySelector('.subtotal').textContent = subtotal.toFixed(2);
        row.querySelector('input[name="subtotal[]"]').value = subtotal;
        
        // Update total
        updateTotal();
    }
    
    // Function to update the total amount
    function updateTotal() {
        const subtotals = document.querySelectorAll('.subtotal');
        let total = 0;
        
        subtotals.forEach(element => {
            total += parseFloat(element.textContent);
        });
        
        document.getElementById('totalAmount').textContent = total.toFixed(2);
        document.getElementById('total_amount').value = total;
        
        checkItems();
    }
    
    // Function to check if there are items in the sale
    function checkItems() {
        const rows = document.querySelectorAll('#saleItemsBody tr');
        const completeSaleBtn = document.getElementById('completeSaleBtn');
        
        if (rows.length > 0) {
            completeSaleBtn.disabled = false;
        } else {
            completeSaleBtn.disabled = true;
        }
    }
    
    // Form validation before submission
    const saleForm = document.getElementById('saleForm');
    if (saleForm) {
        saleForm.addEventListener('submit', function(event) {
            const rows = document.querySelectorAll('#saleItemsBody tr');
            if (rows.length === 0) {
                event.preventDefault();
                alert('Please add at least one product to the sale.');
            }
        });
    }
});
```