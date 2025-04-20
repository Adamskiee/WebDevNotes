
### **1. Database Setup (MySQL)**

Create a database named `store_db` and run this SQL script:

```sql
CREATE DATABASE store_db;
USE store_db;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    quantity INT NOT NULL
);
```

---

### **2. `db.php` - Database Connection**

```php
<?php
$host = 'localhost';
$user = 'root';
$pass = '';
$db = 'store_db';

$conn = new mysqli($host, $user, $pass, $db);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
```

---

### **3. `register.php` - User Registration**

```php
<?php
require 'db.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = password_hash($_POST["password"], PASSWORD_BCRYPT);

    $stmt = $conn->prepare("INSERT INTO users (username, password) VALUES (?, ?)");
    $stmt->bind_param("ss", $username, $password);
    $stmt->execute();

    header("Location: login.html");
}
?>
```

---

### **4. `login.php` - User Login**

```php
<?php
require 'db.php';
session_start();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = $_POST["password"];

    $stmt = $conn->prepare("SELECT id, password FROM users WHERE username = ?");
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $stmt->store_result();
    
    $stmt->bind_result($id, $hashed_password);
    $stmt->fetch();

    if ($stmt->num_rows > 0 && password_verify($password, $hashed_password)) {
        $_SESSION["user_id"] = $id;
        header("Location: dashboard.php");
    } else {
        echo "Invalid login.";
    }
}
?>
```

---

### **5. `dashboard.php` - Main Interface**

```php
<?php
require 'db.php';
session_start();

if (!isset($_SESSION["user_id"])) {
    header("Location: login.html");
    exit;
}

$products = $conn->query("SELECT * FROM products");
?>

<!DOCTYPE html>
<html>
<head>
    <title>Store Dashboard</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
<h2>Product List</h2>
<a href="add_product.html">Add Product</a>
<table>
    <tr><th>ID</th><th>Name</th><th>Price</th><th>Quantity</th><th>Actions</th></tr>
    <?php while ($row = $products->fetch_assoc()): ?>
        <tr>
            <td><?= $row["id"] ?></td>
            <td><?= $row["name"] ?></td>
            <td><?= $row["price"] ?></td>
            <td><?= $row["quantity"] ?></td>
            <td>
                <a href="edit_product.php?id=<?= $row['id'] ?>">Edit</a>
                <a href="delete_product.php?id=<?= $row['id'] ?>">Delete</a>
            </td>
        </tr>
    <?php endwhile; ?>
</table>
</body>
</html>
```

---

### **6. Add/Edit/Delete Products (PHP)**

#### `add_product.php`

```php
<?php
require 'db.php';

$name = $_POST["name"];
$price = $_POST["price"];
$quantity = $_POST["quantity"];

$stmt = $conn->prepare("INSERT INTO products (name, price, quantity) VALUES (?, ?, ?)");
$stmt->bind_param("sdi", $name, $price, $quantity);
$stmt->execute();

header("Location: dashboard.php");
?>
```

#### `edit_product.php` (with form and update logic)

```php
<?php
require 'db.php';

if ($_SERVER['REQUEST_METHOD'] === 'GET') {
    $id = $_GET['id'];
    $result = $conn->query("SELECT * FROM products WHERE id = $id");
    $product = $result->fetch_assoc();
} else {
    $id = $_POST["id"];
    $name = $_POST["name"];
    $price = $_POST["price"];
    $quantity = $_POST["quantity"];
    
    $stmt = $conn->prepare("UPDATE products SET name=?, price=?, quantity=? WHERE id=?");
    $stmt->bind_param("sdii", $name, $price, $quantity, $id);
    $stmt->execute();

    header("Location: dashboard.php");
    exit;
}
?>

<form method="POST">
    <input type="hidden" name="id" value="<?= $product['id'] ?>">
    Name: <input type="text" name="name" value="<?= $product['name'] ?>"><br>
    Price: <input type="text" name="price" value="<?= $product['price'] ?>"><br>
    Quantity: <input type="number" name="quantity" value="<?= $product['quantity'] ?>"><br>
    <button type="submit">Update</button>
</form>
```

#### `delete_product.php`

```php
<?php
require 'db.php';
$id = $_GET['id'];
$conn->query("DELETE FROM products WHERE id = $id");
header("Location: dashboard.php");
?>
```

---

### **7. HTML Files (Frontend)**

#### `login.html`

```html
<form method="POST" action="login.php">
    Username: <input type="text" name="username"><br>
    Password: <input type="password" name="password"><br>
    <button type="submit">Login</button>
</form>
```

#### `register.html`

```html
<form method="POST" action="register.php">
    Username: <input type="text" name="username"><br>
    Password: <input type="password" name="password"><br>
    <button type="submit">Register</button>
</form>
```

#### `add_product.html`

```html
<form method="POST" action="add_product.php">
    Name: <input type="text" name="name"><br>
    Price: <input type="text" name="price"><br>
    Quantity: <input type="number" name="quantity"><br>
    <button type="submit">Add Product</button>
</form>
```

---

### **8. `style.css` (Basic Styling)**

```css
body {
    font-family: Arial;
    padding: 20px;
}
table {
    width: 100%;
    border-collapse: collapse;
}
th, td {
    border: 1px solid #ccc;
    padding: 10px;
}
a {
    margin: 0 5px;
}
```

---

This setup covers all the required features: registration, login, CRUD for products, MySQL integration, prepared statements, and basic styling. Let me know if you'd like a downloadable ZIP or guide for deployment using XAMPP.