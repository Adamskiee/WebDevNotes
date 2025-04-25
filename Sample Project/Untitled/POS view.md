```php
<div class="pos-container">
    <div class="product-section">
        <div class="card">
            <div class="card-header">
                <h3>Products</h3>
                <div class="search-filter">
                    <input type="text" id="productSearch" placeholder="Search products..." class="search-input">
                    <select id="categoryFilter" class="category-filter">
                        <option value="all">All Categories</option>
                        <?php foreach($categories as $category): ?>
                            <option value="<?php echo $category['category_id']; ?>"><?php echo $category['category_name']; ?></option>
                        <?php endforeach; ?>
                    </select>
                </div>
            </div>
            <div class="card-body">
                <div class="products-grid" id="productsGrid">
                    <?php foreach($products as $product): ?>
                        <div class="product-card" data-id="<?php echo $product['product_id']; ?>" 
                             data-name="<?php echo $product['product_name']; ?>"
                             data-price="<?php echo $product['price']; ?>"
                             data-stock="<?php echo $product['quantity']; ?>">
                            <div class="product-name"><?php echo $product['product_name']; ?></div>
                            <div class="product-price">$<?php echo number_format($product['price'], 2); ?></div>
                            <div class="product-stock"><?php echo $product['quantity']; ?> in stock</div>
                        </div>
                    <?php endforeach; ?>
                </div>
            </div>
        </div>
    </div>
    
    <div class="cart-section">
        <div class="card">
            <div class="card-header">
                <h3>Current Sale</h3>
                <button type="button" id="clearCartBtn" class="btn btn-warning">Clear</button>
            </div>
            <div class="card-body">
                <form method="POST" action="" id="posForm">
                    <div class="cart-items">
                        <table id="cartTable">
                            <thead>
                                <tr>
                                    <th>Product</th>
                                    <th>Price</th>
                                    <th>Qty</th>
                                    <th>Subtotal</th>
                                    <th>Action</th>
                                </tr>
                            </thead>
                            <tbody id="cartItems">
                                <!-- Cart items will be added here via JavaScript -->
                            </tbody>
                            <tfoot>
                                <tr>
                                    <td colspan="3" class="text-right"><strong>Total:</strong></td>
                                    <td><span id="cartTotal">$0.00</span></td>
                                    <td></td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>
                    
                    <div class="customer-payment mt-2">
                        <div class="input-group">
                            <label for="customer_id">Customer (Optional)</label>
                            <select name="customer_id" id="customer_id">
                                <option value="">Walk-in Customer</option>
                                <?php foreach($customers as $customer): ?>
                                    <option value="<?php echo $customer['customer_id']; ?>"><?php echo $customer['customer_name']; ?></option>
                                <?php endforeach; ?>
                            </select>
                        </div>
                        
                        <div class="input-group">
                            <label for="payment_method">Payment Method</label>
                            <select name="payment_method" id="payment_method" required>
                                <option value="cash">Cash</option>
                                <option value="credit_card">Credit Card</option>
                                <option value="debit_card">Debit Card</option>
                                <option value="online">Online Payment</option>
                            </select>
                        </div>
                        
                        <div class="payment-calc">
                            <div class="input-group">
                                <label for="amount_paid">Amount Paid</label>
                                <input type="number" id="amount_paid" min="0" step="0.01" class="amount-input">
                            </div>
                            <div class="input-group">
                                <label for="change_amount">Change</label>
                                <input type="text" id="change_amount" readonly class="change-input">
                            </div>
                        </div>
                    </div>
                    
                    <div class="checkout-buttons mt-2">
                        <button type="submit" name="process_sale" id="checkoutBtn" class="btn btn-success btn-lg" disabled>Process Sale</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>

<style>
    .pos-container {
        display: flex;
        gap: 20px;
    }
    
    .product-section {
        flex: 2;
    }
    
    .cart-section {
        flex: 1;
    }
    
    .search-filter {
        display: flex;
        gap: 10px;
    }
    
    .search-input {
        flex: 2;
    }
    
    .category-filter {
        flex: 1;
    }
    
    .products-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
        gap: 15px;
        max-height: 70vh;
        overflow-y: auto;
        padding: 10px;
    }
    
    .product-card {
        background-color: white;
        border: 1px solid #ddd;
        border-radius: 5px;
        padding: 15px;
        cursor: pointer;
        transition: all 0.3s;
    }
    
    .product-card:hover {
        border-color: #6e8efb;
        box-shadow: 0 3px 8px rgba(0,0,0,0.1);
    }
    
    .product-name {
        font-weight: bold;
        margin-bottom: 5px;
    }
    
    .product-price {
        color: #28a745;
        font-size: 16px;
        margin-bottom: 5px;
    }
    
    .product-stock {
        font-size: 12px;
        color: #666;
    }
    
    .cart-items {
        max-height: 40vh;
        overflow-y: auto;
    }
    
    .payment-calc {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 10px;
    }
    
    .checkout-buttons {
        text-align: center;
    }
    
    .qty-input {
        width: 60px;
        text-align: center;
    }
    
    @media (max-width: 992px) {
        .pos-container {
            flex-direction: column;
        }
    }
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
    const productsGrid = document.getElementById('productsGrid');
    const cartItems = document.getElementById('cartItems');
    const cartTotal = document.getElementById('cartTotal');
    const checkoutBtn = document.getElementById('checkoutBtn');
    const clearCartBtn = document.getElementById('clearCartBtn');
    const productSearch = document.getElementById('productSearch');
    const categoryFilter = document.getElementById('categoryFilter');
    const amountPaid = document.getElementById('amount_paid');
    const changeAmount = document.getElementById('change_amount');
    
    let cart = [];
    
    // Add product to cart
    productsGrid.addEventListener('click', function(e) {
        const productCard = e.target.closest('.product-card');
        if (productCard) {
            const productId = productCard.getAttribute('data-id');
            const productName = productCard.getAttribute('data-name');
            const productPrice = parseFloat(productCard.getAttribute('data-price'));
            const productStock = parseInt(productCard.getAttribute('data-stock'));
            
            // Check if product already in cart
            const existingItem = cart.find(item => item.id === productId);
            
            if (existingItem) {
                // Increase quantity if not exceeding stock
                if (existingItem.quantity < productStock) {
                    existingItem.quantity++;
                    existingItem.subtotal = existingItem.price * existingItem.quantity;
                } else {
                    alert(`Cannot add more. Only ${productStock} available in stock.`);
                }
            } else {
                // Add new item to cart
                cart.push({
                    id: productId,
                    name: productName,
                    price: productPrice,
                    quantity: 1,
                    stock: productStock,
                    subtotal: productPrice
                });
            }
            
            updateCart();
        }
    });
    
    // Update cart when quantity changes
    cartItems.addEventListener('input', function(e) {
        if (e.target.classList.contains('qty-input')) {
            const row = e.target.closest('tr');
            const productId = row.getAttribute('data-id');
            const quantity = parseInt(e.target.value) || 0;
            
            const cartItem = cart.find(item => item.id === productId);
            if (cartItem) {
                // Validate quantity against stock
                if (quantity > cartItem.stock) {
                    e.target.value = cartItem.stock;
                    cartItem.quantity = cartItem.stock;
                    alert(`Cannot add more than ${cartItem.stock} items (max stock).`);
                } else if (quantity <= 0) {
                    // Remove item if quantity is 0 or negative
                    cart = cart.filter(item => item.id !== productId);
                    row.remove();
                } else {
                    cartItem.quantity = quantity;
                }
                
                cartItem.subtotal = cartItem.price * cartItem.quantity;
                updateCart();
            }
        }
    });
    
    // Remove item from cart
    cartItems.addEventListener('click', function(e) {
        if (e.target.classList.contains('remove-item')) {
            const row = e.target.closest('tr');
            const productId = row.getAttribute('data-id');
            
            cart = cart.filter(item => item.id !== productId);
            updateCart();
        }
    });
    
    // Clear cart
    clearCartBtn.addEventListener('click', function() {
        if (cart.length > 0) {
            if (confirm('Are you sure you want to clear the cart?')) {
            cart = [];
                updateCart();
            }
        }
    });
    
    // Calculate change amount when payment amount is entered
    amountPaid.addEventListener('input', function() {
        const totalValue = getTotalValue();
        const paid = parseFloat(this.value) || 0;
        const change = paid - totalValue;
        
        changeAmount.value = change >= 0 ? '$' + change.toFixed(2) : '$0.00';
    });
    
    // Product search functionality
    productSearch.addEventListener('input', function() {
        filterProducts();
    });
    
    // Category filter functionality
    categoryFilter.addEventListener('change', function() {
        filterProducts();
    });
    
    // Update cart display
    function updateCart() {
        cartItems.innerHTML = '';
        
        if (cart.length === 0) {
            checkoutBtn.disabled = true;
            cartTotal.textContent = '$0.00';
            return;
        }
        
        let total = 0;
        
        cart.forEach(item => {
            const row = document.createElement('tr');
            row.setAttribute('data-id', item.id);
            
            row.innerHTML = `
                <td>
                    ${item.name}
                    <input type="hidden" name="product_id[]" value="${item.id}">
                </td>
                <td>
                    $${item.price.toFixed(2)}
                    <input type="hidden" name="price[]" value="${item.price}">
                </td>
                <td>
                    <input type="number" class="qty-input" name="quantity[]" value="${item.quantity}" min="1" max="${item.stock}">
                </td>
                <td>$${item.subtotal.toFixed(2)}</td>
                <td>
                    <button type="button" class="btn btn-danger btn-sm remove-item">X</button>
                </td>
            `;
            
            cartItems.appendChild(row);
            total += item.subtotal;
        });
        
        cartTotal.textContent = '$' + total.toFixed(2);
        checkoutBtn.disabled = false;
        
        // Update amount paid & change calculation
        updateChangeAmount();
    }
    
    // Get total cart value
    function getTotalValue() {
        let total = 0;
        cart.forEach(item => {
            total += item.subtotal;
        });
        return total;
    }
    
    // Update change amount calculation
    function updateChangeAmount() {
        const totalValue = getTotalValue();
        const paid = parseFloat(amountPaid.value) || 0;
        const change = paid - totalValue;
        
        changeAmount.value = change >= 0 ? '$' + change.toFixed(2) : '$0.00';
    }
    
    // Filter products by search term and category
    function filterProducts() {
        const searchTerm = productSearch.value.toLowerCase();
        const categoryId = categoryFilter.value;
        
        const productCards = document.querySelectorAll('.product-card');
        
        productCards.forEach(card => {
            const productName = card.getAttribute('data-name').toLowerCase();
            const productCategory = card.getAttribute('data-category');
            
            const matchesSearch = productName.includes(searchTerm);
            const matchesCategory = categoryId === 'all' || productCategory === categoryId;
            
            if (matchesSearch && matchesCategory) {
                card.style.display = 'block';
            } else {
                card.style.display = 'none';
            }
        });
    }
    
    // Prevent form submission if cart is empty
    document.getElementById('posForm').addEventListener('submit', function(e) {
        if (cart.length === 0) {
            e.preventDefault();
            alert('Your cart is empty. Please add products before checking out.');
            return false;
        }
        
        // For cash payments, validate that amount paid is enough
        const paymentMethod = document.getElementById('payment_method').value;
        if (paymentMethod === 'cash') {
            const totalValue = getTotalValue();
            const paid = parseFloat(amountPaid.value) || 0;
            
            if (paid < totalValue) {
                e.preventDefault();
                alert('Amount paid must be equal to or greater than the total amount.');
                return false;
            }
        }
        
        return true;
    });
});
</script>
```