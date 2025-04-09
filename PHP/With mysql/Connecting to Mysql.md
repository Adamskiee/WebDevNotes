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
include_once 'db.php';
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

```php
<?php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 if ($conn->connect_error) die("Fatal Error");
 $query = "SELECT * FROM cats";
 $result = $conn->query($query);
 if (!$result) die ("Database access failed");
 $rows = $result->num_rows;
 echo "<table><tr> <th>Id</th> <th>Family</th><th>Name</th><th>Age</th></tr>";
 for ($j = 0 ; $j < $rows ; ++$j)
 {
 $row = $result->fetch_array(MYSQLI_NUM);
 echo "<tr>";
 for ($k = 0 ; $k < 4 ; ++$k)
 echo "<td>" . htmlspecialchars($row[$k]) . "</td>";
 echo "</tr>";
 }
 echo "</table>";
?>
```
### Fetching results one cell at a time
```php
<?php // query-mysqli.php
 require_once 'login.php';
 $connection = new mysqli($hn, $un, $pw, $db);
 
 if ($connection->connect_error) die("Fatal Error"); 
 
 $query = "SELECT * FROM classics"; 
 $result = $connection->query($query); 
 
 if (!$result) die("Fatal Error"); 
 
 $rows = $result->num_rows;
 for ($j = 0 ; $j < $rows ; ++$j) 
 { 
 $result->data_seek($j); 
 echo 'Author: ' .htmlspecialchars($result->fetch_assoc()['author']) .'  '; 
 $result->data_seek($j); 
 echo 'Title: ' .htmlspecialchars($result->fetch_assoc()['title']) .'  ';
 $result->data_seek($j);
 echo 'Category: '.htmlspecialchars($result->fetch_assoc()['category']).'  ';
 $result->data_seek($j);
 echo 'Year: ' .htmlspecialchars($result->fetch_assoc()['year']) .'  ';
 $result->data_seek($j);
 echo 'ISBN: ' .htmlspecialchars($result->fetch_assoc()['isbn']) .'  '; 
 }
 $result->close(); $connection->close(); 
?>
```

### Fetching results one row at a time
```php
<?php // fetchrow.php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 if ($conn->connect_error) die("Fatal Error");
 
 $query = "SELECT * FROM classics";
 $result = $conn->query($query);
 if (!$result) die("Fatal Error");
 
 $rows = $result->num_rows;
 
 for ($j = 0 ; $j < $rows ; ++$j)
 {
 $row = $result->fetch_array(MYSQLI_ASSOC);
 echo 'Author: ' . htmlspecialchars($row['author']) . '<br>';
 echo 'Title: ' . htmlspecialchars($row['title']) . '<br>';
 echo 'Category: ' . htmlspecialchars($row['category']) . '<br>';
 echo 'Year: ' . htmlspecialchars($row['year']) . '<br>';
 echo 'ISBN: ' . htmlspecialchars($row['isbn']) . '<br><br>';
 }
 $result->close();
 $conn->close();
?>
```
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

## PRACTICAL EXAMPLE

```php
<?php // sqltest.php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 if ($conn->connect_error) die("Fatal Error");
 
 if (isset($_POST['delete']) && isset($_POST['isbn']))
 {
	 $isbn = get_post($conn, 'isbn');
	 $query = "DELETE FROM classics WHERE isbn='$isbn'";
	 $result = $conn->query($query);
	 
	 if (!$result) echo "DELETE failed<br><br>";
 }
 
 if (isset($_POST['author']) &&
	 isset($_POST['title']) &&
	 isset($_POST['category']) &&
	 isset($_POST['year']) &&
	 isset($_POST['isbn']))
 {
	 $author = get_post($conn, 'author');
	 $title = get_post($conn, 'title');
	 $category = get_post($conn, 'category');
	 $year = get_post($conn, 'year');
	 $isbn = get_post($conn, 'isbn');
	 $query = "INSERT INTO classics VALUES" .
	 "('$author', '$title', '$category', '$year', '$isbn')";
	 $result = $conn->query($query);
	 if (!$result) echo "INSERT failed<br><br>";
 }
 
 echo <<<_END
 <form action="sqltest.php" method="post">
	 <pre>
		 Author <input type="text" name="author">
		 Title <input type="text" name="title">
		 Category <input type="text" name="category">
		 Year <input type="text" name="year">
		 ISBN <input type="text" name="isbn">
		 <input type="submit" value="ADD RECORD">
	 </pre>
 </form>
 _END;

 $query = "SELECT * FROM classics";
 
 $result = $conn->query($query);
 if (!$result) die ("Database access failed");
 
 $rows = $result->num_rows;
 for ($j = 0 ; $j < $rows ; ++$j)
 {
	 $row = $result->fetch_array(MYSQLI_NUM);
	 $r0 = htmlspecialchars($row[0]);
	 $r1 = htmlspecialchars($row[1]);
	 $r2 = htmlspecialchars($row[2]);
	 $r3 = htmlspecialchars($row[3]);
	 $r4 = htmlspecialchars($row[4]);
	
	 echo <<<_END
	 <pre>
		 Author $r0
		 Title $r1
		 Category $r2
		 Year $r3
		 ISBN $r4
	 </pre>
	 <form action='sqltest.php' method='post'>
		 <input type='hidden' name='delete' value='yes'>
		 <input type='hidden' name='isbn' value='$r4'>
		 <input type='submit' value='DELETE RECORD'>
	 </form>
	_END;
 }
 
 $result->close();
 $conn->close();
 
 function get_post($conn, $var)
 {
	 return $conn->real_escape_string($_POST[$var]);
 }
?>
```

## Creating table
```php
<?php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 if ($conn->connect_error) die("Fatal Error");
 $query = "CREATE TABLE cats (
 id SMALLINT NOT NULL AUTO_INCREMENT,
 family VARCHAR(32) NOT NULL,
 name VARCHAR(32) NOT NULL,
 age TINYINT NOT NULL,
 PRIMARY KEY (id)
 )";
 $result = $conn->query($query);
 if (!$result) die ("Database access failed");
?>
```

## Describing the table
```php
<?php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 
 if ($conn->connect_error) die("Fatal Error");
 
 $query = "DESCRIBE cats";
 $result = $conn->query($query);
 
 if (!$result) die ("Database access failed");
 
 $rows = $result->num_rows;
 
 echo "<table><tr><th>Column</th><th>Type</th><th>Null</th><th>Key</th></tr>";
 for ($j = 0 ; $j < $rows ; ++$j)
 {
	 $row = $result->fetch_array(MYSQLI_NUM);
	 echo "<tr>";
	 for ($k = 0 ; $k < 4 ; ++$k)
	 echo "<td>" . htmlspecialchars($row[$k]) . "</td>";
	 echo "</tr>";
 }
 echo "</table>";
?>
```

