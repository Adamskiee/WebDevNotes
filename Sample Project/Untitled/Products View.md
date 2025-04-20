``` php
<div class="card">
    <div class="card-header">
        <h3><?php echo $edit_product ? 'Edit Product' : 'Add New Product'; ?></h3>
    </div>
    <div class="card-body">
        <form method="POST" action="">
            <?php if($edit_product): ?>
                <input type="hidden" name="product_id" value="<?php echo $edit_product['product_id']; ?>">
            <?php endif; ?>
            
            <div class="form-grid">
                <div class="input-group">
                    <label for="product_name">Product Name</label>
                    <input type="text" id="product_name" name="product_name" value="<?php echo $edit_product['product_name'] ?? ''; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="category_id">Category</label>
                    <select id="category_id" name="category_id" required>
                        <option value="">Select Category</option>
                        <?php foreach($categories as $category): ?>
                            <option value="<?php echo $category['category_id']; ?>" <?php echo (isset($edit_product['category_id']) && $edit_product['category_id'] == $category['category_id']) ? 'selected' : ''; ?>>
                                <?php echo $category['category_name']; ?>
                            </option>
                        <?php endforeach; ?>
                    </select>
                </div>
                
                <div class="input-group">
                    <label for="price">Selling Price</label>
                    <input type="number" id="price" name="price" step="0.01" min="0" value="<?php echo $edit_product['price'] ?? ''; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="cost_price">Cost Price</label>
                    <input type="number" id="cost_price" name="cost_price" step="0.01" min="0" value="<?php echo $edit_product['cost_price'] ?? ''; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="quantity">Current Stock Quantity</label>
                    <input type="number" id="quantity" name="quantity" min="0" value="<?php echo $edit_product['quantity'] ?? '0'; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="reorder_level">Reorder Level</label>
                    <input type="number" id="reorder_level" name="reorder_level" min="0" value="<?php echo $edit_product['reorder_level'] ?? '10'; ?>" required>
                </div>
            </div>
            
            <div class="input-group">
                <label for="description">Description</label>
                <textarea id="description" name="description" rows="3"><?php echo $edit_product['description'] ?? ''; ?></textarea>
            </div>
            
            <div class="text-right">
                <?php if($edit_product): ?>
                    <a href="products.php" class="btn btn-secondary">Cancel</a>
                    <button type="submit" name="edit_product" class="btn btn-primary">Update Product</button>
                <?php else: ?>
                    <button type="submit" name="add_product" class="btn btn-success">Add Product</button>
                <?php endif; ?>
            </div>
        </form>
    </div>
</div>

<div class="card mt-2">
    <div class="card-header">
        <h3>All Products</h3>
    </div>
    <div class="card-body">
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Name</th>
                        <th>Category</th>
                        <th>Price</th>
                        <th>Cost</th>
                        <th>Stock</th>
                        <th>Status</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <?php if(empty($products)): ?>
                        <tr>
                            <td colspan="8">No products found</td>
                        </tr>
                    <?php else: ?>
                        <?php foreach($products as $product): ?>
                            <tr>
                                <td><?php echo $product['product_id']; ?></td>
                                <td><?php echo $product['product_name']; ?></td>
                                <td><?php echo $product['category_name'] ?? 'Uncategorized'; ?></td>
                                <td>$<?php echo number_format($product['price'], 2); ?></td>
                                <td>$<?php echo number_format($product['cost_price'], 2); ?></td>
                                <td><?php echo $product['quantity']; ?></td>
                                <td>
                                    <?php if($product['quantity'] <= 0): ?>
                                        <span class="text-danger">Out of Stock</span>
                                    <?php elseif($product['quantity'] <= $product['reorder_level']): ?>
                                        <span class="text-warning">Low Stock</span>
                                    <?php else: ?>
                                        <span class="text-success">In Stock</span>
                                    <?php endif; ?>
                                </td>
                                <td>
                                    <a href="products.php?edit=<?php echo $product['product_id']; ?>" class="btn btn-secondary btn-sm">Edit</a>
                                    <form method="POST" action="" style="display: inline;">
                                        <input type="hidden" name="product_id" value="<?php echo $product['product_id']; ?>">
                                        <button type="submit" name="delete_product" class="btn btn-danger btn-sm" onclick="return confirm('Are you sure you want to delete this product?')">Delete</button>
                                    </form>
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