Perfect, Sakib 👏
Let’s move on to **Day 4 — Events and Handlers** in React — a *super important* topic for making your app interactive and dynamic.

We’ll go step by step 👇

---

## ⚡ **Day 4 — Events and Handlers**

### 🏷️ Topic

**Handling Events and User Interaction**

---

## 🎯 Learning Goals

By the end of this day, you’ll be able to:

* Handle user actions like clicks, typing, and form submission.
* Write event handler functions.
* Pass event handlers as props between components.
* Understand how event binding works in React.

---

## 🧠 1. What Are Events?

Events are actions performed by users — such as:

* Clicking a button 🖱️
* Typing in an input field ⌨️
* Submitting a form 📤
* Hovering over an element 🖐️

React handles events using **camelCase syntax** and **functions** (not strings like in HTML).

Example in HTML vs React:

```html
<!-- HTML -->
<button onclick="handleClick()">Click Me</button>
```

```jsx
// React
<button onClick={handleClick}>Click Me</button>
```

---

## ⚙️ 2. Handling Click Events

```jsx
import React from "react";

function App() {
  function handleClick() {
    alert("Button Clicked!");
  }

  return (
    <div>
      <h2>Day 4: Events and Handlers</h2>
      <button onClick={handleClick}>Click Me</button>
    </div>
  );
}

export default App;
```

✅ **Key Points:**

* Use **onClick** for button clicks.
* Don’t call the function directly (`handleClick()`) — just pass the reference (`handleClick`).

---

## ✍️ 3. Handling Input Events

```jsx
import React, { useState } from "react";

function InputExample() {
  const [name, setName] = useState("");

  function handleChange(event) {
    setName(event.target.value);
  }

  return (
    <div>
      <input type="text" onChange={handleChange} placeholder="Type your name" />
      <p>Hello, {name}!</p>
    </div>
  );
}

export default InputExample;
```

✅ **Key Points:**

* Use `onChange` for input fields.
* The `event` object gives access to what the user typed (`event.target.value`).

---

## 🧩 4. Passing Event Handlers as Props

You can pass event handlers **from a parent component to a child**.

```jsx
function Button({ onClick }) {
  return <button onClick={onClick}>Click Child Button</button>;
}

function Parent() {
  function handleChildClick() {
    alert("Child Button Clicked!");
  }

  return <Button onClick={handleChildClick} />;
}
```

✅ **Key Idea:**
This allows **parent components** to control **child behavior** — a key part of React’s “data flow”.

---

## 🔄 5. Handling Form Submission

React uses **onSubmit** to handle forms (not `onsubmit` like in HTML).

```jsx
function FormExample() {
  function handleSubmit(event) {
    event.preventDefault(); // stops page reload
    alert("Form Submitted!");
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" placeholder="Enter something" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

✅ **Remember:**
Always use `event.preventDefault()` to stop the page from refreshing.

---

## 🧩 6. Event Object

React passes a **synthetic event** object (a cross-browser wrapper) to handlers.

```jsx
function Example() {
  function handleClick(event) {
    console.log(event.type); // click
  }

  return <button onClick={handleClick}>Log Event Type</button>;
}
```

---

## 🧪 Mini Practice Tasks

Try these small exercises:

1. Create a button that changes text when clicked.
2. Create an input that shows live character count.
3. Build a form that alerts the name typed when submitted.

---

## 🚀 Summary

| Concept      | Event         | Example                           |
| ------------ | ------------- | --------------------------------- |
| Button Click | `onClick`     | `<button onClick={handleClick}>`  |
| Input Change | `onChange`    | `<input onChange={handleChange}>` |
| Form Submit  | `onSubmit`    | `<form onSubmit={handleSubmit}>`  |
| Mouse Over   | `onMouseOver` | `<div onMouseOver={handleHover}>` |

---

## 🎯 Now it's practice project time!

You will now learn by creating a small but important project —

### **Mini Project: Counter App**
👉 This will give you practice with `onClick` events, state updates, and handler functions.

---

### 🔹 Counter App Example

```jsx
import React, { useState } from "react";

function CounterApp() {
  const [count, setCount] = useState(0);

  function handleIncrement() {
    setCount(count + 1);
  }

  function handleDecrement() {
    setCount(count - 1);
  }

  function handleReset() {
    setCount(0);
  }

  return (
    <div style={{ textAlign: "center", marginTop: "50px" }}>
      <h2>Counter App</h2>
      <h1>{count}</h1>
      <button onClick={handleIncrement}>➕ Increase</button>
      <button onClick={handleDecrement}>➖ Decrease</button>
      <button onClick={handleReset}>🔁 Reset</button>
    </div>
  );
}

export default CounterApp;
```

---

### 🧩 Your task:

1. Run this code in a React project (e.g. in the `CounterApp.jsx` file).
2. Click each button to see the value increase, decrease, and reset.
3. Make it a more beautiful UI if you want (add colors, borders, animations).
---
