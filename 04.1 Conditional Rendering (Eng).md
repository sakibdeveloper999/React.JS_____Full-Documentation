Sakib 👏 — let’s **deep dive** into
📘 **05_Conditional_Rendering — If-else, Ternary, Logical && Rendering in React.js**
with **very detailed step-by-step explanations, diagrams (conceptually), and examples**.

---

# 🔥 React.js Conditional Rendering (Complete Tutorial)

### 📚 What You’ll Learn

In this full guide, you’ll understand:

1. What **Conditional Rendering** means in React
2. Why we use it
3. All **3 main techniques** — If-else, Ternary, Logical &&
4. Where and when to use them
5. Advanced patterns (switch, function, early return)
6. Real-world combined examples

---

## 🧩 1. What is Conditional Rendering?

In plain English:

> Conditional Rendering means **showing different UI (JSX)** depending on a **condition**.

You can think of it like “if user logged in, show dashboard; else show login form.”

In React, **you don’t show/hide HTML manually** — you let React **decide what JSX to return** based on conditions.

---

## 🧠 2. How React Handles Conditional Logic

React Components are functions that return **JSX**.

When React re-renders:

1. It calls your component function.
2. You run any logic (conditions, variables).
3. You return the JSX React should paint on screen.

Example:

```jsx
function Example() {
  const isNight = true;
  return (
    <div>
      {isNight ? <h1>Good Night 🌙</h1> : <h1>Good Morning ☀️</h1>}
    </div>
  );
}
```

React doesn’t update the DOM manually — it just re-renders **different JSX** each time.

---

## 🧱 3. Conditional Rendering Techniques

Let’s learn each one **step-by-step** with examples and notes.

---

### 🔹 A. Using `if-else` Statements

**Best for:**

* Multiple conditions
* More complex logic
* Readability

You can use it **before** your JSX `return()`.

#### Example 1 — Simple

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>Welcome back, user!</h1>;
  } else {
    return <h1>Please sign in.</h1>;
  }
}
```

#### Example 2 — Storing result in variable

```jsx
function Greeting({ isLoggedIn }) {
  let message;

  if (isLoggedIn) {
    message = "Welcome back!";
  } else {
    message = "Please sign up.";
  }

  return <h1>{message}</h1>;
}
```

✅ **Pros:**

* Easy to read
* Handles many branches (else-if)

❌ **Cons:**

* Verbose for small inline JSX changes

---

### 🔹 B. Using the Ternary Operator `condition ? expr1 : expr2`

**Best for:**

* Small, inline conditional rendering
* Two outcomes only

This is JavaScript shorthand for `if-else`.

#### Example 1 — Inline JSX

```jsx
function LoginStatus({ loggedIn }) {
  return (
    <p>{loggedIn ? "You’re logged in ✅" : "You’re logged out ❌"}</p>
  );
}
```

#### Example 2 — Conditional Button Text

```jsx
function SaveButton({ isSaving }) {
  return (
    <button disabled={isSaving}>
      {isSaving ? "Saving..." : "Save"}
    </button>
  );
}
```

✅ **Pros:**

* Compact
* Great for JSX expressions
* Easily inline inside `return()`

❌ **Cons:**

* Can reduce readability if nested too deep

💡 **Pro Tip:**
Never nest too many ternaries — it gets confusing.
If you need more than 2 outcomes, use `if/else` or `switch`.

---

### 🔹 C. Using Logical AND (`&&`)

**Best for:**

* Rendering something **only if** a condition is true
* No need for an “else” case

Works like this:

* If condition is `true` → React renders the right-hand side.
* If condition is `false` → React renders **nothing**.

#### Example 1 — Simple

```jsx
function UserGreeting({ username }) {
  return (
    <div>
      <h2>Hello!</h2>
      {username && <p>Welcome, {username}!</p>}
    </div>
  );
}
```

If `username` exists, React will render the paragraph.

If `username` is `null`, `undefined`, or `false`, React renders nothing.

#### Example 2 — Show Alert if Logged Out

```jsx
function Alert({ loggedIn }) {
  return (
    <div>
      {!loggedIn && <p className="alert">Please log in to continue</p>}
    </div>
  );
}
```

✅ **Pros:**

* Very clean for one-sided conditions
* Common in React for “optional rendering”

❌ **Cons:**

* Beware of falsy values like `0` or empty string — React might print them

#### Example of issue:

```jsx
const count = 0;
return <div>{count && <p>Items: {count}</p>}</div>; 
// outputs: 0 (not desired)
```

✅ Fix:

```jsx
return <div>{count > 0 && <p>Items: {count}</p>}</div>;
```

---

## ⚡ 4. Early Return Pattern

If your component can exit early, use `return` directly instead of nesting `if` blocks.

#### Example

```jsx
function DataStatus({ data, loading }) {
  if (loading) return <p>Loading...</p>;
  if (!data) return <p>No Data Found</p>;
  return <p>Data Loaded Successfully!</p>;
}
```

✅ Cleaner
✅ Avoids deep nesting
✅ Improves readability

---

## 🌀 5. `switch` or Map-based Conditional Rendering

**When you have many possible states (e.g., “loading”, “error”, “success”).**

### Example using `switch`

```jsx
function StatusMessage({ status }) {
  switch (status) {
    case "loading":
      return <p>Loading...</p>;
    case "error":
      return <p style={{ color: "red" }}>Error occurred!</p>;
    case "success":
      return <p style={{ color: "green" }}>Operation successful!</p>;
    default:
      return <p>Idle</p>;
  }
}
```

### Example using Map Object

```jsx
const messages = {
  loading: <p>Loading...</p>,
  error: <p>Error ❌</p>,
  success: <p>Success ✅</p>,
};

