You can still use the same `input` event listener like before — placing the `<input>` inside a `<form>` doesn't change that behavior. But if you're using a **submit button**, you might want to handle it separately.

---

### 🧪 Example: Username inside a form

#### 🧾 `index.php` or `index.html`

```html
<form id="signup-form">
  <label>Username:</label>
  <input type="text" id="username" name="username">
  <span id="result"></span><br>

  <label>Password:</label>
  <input type="password" name="password"><br>

  <input type="submit" value="Register">
</form>

<script>
document.getElementById("username").addEventListener("input", function () {
  let username = this.value;

  fetch("check_username.php", {
    method: "POST",
    headers: {
      "Content-Type": "application/x-www-form-urlencoded"
    },
    body: "username=" + encodeURIComponent(username)
  })
  .then(res => res.text())
  .then(data => {
    document.getElementById("result").innerText = data;
  });
});

// Optional: prevent form from submitting if username is taken
document.getElementById("signup-form").addEventListener("submit", function(e) {
  const resultText = document.getElementById("result").innerText;
  if (resultText.includes("taken")) {
    e.preventDefault(); // stop form submission
    alert("Please choose another username.");
  }
});
</script>
```

---

### ✅ What’s happening:

- The form includes a username and password field.
    
- As the user **types** in the username field:
    
    - JavaScript checks with PHP if the username exists in the database.
        
    - The result is shown in real time (`<span id="result">`).
        
- On form **submit**:
    
    - It checks if the username was marked as "taken".
        
    - If so, it **cancels submission** using `e.preventDefault()`.
        

---

### 📌 Notes:

- You can add more validation rules (like for password length, email format, etc.).
    
- You should still validate again in **PHP after form submission**, because client-side JS can be bypassed.
    

---
