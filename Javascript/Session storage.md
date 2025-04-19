Great follow-up! Let's talk about **Session Storage**, which is **very similar** to Local Storage — but with a key difference in how long the data lasts.

---

## 📦 What is Session Storage?

- Part of the Web Storage API (like Local Storage).
    
- Stores data as **key-value pairs**, just like Local Storage.
    
- BUT: **It only lasts for the duration of the browser tab/session**.
    
    - Close the tab? Data is gone.
        
    - Refresh the page? Data is still there.
        
    - Open a new tab? It has a **different session**.
        

---

## 🧠 When to use it

Use **Session Storage** when you:

- Want to store **temporary data** (e.g., form progress, page status).
    
- Don’t want that data to persist after the user closes the tab.
    

---

## 🛠️ Basic Syntax

### 🔹 Set an item:

```javascript
sessionStorage.setItem("username", "john_doe");
```

### 🔹 Get an item:

```javascript
let user = sessionStorage.getItem("username");
console.log(user); // Outputs: john_doe
```

### 🔹 Remove an item:

```javascript
sessionStorage.removeItem("username");
```

### 🔹 Clear all session storage:

```javascript
sessionStorage.clear();
```

---

## 🧪 Example: Store temporary login message

```html
<!-- index.html -->
<body onload="showWelcome()">
  <h1 id="welcomeMsg"></h1>
  <script>
    function showWelcome() {
      const name = sessionStorage.getItem("username");
      if (name) {
        document.getElementById("welcomeMsg").innerText = "Welcome, " + name + "!";
      }
    }

    // Simulate login
    sessionStorage.setItem("username", "John");
  </script>
</body>
```

> Close the tab, reopen the file? The message disappears — that’s session storage.

---

## ⚖️ Session Storage vs Local Storage

|Feature|`sessionStorage`|`localStorage`|
|---|---|---|
|Data lifespan|Until tab is closed|Until manually deleted|
|Shared across tabs?|❌ No|✅ Yes|
|Capacity|~5MB|~5–10MB|
|Use case|Temporary/session data|Persistent data|

---

Want me to make a full example combining form + session storage + clear button?