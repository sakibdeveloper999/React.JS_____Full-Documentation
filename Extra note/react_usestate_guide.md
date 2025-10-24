# 🧠 React `useState` — The Complete Practical Developer Guide

A detailed and professional developer guide to mastering React’s `useState` hook — with practical examples, wrong vs correct usage patterns, and optimization tips.

---

## 📘 Table of Contents
1. What is `useState`?
2. Syntax & Basic Usage
3. Example 1 — Counter
4. Example 2 — Todo List
5. Example 3 — Profile Editor
6. Example 4 — Timer (with useEffect)
7. Example 5 — Controlled Form
8. Common Mistakes
9. Optimization Tips
10. Summary

---

## ⚛️ 1. What is `useState`?

`useState` is a **React Hook** that lets you add state to functional components.

### ✅ Syntax
```jsx
const [state, setState] = useState(initialValue);
```

- **`state`** → current value.
- **`setState`** → function to update the state.
- **`initialValue`** → initial state (number, string, object, array, etc.).

---

## 🧮 2. Example 1 — Counter (with Double Click)

### ✅ Code
```jsx
import React, { useState } from "react";

export default function DoubleClickCounter() {
  const [count, setCount] = useState(0);

  const handleClick = () => setCount(prev => prev + 1);
  const handleDoubleClick = () => setCount(prev => prev + 2);

  return (
    <div style={{ textAlign: "center", marginTop: 50 }}>
      <h2>Count: {count}</h2>
      <button onClick={handleClick} onDoubleClick={handleDoubleClick}>
        Click / Double Click
      </button>
    </div>
  );
}
```

### 🧩 Explanation
- `useState(0)` initializes count = 0.
- Functional updater `prev => prev + 1` ensures safe update (no stale state).

### ❌ Wrong Example
```js
setCount(count + 1); // May cause incorrect value when multiple updates are batched
```

---

## 📝 3. Example 2 — Todo List (Add, Remove, Toggle)

### ✅ Code
```jsx
import React, { useState } from "react";

export default function TodoList() {
  const [todos, setTodos] = useState([{ id: 1, text: "Learn React", done: false }]);
  const [input, setInput] = useState("");

  const addTodo = () => {
    if (!input.trim()) return;
    setTodos(prev => [...prev, { id: Date.now(), text: input, done: false }]);
    setInput("");
  };

  const toggleTodo = id => {
    setTodos(prev => prev.map(todo => todo.id === id ? { ...todo, done: !todo.done } : todo));
  };

  const removeTodo = id => setTodos(prev => prev.filter(todo => todo.id !== id));

  return (
    <div style={{ textAlign: "center", marginTop: 30 }}>
      <input value={input} onChange={e => setInput(e.target.value)} placeholder="Add task..." />
      <button onClick={addTodo}>Add</button>
      <ul style={{ listStyle: "none", padding: 0 }}>
        {todos.map(t => (
          <li key={t.id}>
            <span style={{ textDecoration: t.done ? "line-through" : "none", cursor: "pointer" }} onClick={() => toggleTodo(t.id)}>
              {t.text}
            </span>
            <button onClick={() => removeTodo(t.id)}>❌</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### 🧩 Explanation
- Array state update uses **spread syntax** (`...prev`).
- `map()` and `filter()` maintain immutability.

### ❌ Wrong Example
```js
todos.push(newTodo);
setTodos(todos); // Direct mutation — React won't detect change
```

---

## 👤 4. Example 3 — Profile Editor (Nested Object)

### ✅ Code
```jsx
import React, { useState } from "react";

export default function ProfileEditor() {
  const [profile, setProfile] = useState({
    name: "Md. Sakib",
    address: { city: "Dhaka", country: "Bangladesh" },
  });

  const updateCity = newCity => {
    setProfile(prev => ({ ...prev, address: { ...prev.address, city: newCity } }));
  };

  return (
    <div style={{ margin: 50 }}>
      <h3>{profile.name}</h3>
      <p>City: {profile.address.city}</p>
      <input type="text" placeholder="New city..." onChange={e => updateCity(e.target.value)} />
    </div>
  );
}
```

### 🧩 Explanation
- Nested update requires copying each level (`...prev.address`).
- React only shallowly compares state; mutation won’t trigger re-render.

### ❌ Wrong Example
```js
profile.address.city = newCity;
setProfile(profile); // Same reference → No re-render
```

---

## ⏱ 5. Example 4 — Timer (Avoiding Stale Closure)

### ✅ Code
```jsx
import React, { useState, useEffect } from "react";

export default function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => setSeconds(s => s + 1), 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <div style={{ textAlign: "center", marginTop: 40 }}>
      <h2>⏱ Time: {seconds}s</h2>
    </div>
  );
}
```

### 🧩 Explanation
- Functional updater prevents **stale closure**.
- Cleanup (`clearInterval`) stops timer on unmount.

### ❌ Wrong Example
```js
setSeconds(seconds + 1); // stale value after first render
```

---

## 🧾 6. Example 5 — Controlled Form (Combined State)

### ✅ Code
```jsx
import React, { useState } from "react";

export default function SignupForm() {
  const [form, setForm] = useState({ name: "", email: "", password: "" });

  const handleChange = e => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = e => {
    e.preventDefault();
    alert(JSON.stringify(form, null, 2));
  };

  return (
    <form onSubmit={handleSubmit} style={{ margin: 50 }}>
      <input name="name" placeholder="Name" value={form.name} onChange={handleChange} />
      <input name="email" placeholder="Email" value={form.email} onChange={handleChange} />
      <input name="password" placeholder="Password" value={form.password} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### 🧩 Explanation
- Combined state pattern reduces duplication.
- `[name]: value` dynamically updates specific field.

---

## ⚠️ 7. Common Mistakes
| Mistake | Issue |
|----------|-------|
| Direct mutation (`state.push`) | No re-render |
| Using stale value (`setCount(count + 1)`) | Incorrect batching |
| Forgetting to copy nested objects | Partial update failure |
| Not cleaning intervals/timeouts | Memory leaks |

---

## 🚀 8. Optimization Tips
- Use **functional updater** when next state depends on previous state.
- Use **lazy initialization** for expensive defaults:
  ```js
  const [data] = useState(() => heavyCompute());
  ```
- Split unrelated states into multiple `useState` calls.
- Avoid inline object/array creation inside JSX.
- Use `useMemo` or `useCallback` to prevent unnecessary re-renders.

---

## 🧠 9. Summary
- `useState` is the simplest React Hook for managing local state.
- Always keep updates **immutable**.
- Prefer functional form when the next value depends on the old one.
- Understand how React batches state updates.

---

📦 **Next Step:** Learn `useReducer` for complex state logic — it builds directly on these principles!

