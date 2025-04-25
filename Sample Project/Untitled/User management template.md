``` php
<div class="card">
    <div class="card-header">
        <h3><?php echo $edit_user ? 'Edit User' : 'Add New User'; ?></h3>
    </div>
    <div class="card-body">
        <form method="POST" action="">
            <?php if($edit_user): ?>
                <input type="hidden" name="user_id" value="<?php echo $edit_user['user_id']; ?>">
            <?php endif; ?>
            
            <div class="form-grid">
                <div class="input-group">
                    <label for="username">Username</label>
                    <input type="text" id="username" name="username" value="<?php echo $edit_user['username'] ?? ''; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="password">Password <?php echo $edit_user ? '(Leave blank to keep current)' : ''; ?></label>
                    <input type="password" id="password" name="password" <?php echo $edit_user ? '' : 'required'; ?>>
                </div>
                
                <div class="input-group">
                    <label for="full_name">Full Name</label>
                    <input type="text" id="full_name" name="full_name" value="<?php echo $edit_user['full_name'] ?? ''; ?>" required>
                </div>
                
                <div class="input-group">
                    <label for="email">Email</label>
                    <input type="email" id="email" name="email" value="<?php echo $edit_user['email'] ?? ''; ?>">
                </div>
                
                <div class="input-group">
                    <label for="role">Role</label>
                    <select id="role" name="role" required>
                        <option value="admin" <?php echo (isset($edit_user['role']) && $edit_user['role'] == 'admin') ? 'selected' : ''; ?>>Administrator</option>
                        <option value="employee" <?php echo (isset($edit_user['role']) && $edit_user['role'] == 'employee') ? 'selected' : ''; ?>>Employee</option>
                    </select>
                </div>
            </div>
            
            <div class="text-right">
                <?php if($edit_user): ?>
                    <a href="users.php" class="btn btn-secondary">Cancel</a>
                    <button type="submit" name="edit_user" class="btn btn-primary">Update User</button>
                <?php else: ?>
                    <button type="submit" name="add_user" class="btn btn-success">Add User</button>
                <?php endif; ?>
            </div>
        </form>
    </div>
</div>

<div class="card mt-2">
    <div class="card-header">
        <h3>All Users</h3>
    </div>
    <div class="card-body">
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Username</th>
                        <th>Full Name</th>
                        <th>Email</th>
                        <th>Role</th>
                        <th>Created</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <?php if(empty($users)): ?>
                        <tr>
                            <td colspan="7">No users found</td>
                        </tr>
                    <?php else: ?>
                        <?php foreach($users as $user): ?>
                            <tr>
                                <td><?php echo $user['user_id']; ?></td>
                                <td><?php echo $user['username']; ?></td>
                                <td><?php echo $user['full_name']; ?></td>
                                <td><?php echo $user['email']; ?></td>
                                <td><?php echo ucfirst($user['role']); ?></td>
                                <td><?php echo date('M d, Y', strtotime($user['created_at'])); ?></td>
                                <td>
                                    <a href="users.php?edit=<?php echo $user['user_id']; ?>" class="btn btn-secondary btn-sm">Edit</a>
                                    <?php if($user['user_id'] != $_SESSION['user_id']): ?>
                                    <form method="POST" action="" style="display: inline;">
                                        <input type="hidden" name="user_id" value="<?php echo $user['user_id']; ?>">
                                        <button type="submit" name="delete_user" class="btn btn-danger btn-sm" onclick="return confirm('Are you sure you want to delete this user?')">Delete</button>
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