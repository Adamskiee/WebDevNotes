```php
<?php
$host = "localhost";
$username = "root";
$password = "";
$database = "testdb";
$conn = new mysqli($host, $username, $password,
$database);
// Check connection
if ($conn->connect_error) { 
die("Connection failed: " . $conn-
>connect_error);
}
?>
```

`$conn->connect_error`: This property stores the error message if the connection fails. If there's no error, it will be empty (i.e., `null`).
## INSERTING DATA
```php
<?php
include 'db.php';
$name = "John Doe";
$email = "john@example.com";
$sql = "INSERT INTO users (name, email) VALUES
('$name', '$email')";
if ($conn->query($sql) === TRUE) {
echo "New record created successfully";
} else {
echo "Error: " . $conn->error;
}
$conn->close();
?>
```
**`include 'db.php';`**:
- This is used to include your database connection file (`db.php`), where you presumably have your `$conn` object defined. Make sure your `db.php` file contains the correct connection setup for MySQL.
`$result = $conn->query($sql)` executes the query. The result of a `SELECT` query is stored in the `$result` variable.
If the query fails, it prints the error message using `$conn->error`.
## READ DATA
```php
<?php
include 'db.php';
$sql = "SELECT * FROM users";
$result = $conn->query($sql);
if ($result->num_rows > 0) {
while($row = $result->fetch_assoc()) {
echo "ID: " . $row["id"]. " - Name: " .
$row["name"]. " - Email: " . $row["email"].
"<br>";
}
} else {
echo "0 results";
}
$conn->close();
?>
```
You correctly check if the number of rows returned by the query is greater than 0 using `$result->num_rows > 0`
`$result->fetch_assoc()` to fetch each row and display the data in a readable format.
## UPDATE DATA
```php
<?php
include 'db.php';
$sql = "UPDATE users SET name='Jane Doe' WHERE
id=1";
if ($conn->query($sql) === TRUE) {
echo "Record updated successfully";
} else {
echo "Error updating record: " . $conn-
>error;
}
$conn->close();
```

## DELETE DATA
```php
<?php
include 'db.php';
$sql = "DELETE FROM users WHERE id=1";
if ($conn->query($sql) === TRUE) {
echo "Record deleted successfully";
} else {
echo "Error deleting record: " . $conn-
>error;
}
$conn->close();
```

