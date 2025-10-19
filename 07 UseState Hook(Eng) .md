
---

# **07 — useState Hook in React.js (Full Guide)**

`useState` is the **most fundamental React Hook** for managing state in functional components.

---

## **1️⃣ What is `useState`?**

* `useState` allows **functional components** to hold state (like class components’ `this.state`).
* Syntax:

```js
const [state, setState] = useState(initialValue);
```

* `state` → current value
* `setState` → function to update the value
* `initialValue` → can be primitive, object, array, or lazy initializer function

---

## **2️⃣ Basic Counter Example**

```jsx
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+1</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}

export default Counter;
```

**Notes:**

* Functional updates (`prev => prev + 1`) are safer than `setCount(count + 1)` when multiple updates happen quickly.
* State update triggers a **re-render**.

---

## **3️⃣ State Types**

### **Primitive**

* Numbers, strings, booleans

```js
const [name, setName] = useState("Sakib");
```

### **Object**

* Use spread operator to avoid mutation:

```js
const [user, setUser] = useState({ name: "Sakib", age: 25 });
setUser(prev => ({ ...prev, age: 26 })); // Update safely
```

### **Array**

* Arrays should also be immutable:

```js
const [items, setItems] = useState([]);
setItems(prev => [...prev, "New item"]); // Add
setItems(prev => prev.filter(item => item !== "Remove")); // Remove
```

---

## **4️⃣ Controlled Inputs**

* React state is **single source of truth** for inputs:

```jsx
function NameForm() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}
      onChange={e => setName(e.target.value)}
      placeholder="Type your name"
    />
  );
}
```

* Works for `<input>`, `<textarea>`, `<select>`.

---

## **5️⃣ Lazy Initialization**

* For expensive initial state computations, pass a function to `useState`:

```js
const [data, setData] = useState(() => computeExpensiveValue());
```

* Function runs **only once** during first render.

---

## **6️⃣ Functional Updates**

* Essential when new state depends on previous state:

```js
setCount(prev => prev + 1);
```

* Avoid stale closure issues.

---

## **7️⃣ Edge Cases & Pitfalls**

1. **Never mutate state directly**:

   * Wrong: `user.age = 30; setUser(user);`
   * Correct: `setUser(prev => ({ ...prev, age: 30 }))`

2. **Asynchronous updates**

   * `setState` does not update immediately; it schedules a re-render.

3. **Multiple updates**

   * Use functional updates to avoid losing changes in rapid updates.

4. **Derived state**

   * Avoid storing values that can be computed from other state.

---

## **8️⃣ Updating Object & Array States**

### **Object State**

```js
const [user, setUser] = useState({ name: "", age: 0 });
setUser(prev => ({ ...prev, name: "Sakib" }));
```

### **Array State**

```js
const [todos, setTodos] = useState([]);
setTodos(prev => [...prev, { id: 1, text: "Learn React" }]);
setTodos(prev => prev.filter(todo => todo.id !== 1));
```

---

## **9️⃣ Using Multiple States**

* You can use multiple `useState` hooks for separate pieces of state:

```js
const [count, setCount] = useState(0);
const [name, setName] = useState("");
```

* Recommended over a single large object when fields are unrelated.

---

## **🔟 Example: Todo App Using useState**

```jsx
import React, { useState } from "react";

function TodoApp() {
  const [text, setText] = useState("");
  const [todos, setTodos] = useState([]);

  const addTodo = e => {
    e.preventDefault();
    if (!text.trim()) return;
    const newTodo = { id: Date.now(), text: text.trim(), done: false };
    setTodos(prev => [...prev, newTodo]);
    setText("");
  };

  const toggleDone = id => {
    setTodos(prev => prev.map(t => t.id === id ? { ...t, done: !t.done } : t));
  };

  const removeTodo = id => {
    setTodos(prev => prev.filter(t => t.id !== id));
  };

  return (
    <div>
      <h3>Todo App</h3>
      <form onSubmit={addTodo}>
        <input value={text} onChange={e => setText(e.target.value)} />
        <button type="submit">Add</button>
      </form>

      <ul>
        {todos.map(t => (
          <li key={t.id}>
            <input type="checkbox" checked={t.done} onChange={() => toggleDone(t.id)} />
            {t.text}
            <button onClick={() => removeTodo(t.id)}>Remove</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoApp;
```

---

## **1️⃣1️⃣ Quick Tips & Best Practices**

* Keep state **minimal**.
* Use **functional updates** when dependent on previous state.
* Avoid storing **derived/computed values** in state.
* Split multiple independent states into **multiple `useState` hooks**.
* Use `useReducer` when state logic becomes complex.

---

✅ **This covers absolutely everything about `useState` in React.js**:

* Basic & advanced usage
* Primitive, object, array state
* Controlled inputs
* Lazy initialization
* Functional updates
* Edge cases & best practices
* Full example (Todo App)

---

