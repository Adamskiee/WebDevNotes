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

