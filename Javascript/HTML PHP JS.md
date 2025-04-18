Alright! Here's a different example — a **contact form** that uses:

- **HTML** to build the form,
    
- **JavaScript** to validate required fields,
    
- **PHP** to receive the data and simulate sending a message (or saving to database, etc.).
    

---

### 📩 **Scenario:**

A user fills out a contact form with their name, email, and message.

- JavaScript checks for valid input.
    
- PHP handles and displays the submitted message.
    

---

### ✅ **1. `contact.html`**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Contact Us</title>
  <script>
    function validateForm() {
      let name = document.forms["contactForm"]["name"].value;
      let email = document.forms["contactForm"]["email"].value;
      let message = document.forms["contactForm"]["message"].value;

      if (name === "" || email === "" || message === "") {
        alert("All fields are required.");
        return false;
      }
      // Basic email pattern
      let emailPattern = /^[^ ]+@[^ ]+\.[a-z]{2,3}$/;
      if (!email.match(emailPattern)) {
        alert("Enter a valid email address.");
        return false;
      }
      return true;
    }
  </script>
</head>
<body>

  <h2>Contact Us</h2>
  <form name="contactForm" action="submit_contact.php" method="post" onsubmit="return validateForm()">
    <p><input type="text" name="name" placeholder="Your Name"></p>
    <p><input type="text" name="email" placeholder="Your Email"></p>
    <p><textarea name="message" placeholder="Your Message"></textarea></p>
    <p><input type="submit" value="Send Message"></p>
  </form>

</body>
</html>
```

---

### ✅ **2. `submit_contact.php`**

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name    = htmlspecialchars($_POST["name"]);
    $email   = htmlspecialchars($_POST["email"]);
    $message = htmlspecialchars($_POST["message"]);

    echo "<h2>Thank You, $name!</h2>";
    echo "<p>We have received your message:</p>";
    echo "<blockquote>$message</blockquote>";
    echo "<p>We'll reply to your email at <strong>$email</strong> as soon as possible.</p>";
} else {
    echo "<h2>No form submission detected.</h2>";
}
?>
```

---

### 🧠 Tip:

- In a real project, the PHP could also send an email (with `mail()` function), store messages in a database, or notify admins.
    
- You can also improve security by using `filter_var()` to validate the email in PHP.
    

---

Want to make it send real emails or connect it to a database next?

Sure! Here's another practical example that combines **HTML**, **JavaScript**, and **PHP** — this time for a **simple calculator** that adds two numbers.

---

### 🧮 **Scenario:**

User enters two numbers.

- **JavaScript** checks the inputs are numbers.
    
- **PHP** performs the addition and returns the result.
    

---

### ✅ **1. `calculator.html`**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Simple Calculator</title>
  <script>
    function validateForm() {
      let num1 = document.forms["calcForm"]["num1"].value;
      let num2 = document.forms["calcForm"]["num2"].value;

      if (isNaN(num1) || isNaN(num2) || num1 === "" || num2 === "") {
        alert("Please enter valid numbers!");
        return false;
      }
      return true;
    }
  </script>
</head>
<body>

  <h2>Add Two Numbers</h2>
  <form name="calcForm" action="calculate.php" method="post" onsubmit="return validateForm()">
    <input type="text" name="num1" placeholder="First Number"> +
    <input type="text" name="num2" placeholder="Second Number">
    <input type="submit" value="Calculate">
  </form>

</body>
</html>
```

---

### ✅ **2. `calculate.php`**

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $num1 = $_POST["num1"];
    $num2 = $_POST["num2"];

    if (is_numeric($num1) && is_numeric($num2)) {
        $sum = $num1 + $num2;
        echo "<h2>Result: $num1 + $num2 = $sum</h2>";
    } else {
        echo "<h2>Invalid input. Please enter numbers only.</h2>";
    }
} else {
    echo "<h2>No data received.</h2>";
}
?>
```

---

### 🛠️ How It Works:

1. User fills out the form in `calculator.html`.
    
2. **JavaScript** ensures the inputs are numbers.
    
3. If valid, the form submits to `calculate.php`.
    
4. **PHP** adds the numbers and displays the result.
    

---

Want another one? Maybe a login system, comment box, or feedback form?

