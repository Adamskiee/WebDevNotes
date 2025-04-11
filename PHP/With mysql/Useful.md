	`insert_id` = stored the id created by auto_increment
	

The `fetch_array` method can return three types of array according to the value passed to it:
MYSQLI_NUM Numeric array
- Each column appears in the array in the order in which you defined it when you created (or altered) the table. In our case, the zeroth element of the array contains the author column, element 1 contains the title column, and so on.
MYSQLI_ASSOC Associative array
- Each key is the name of a column. Because items of data are referenced by column name (rather than index number), use this option where possible in your code to make debugging easier and help other programmers better manage your code.
MYSQLI_BOTH Associative and numeric array
- Associative arrays are usually more useful than numeric ones because you can refer to each column by name, such as `$row['author']`, instead of trying to remember where it is in the column order. This script uses an associative array, leading us to pass MYSQLI_ASSOC.

``$stmt->affected_rows`` = verify if command is executed properly


## Preventing Hacking Attempts
``` php
function mysql_fix_string($conn, $string)
  {
    if (get_magic_quotes_gpc()) $string = stripslashes($string);
    return $conn->real_escape_string($string);
  }
```
 The `get_magic_quotes_gpc` function returns TRUE if magic quotes are active. In that
 case, any slashes that have been added to a string have to be removed, or the
 real_escape_string method could end up double-escaping some characters, creat
ing corrupted strings. Example 10-17 illustrates how you would incorporate
 `mysql_fix_string` within your own code

```php
 <?php
  require_once 'login.php';
  $conn = new mysqli($hn, $un, $pw, $db);
  if ($conn->connect_error) die("Fatal Error");
  $user  = mysql_fix_string($conn, $_POST['user']);
  $pass  = mysql_fix_string($conn, $_POST['pass']);
  $query = "SELECT * FROM users WHERE user='$user' AND pass='$pass'";
  // Etc.
  function mysql_fix_string($conn, $string)
  {
    if (get_magic_quotes_gpc()) $string = stripslashes($string);
    return $conn->real_escape_string($string);
  }
 ?>
```