function Status({ status }) {
  return messages[status] || <p>Idle</p>;
}
```

---

## 🎨 6. Conditional CSS, Classes, and Styles

You can apply condition-based classes or styles too.

#### Example 1 — Conditional CSS Class

```jsx
function Button({ primary }) {
  return (
    <button className={`btn ${primary ? "btn-primary" : "btn-secondary"}`}>
      Click
    </button>
  );
}
```

#### Example 2 — Inline Styles

```jsx
function Alert({ type }) {
  const style = {
    color: type === "error" ? "red" : "green",
  };
  return <p style={style}>This is a message.</p>;
}
```

#### Example 3 — Using `clsx` Library

```jsx
import clsx from "clsx";

function Button({ active }) {
  return (
    <button className={clsx("btn", { "btn-active": active })}>
      Click Me
    </button>
  );
}
```

---

## 💡 7. Conditional Rendering Inside Lists

Sometimes you want to hide or show specific items when mapping.

#### Example

```jsx
function TodoList({ todos, showCompleted }) {
  return (
    <ul>
      {todos
        .filter(todo => (showCompleted ? true : !todo.completed))
        .map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
    </ul>
  );
}
```

---

## 🧮 8. Advanced Example — All Techniques Together

```jsx
import React, { useState, useEffect } from "react";

function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [showInfo, setShowInfo] = useState(false);

  // Simulate data loading
  useEffect(() => {
    setTimeout(() => {
      setUser({ name: "Sakib", age: 21, email: "sakib@example.com" });
      setLoading(false);
    }, 1000);
  }, []);

  // Early return
  if (loading) return <h2>Loading user...</h2>;
  if (!user) return <h2>No user found.</h2>;

  return (
    <div className="user-card">
      <h2>Hello, {user.name}!</h2>

      {/* Ternary */}
      <p>Status: {user.age >= 18 ? "Adult" : "Minor"}</p>

      {/* Logical && */}
      {user.email && <p>Email: {user.email}</p>}

      <button onClick={() => setShowInfo(!showInfo)}>
        {showInfo ? "Hide Details" : "Show Details"}
      </button>

      {showInfo && (
        <div className="details">
          <p>Extra user information displayed here...</p>
        </div>
      )}
    </div>
  );
}

export default UserProfile;
```

---

## 🧭 9. Choosing the Right Method

| Situation                     | Best Technique | Example                                |
| ----------------------------- | -------------- | -------------------------------------- |
| Multiple conditions           | `if` / `else`  | `if (x) return A; else return B;`      |
| Two simple outcomes           | Ternary        | `x ? A : B`                            |
| One-sided (show only if true) | `&&`           | `x && <div>Show</div>`                 |
| Many states                   | `switch` / map | `switch (status)`                      |
| Optional render early exit    | Early return   | `if (!data) return <div>No data</div>` |

---

## 🧠 10. Summary

| Technique        | Description        | Syntax                                |
| ---------------- | ------------------ | ------------------------------------- |
| **If-else**      | Full JS condition  | `if (condition) { ... } else { ... }` |
| **Ternary**      | Inline shorthand   | `{condition ? A : B}`                 |
| **Logical &&**   | Renders if true    | `{condition && A}`                    |
| **Switch / Map** | Multiple cases     | `switch (status) { ... }`             |
| **Early return** | Exit before render | `if (!data) return null;`             |

---

## 💻 Practice Challenge

Create a small React app called **“Login Panel”**:

1. If `loading` → show “Loading...”
2. If not logged in → show Login form
3. If logged in → show profile info
4. Add “Show details” button using logical &&
5. Style the active state differently using conditional class

