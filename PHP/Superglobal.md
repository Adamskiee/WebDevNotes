Superglobal variables, which means that they are provided by the PHP environment but are global within the program, accessible absolutely everywhere.
These superglobals contain lots of useful information about the currently running program and its environment. They are structured as associative arrays.

![[Pasted image 20250409144952.png]]
![[Pasted image 20250409145004.png]]

``` php
$came_from = htmlentities($_SERVER['HTTP_REFERER'])
```
Using the `htmlentities` function for sanitization is an important practice in any circumstance where user or other third-party data is being processed for output, not just with superglobals.

