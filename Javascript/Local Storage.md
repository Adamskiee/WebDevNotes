
Working with **Local Storage** in JavaScript is super useful when you want to store data **on the user's browser**, like keeping login status, saving form input, or user preferences — and it **doesn’t disappear when the browser is closed**.

---

### 🧠 What is Local Storage?

- Part of the Web Storage API.
    
- Stores data as **key-value pairs**.
    
- Can store **only strings** (but you can store JSON by converting objects to strings).
    

---

### 🛠️ Basic Usage

#### 🔹 Set an item:

```javascript
localStorage.setItem("username", "john_doe");
```

#### 🔹 Get an item:

```javascript
const user = localStorage.getItem("username");
console.log(user); // Outputs: john_doe
```

#### 🔹 Remove an item:

```javascript
localStorage.removeItem("username");
```

#### 🔹 Clear all storage:

```javascript
localStorage.clear();
```

---

### 🧩 Store JavaScript objects (use JSON)

```javascript
const user = { name: "John", age: 25 };
localStorage.setItem("user", JSON.stringify(user)); // Save

const storedUser = JSON.parse(localStorage.getItem("user")); // Retrieve
console.log(storedUser.name); // John
```

---

### ✅ Example: Remember Username on Login Form

```html
<!-- login.html -->
<form onsubmit="saveUsername()">
  <input type="text" id="username" placeholder="Enter username" required />
  <button type="submit">Login</button>
</form>

<script>
  // Load saved username if exists
  document.getElementById("username").value = localStorage.getItem("savedUsername") || "";

  function saveUsername() {
    const username = document.getElementById("username").value;
    localStorage.setItem("savedUsername", username);
  }
</script>
```

---

### ⚠️ Notes:

- Local Storage is **not secure** for storing sensitive data like passwords or tokens.
    
- It works **only in the browser**, not in PHP.
    

---

### 🔤 HTML Part

```html
<form onsubmit="saveUsername()">
  <input type="text" id="username" placeholder="Enter username" required />
  <button type="submit">Login</button>
</form>
```

- `<form>`: Creates a login form.
    
- `onsubmit="saveUsername()"`: Calls the `saveUsername()` function **when the form is submitted**.
    
- `<input>`: Text field where the user enters their username.
    
    - `id="username"`: Gives this input an ID so JavaScript can reference it.
        
    - `required`: Makes sure the user can't submit the form if the field is empty.
        
- `<button>`: Submits the form.
    

---

### 🧠 JavaScript Part

```javascript
// Load saved username if exists
document.getElementById("username").value = localStorage.getItem("savedUsername") || "";
```

- This line runs **as soon as the page loads**.
    
- It checks local storage:  
    `localStorage.getItem("savedUsername")`
    
    - If it **exists**, it fills in the input box with that value.
        
    - If not, it fills it with an empty string (`""`).
        
- This is useful to **remember the last entered username** even after refreshing or reopening the page.
    

---

```javascript
function saveUsername() {
  const username = document.getElementById("username").value;
  localStorage.setItem("savedUsername", username);
}
```

- `saveUsername()` is called when the user clicks the "Login" button.
    
- It:
    
    1. Gets the value the user typed in the input field.
        
    2. Saves it to **local storage** using the key `"savedUsername"`.
        

---

### ✅ Overall Behavior:

1. You visit the page and type your username.
    
2. When you click "Login", the form saves your username to the browser using local storage.
    
3. The next time you open or refresh the page, your username is automatically filled in.
    

---

Let me know if you want this to also prevent the page from reloading or if you want a version using **sessionStorage** instead!