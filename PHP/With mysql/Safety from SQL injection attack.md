# Preventing Hacking attempts
```php
<?php
 require_once 'login.php';
 $conn = new mysqli($hn, $un, $pw, $db);
 if ($conn->connect_error) die("Fatal Error");
 $user = mysql_fix_string($conn, $_POST['user']);
 $pass = mysql_fix_string($conn, $_POST['pass']);
 $query = "SELECT * FROM users WHERE user='$user' AND pass='$pass'";
 // Etc.
 function mysql_fix_string($conn, $string)
 {
	 if (get_magic_quotes_gpc()) //returns TRUE if magic quotes are active
		 $string = stripslashes($string); 
	 return $conn->real_escape_string($string);
 }
?>
```

# Placeholders
They are positions within prepared statements in which data is transferred directly to the database, without the possibility of user-submitted (or other) data being interpreted as MySQL statements (and the potential for hacking that could then result).
``` mysql
PREPARE statement FROM "INSERT INTO classics VALUES(?,?,?,?,?)"; 
SET @author = "Emily Brontë", 
	@title = "Wuthering Heights", 
	@category = "Classic Fiction", 
	@year = "1847", 
	@isbn = "9780553212587"; 
EXECUTE statement USING @author,@title,@category,@year,@isbn;
DEALLOCATE PREPARE statement;
```

This can be cumbersome to submit to MySQL, so the mysqli extension makes handling placeholders easier for you with a ready-made method called prepare, which you call like this: ``$stmt = $conn->prepare('INSERT INTO classics VALUES(?,?,?,?,?)');``

`$stmt->bind_param('sssss', $author, $title, $category, $year, $isbn);`
The first argument to `bind_param` is a string representing the type of each of the arguments in turn. In this case, it comprises five s characters, representing strings, but any combination of types can be specified here, out of the following:
• i: The data is an integer. 
• d: The data is a double. 
• s: The data is a string.
• b: The data is a BLOB (and will be sent in packets).

```php
 <?php
  require_once 'login.php';
  $conn = new mysqli($hn, $un, $pw, $db);
  if ($conn->connect_error) die("Fatal Error");
  $stmt = $conn->prepare('INSERT INTO classics VALUES(?,?,?,?,?)');
  $stmt->bind_param('sssss', $author, $title, $category, $year, $isbn);
  $author   = 'Emily Brontë';
  $title    = 'Wuthering Heights';
  $category = 'Classic Fiction';
  $year     = '1847';
  $isbn     = '9780553212587';
  $stmt->execute();
  printf("%d Row inserted.\n", $stmt->affected_rows);
  $stmt->close();
  $conn->close();
 ?>
```

# Preventing HTML injection
``` php
 <?php
  require_once 'login.php';
  $conn = new mysqli($hn, $un, $pw, $db);
  if ($conn->connect_error) die("Fatal Error");
  $user  = mysql_entities_fix_string($conn, $_POST['user']);
  $pass  = mysql_entities_fix_string($conn, $_POST['pass']);
  $query = "SELECT * FROM users WHERE user='$user' AND pass='$pass'";
  // Etc.
  function mysql_entities_fix_string($conn, $string)
  {
    return htmlentities(mysql_fix_string($conn, $string));
  }
  function mysql_fix_string($conn, $string)
  {
    if (get_magic_quotes_gpc()) $string = stripslashes($string);
    return $conn->real_escape_string($string);
  }
 ?>
```