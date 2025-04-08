## PHP Strings
The PHP `strlen()` function returns the length of a string.
The PHP `str_word_count()` function counts the number of words in a string.
The PHP `strpos(text, wordtofind)` function searches for a specific text within a string.
- If a match is found, the function returns the character position of the first match. If no match is found, it will return FALSE.
``` php
echo strpos("Hello world!", "world")
```
## Modify String
The `strtoupper()` function returns the string in upper case
The `strtolower()` function returns the string in lower case
The PHP `str_replace()` function replaces some characters with some other characters in a string.
```php
$x = "Hello World!";
echo str_replace("World", "Dolly", $x);
```
The PHP `strrev()` function reverses a string.
The `trim()` removes any whitespace from the beginning or the end
The PHP `explode()` function splits a string into an array.
- The first parameter of the `explode()` function represents the "separator". The "separator" specifies where to split the string.
```php
$x = "Hello World!";
$y = explode(" ", $x);

//Use the print_r() function to display the result:
print_r($y);

/*
Result:
Array ( [0] => Hello [1] => World! )
*/
```

## Slicing Strings
You can return a range of characters by using the `substr()` function.
- Specify the start index and the number of characters you want to return.
- Start the slice at index 6 and end the slice 5 positions later:
```php
$x = "Hello World!";
echo substr($x, 6, 5);
```
- Start the slice at index 6 and go all the way to the end:
```php
$x = "Hello World!";
echo substr($x, 6);
```
- Get the 3 characters, starting from the "o" in world (index -5):
```php
$x = "Hello World!";
echo substr($x, -5, 3);
```
- From the string "Hi, how are you?", get the characters starting from index 5, and continue until you reach the 3. character from the end (index -3).
- Should end up with "ow are y":
```php
$x = "Hi, how are you?";
echo substr($x, 5, -3);
```

## Escape Characters
To insert characters that are illegal in a string, use an escape character.
An escape character is a backslash `\` followed by the character you want to insert.
```php
$x = "We are the so-called \"Vikings\" from the north.";
```

