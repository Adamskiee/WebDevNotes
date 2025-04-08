
#### 1. **Create the MySQL Table**

In phpMyAdmin, run this SQL to create the table:

```sql
CREATE DATABASE IF NOT EXISTS contact_db;

USE contact_db;

CREATE TABLE contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL,
    message TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

#### 2. **Create `db.php` for DB Connection**

```php
<?php
$servername = "localhost";
$username = "root";
$password = "";
$dbname = "contact_db";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
```

---

#### 3. **Create `form.html` (Your Contact Form)**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Contact Form</title>
</head>
<body>
    <h2>Contact Us</h2>
    <form method="POST" action="submit.php">
        <label>Name:</label><br>
        <input type="text" name="name" required><br><br>
        
        <label>Email:</label><br>
        <input type="email" name="email" required><br><br>
        
        <label>Message:</label><br>
        <textarea name="message" required></textarea><br><br>
        
        <input type="submit" value="Send">
    </form>
</body>
</html>
```

---

#### 4. **Create `submit.php` to Handle Form Submission**

```php
<?php
include 'db.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = htmlspecialchars($_POST['name']);
    $email = htmlspecialchars($_POST['email']);
    $message = htmlspecialchars($_POST['message']);

    $sql = "INSERT INTO contacts (name, email, message)
            VALUES ('$name', '$email', '$message')";

    if ($conn->query($sql) === TRUE) {
        echo "Message sent successfully!";
    } else {
        echo "Error: " . $conn->error;
    }

    $conn->close();
}
?>
```

---