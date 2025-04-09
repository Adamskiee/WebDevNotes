## Concatenation
`echo "one" . "two";` // Prints: onetwo

## Creating Variables
`$my_name = "Aisle Nevertell";`

## Using Variables
``` php
$favorite_food = "Red curry with eggplant, green beans, and peanuts";
echo $favorite_food;
```

## Variable Parsing
``` php
$dog_name = "Tadpole";
$favorite_food = "salmon";
$color = "brown";

echo "I have a $color dog named $dog_name and her favorite food is $favorite_food.";
// Prints: I have a brown dog named Tadpole and her favorite food is salmon.
```

```php
$dog_name = "Tadpole";
$favorite_food = "treat";
$color = "brown";

echo "I have a ${color}ish dog named $dog_name and her favorite food is ${favorite_food}s.";
// Prints: I have a brownish dog named Tadpole and her favorite food is treats.
```

## Variable Reassignment
```php
$favorite_food = "Red curry with eggplant";
echo $favorite_food; // Prints: Red curry with eggplant

// Reassign the value of $favorite_food to a new string
$favorite_food = "Pizza"; 
echo $favorite_food; // Prints: Pizza
```

## Concatenating Assignment Operator
```php
$full_name = "Aisle";
$full_name .= " Nevertell";
echo $full_name; // Prints: Aisle Nevertell
```

## Assign by Reference
```php
$first_player_rank = "Beginner";
$second_player_rank =& $first_player_rank; 
echo $second_player_rank; // Prints: Beginner

$first_player_rank = "Intermediate"; // Reassign the value of $first_player_rank
echo $second_player_rank; // Prints: Intermediate
```

## Multiple line commands
 ``` php
<?php
  $author = "Brian W. Kernighan";
  echo <<<_END
  Debugging is twice as hard as writing the code in the first place.
  Therefore, if you write the code as cleverly as possible, you are,
  by definition, not smart enough to debug it.
  - $author.
 _END;
 ?>
```
This code tells PHP to output everything between the two END tags as if it were a double-quoted string (except that quotes in a heredoc do not need to be escaped).

## Difference between echo and print
The two commands are quite similar, but print is a function-like construct that takes a single parameter and has a return value (which is always 1), whereas echo is purely a PHP language con struct. Since both commands are constructs, neither requires parentheses. 
By and large, the `echo` command usually will be a tad faster than `print`, because it doesn’t set a return value. On the other hand, because it isn’t implemented like a function, echo cannot be used as part of a more complex expression, whereas print can. 
Here’s an example to output whether the value of a variable is TRUE or FALSE using `print`—something you could not perform in the same manner with echo, because it would display a Parse error message: ```
```
$b ? print "TRUE" : print "FALSE";
```

## Return Date
```php
<?php
  function longdate($timestamp)
  {
    return date("l F jS Y", $timestamp);
  }
  echo longdate(time());
  echo longdate(time() - 17 * 24 * 60 * 60); 
  /*which passes to longdate the current time less the number of seconds since 17 days ago (17 days × 24 hours × 60 minutes × 60 seconds).*/
?>
```