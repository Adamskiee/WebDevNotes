# Declaring a Class
```php
<?php
  $object = new User;
  print_r($object);
  class User
  {
    public $name, $password;
    function save_user()
    {
      echo "Save User code goes here";
    }
  }
 ?>
```

# Creating a object
``` php
$temp = new User('name', 'password');
```

# Accessing Object
``` php
<?php
    $object = new User;
    print_r($object); echo "<br>";
    $object->name     = "Joe";
    $object->password = "mypass";
    print_r($object); echo "<br>";
    $object->save_user();
    class User {
        public $name, $password;
        function save_user()
        {
          echo "Save User code goes here";
        }
      }
 ?>
```

# Cloning Object
``` php
<?php
$object1 = new User();
$object1->name = "Alice";
$object2 = $object1;
$object2->name = "Amy";
echo "object1 name = " . $object1->name . "  "; 
echo "object2 name = " . $object2->name; 
class User { 
public $name; 
} 
?>
```