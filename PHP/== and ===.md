```php
<?php
    $a = "1000";
    $b = "+1000";
    if ($a == $b)  echo "1";
    if ($a == $b) echo "2";
?>
```
Result: 12

``` php
<?php
  $a = "1000";
  $b = "+1000";
  if ($a == $b)  echo "1";
  if ($a === $b) echo "2";
 ?>
```
result: 1

