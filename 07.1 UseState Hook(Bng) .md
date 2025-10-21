
---

# **07 — useState Hook (সম্পূর্ণ বাংলায় গাইড)**

`useState` হলো **React-এর সবচেয়ে গুরুত্বপূর্ণ Hook** যা functional components-এ **state** পরিচালনার জন্য ব্যবহৃত হয়।

---

## **১️⃣ useState কি?**

* `useState` functional component-কে state ধারণ করার ক্ষমতা দেয়।
* সিনট্যাক্স:

```js
const [state, setState] = useState(initialValue);
```

* `state` → বর্তমান মান
* `setState` → মান আপডেট করার ফাংশন
* `initialValue` → primitive, object, array বা lazy function হতে পারে

---

## **২️⃣ বেসিক কাউন্টার উদাহরণ**

```jsx
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prev => prev + 1);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+1</button>
      <button onClick={() => setCount(0)}>রিসেট</button>
    </div>
  );
}

export default Counter;
```

**মুখ্য বিষয়:**

* Functional updates (`prev => prev + 1`) ব্যবহার করলে **stale value সমস্যা** এড়ানো যায়।
* State update করলে component **automatic re-render** হয়।

---

## **৩️⃣ State ধরনের ব্যবহার**

### **Primitive**

```js
const [name, setName] = useState("Sakib");
```

### **Object**

```js
const [user, setUser] = useState({ name: "Sakib", age: 25 });
setUser(prev => ({ ...prev, age: 26 })); // সঠিকভাবে আপডেট
```

### **Array**

```js
const [items, setItems] = useState([]);
setItems(prev => [...prev, "নতুন আইটেম"]); // যোগ
setItems(prev => prev.filter(item => item !== "পুরনো আইটেম")); // বাদ
```

---

## **৪️⃣ Controlled Inputs**

```jsx
function NameForm() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}
      onChange={e => setName(e.target.value)}
      placeholder="আপনার নাম লিখুন"
    />
  );
}
```

**মুখ্য বিষয়:**

* React state হলো **input-এর একমাত্র truth source**।
* Controlled inputs `<input>`, `<textarea>`, `<select>`-এর জন্য প্রযোজ্য।

---

## **৫️⃣ Lazy Initialization**

* যদি initial state-এর গণনা (computation) ব্যয়বহুল হয়, তাহলে ফাংশন ব্যবহার করুন:

```js
const [data, setData] = useState(() => expensiveComputation());
```

* ফাংশনটি **শুধুমাত্র প্রথম render**-এ run হয়।

---

## **৬️⃣ Functional Updates**

* নতুন state যদি আগের state-এর উপর নির্ভর করে, functional update ব্যবহার করুন:

```js
setCount(prev => prev + 1);
```

* এটি stale closure সমস্যা এড়ায়।

---

## **৭️⃣ Edge Cases & সতর্কতা**

1. **State mutate করবেন না**

   * ❌ ভুল: `user.age = 30; setUser(user);`
   * ✅ সঠিক: `setUser(prev => ({ ...prev, age: 30 }))`

2. **Asynchronous updates**

   * `setState` তাৎক্ষণিক নয়, এটি re-render-এর জন্য schedule করে।

3. **Multiple updates**

   * Functional updates ব্যবহার করলে rapid updates-এ কোনো পরিবর্তন হারায় না।

4. **Derived state**

   * যে মান অন্য state থেকে হিসাব করা যায়, তা আলাদা state হিসেবে রাখবেন না।

---

## **৮️⃣ Object & Array State আপডেট**

### Object State

```js
const [user, setUser] = useState({ name: "", age: 0 });
setUser(prev => ({ ...prev, name: "Sakib" }));
```

### Array State

```js
const [todos, setTodos] = useState([]);
setTodos(prev => [...prev, { id: 1, text: "React শেখা" }]);
setTodos(prev => prev.filter(t => t.id !== 1));
```

---

## **৯️⃣ Multiple States ব্যবহার**

* একাধিক independent state রাখতে পারি:

```js
const [count, setCount] = useState(0);
const [name, setName] = useState("");
```

* এটি বড় object state রাখার চেয়ে পরিষ্কার।

---

## **🔟 উদাহরণ: Todo App**

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

## **১১️⃣ Quick Tips & Best Practices**

* State **সর্বদা minimal রাখুন**।
* Functional updates ব্যবহার করুন যদি আগের state-এর উপর নির্ভর করে।
* Derived/computed values state হিসেবে রাখবেন না।
* Independent fields-এর জন্য **multiple useState** ব্যবহার করুন।
* State logic জটিল হলে `useReducer` ব্যবহার করুন।

---

✅ **এই গাইডটি সম্পূর্ণভাবে `useState` সম্পর্কে সব কিছু অন্তর্ভুক্ত করে:**

* বেসিক ও অ্যাডভান্সড ব্যবহার
* Primitive, Object, Array state
* Controlled Inputs
* Lazy Initialization
* Functional Updates
* Edge Cases & Best Practices
* পূর্ণ Todo App উদাহরণ

---
