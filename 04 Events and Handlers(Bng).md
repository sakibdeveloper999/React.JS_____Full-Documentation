চমৎকার সিদ্ধান্ত, সাকিব 👏
React শেখার চতুর্থ দিন — **Day 4: Events and Handlers (ইভেন্ট ও হ্যান্ডলার)** — এই অংশটি খুবই গুরুত্বপূর্ণ, কারণ এখান থেকেই তুমি শিখবে কিভাবে **ইউজারের অ্যাকশন** (যেমন ক্লিক, টাইপ, সাবমিট) এর মাধ্যমে React অ্যাপকে **ইন্টার‌্যাকটিভ** করা যায়।

---

## ⚡ **Day 4 — ইভেন্ট ও হ্যান্ডলার**

### 🏷️ বিষয়:

**Handling Events and User Interaction (ইভেন্ট ও ইউজার ইন্টার‌্যাকশন পরিচালনা)**

---

## 🎯 শেখার লক্ষ্য:

এই অধ্যায় শেষে তুমি শিখবে—

* ইউজারের অ্যাকশন কিভাবে হ্যান্ডেল করতে হয়
* ইভেন্ট হ্যান্ডলার ফাংশন লেখা
* প্রপসের মাধ্যমে ইভেন্ট হ্যান্ডলার পাঠানো
* ইভেন্ট বাইন্ডিং (event binding) কিভাবে কাজ করে

---

## 🧠 ১. ইভেন্ট কী?

React-এ **ইভেন্ট (Event)** মানে হলো — ইউজারের কোনো কাজ, যেমন:

* বাটনে ক্লিক করা 🖱️
* ইনপুটে কিছু লেখা ⌨️
* ফর্ম সাবমিট করা 📤
* কোনো এলিমেন্টের উপর মাউস রাখা 🖐️

React-এ ইভেন্টের নামগুলো **camelCase** আকারে লেখা হয়, যেমন —
`onclick` নয়, বরং `onClick`.

**উদাহরণ:**

```html
<!-- HTML -->
<button onclick="handleClick()">Click Me</button>
```

```jsx
// React
<button onClick={handleClick}>Click Me</button>
```

---

## ⚙️ ২. বাটন ক্লিক ইভেন্ট হ্যান্ডল করা

```jsx
import React from "react";

function App() {
  function handleClick() {
    alert("বাটনে ক্লিক করা হয়েছে!");
  }

  return (
    <div>
      <h2>Day 4: ইভেন্ট ও হ্যান্ডলার</h2>
      <button onClick={handleClick}>Click Me</button>
    </div>
  );
}

export default App;
```

✅ **মূল পয়েন্ট:**

* React-এ `onClick` ব্যবহার করা হয়।
* `handleClick()` নয়, বরং শুধু `handleClick` দিতে হয় (ফাংশনের রেফারেন্স দিতে হয়)।

---

## ✍️ ৩. ইনপুট ইভেন্ট হ্যান্ডল করা

```jsx
import React, { useState } from "react";

function InputExample() {
  const [name, setName] = useState("");

  function handleChange(event) {
    setName(event.target.value);
  }

  return (
    <div>
      <input type="text" onChange={handleChange} placeholder="তোমার নাম লিখো" />
      <p>হ্যালো, {name}!</p>
    </div>
  );
}

export default InputExample;
```

✅ **মূল পয়েন্ট:**

* ইনপুট পরিবর্তন ধরতে `onChange` ব্যবহার করা হয়।
* ইউজারের লেখা ধরতে হয় `event.target.value` দিয়ে।

---

## 🧩 ৪. প্রপসের মাধ্যমে ইভেন্ট পাঠানো

একটি কম্পোনেন্ট থেকে আরেকটিতে ইভেন্ট হ্যান্ডলার পাঠানো যায় (Parent → Child)।

```jsx
function Button({ onClick }) {
  return <button onClick={onClick}>Click Child Button</button>;
}

function Parent() {
  function handleChildClick() {
    alert("চাইল্ড বাটন ক্লিক হয়েছে!");
  }

  return <Button onClick={handleChildClick} />;
}
```

✅ **মূল ধারণা:**
Parent কম্পোনেন্ট চাইল্ডের আচরণ নিয়ন্ত্রণ করতে পারে।

---

## 🔄 ৫. ফর্ম সাবমিট হ্যান্ডল করা

React-এ ফর্ম সাবমিট করতে `onSubmit` ব্যবহার করা হয়।

```jsx
function FormExample() {
  function handleSubmit(event) {
    event.preventDefault(); // পেজ রিফ্রেশ বন্ধ করে
    alert("ফর্ম সাবমিট হয়েছে!");
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" placeholder="কিছু লিখো" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

✅ সবসময় মনে রাখবে:
`event.preventDefault()` ব্যবহার করো যাতে ফর্ম সাবমিটের সময় পেজ রিফ্রেশ না হয়।

---

## 🧩 ৬. ইভেন্ট অবজেক্ট

React-এ প্রতিটি ইভেন্টে একটি **synthetic event object** পাঠানো হয়, যাতে ইভেন্ট সম্পর্কিত তথ্য থাকে।

```jsx
function Example() {
  function handleClick(event) {
    console.log(event.type); // click
  }

  return <button onClick={handleClick}>Log Event Type</button>;
}
```

---

## 🧪 ছোট ছোট প্র্যাকটিস কাজ:

1. এমন একটি বাটন বানাও, যেটিতে ক্লিক করলে টেক্সট পরিবর্তন হবে।
2. একটি ইনপুট ফিল্ড বানাও, যা দেখাবে তুমি কয়টা অক্ষর লিখেছো।
3. একটি ফর্ম বানাও, যা সাবমিট করলে নাম দেখাবে।

---

## 🚀 সারাংশ

| বিষয়           | ইভেন্ট        | উদাহরণ                            |
| -------------- | ------------- | --------------------------------- |
| বাটন ক্লিক     | `onClick`     | `<button onClick={handleClick}>`  |
| ইনপুট পরিবর্তন | `onChange`    | `<input onChange={handleChange}>` |
| ফর্ম সাবমিট    | `onSubmit`    | `<form onSubmit={handleSubmit}>`  |
| মাউস ওভার      | `onMouseOver` | `<div onMouseOver={handleHover}>` |

---

## 🎯 এখন প্র্যাকটিস প্রোজেক্ট টাইম!

তুমি এখন শিখবে একটি ছোট কিন্তু গুরুত্বপূর্ণ প্রজেক্ট বানিয়ে —

### **Mini Project: Counter App**

👉 এটি তোমাকে `onClick` ইভেন্ট, স্টেট আপডেট, এবং হ্যান্ডলার ফাংশন প্র্যাকটিস করাবে।

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

### 🧩 তোমার কাজ:

1. এই কোডটি React প্রজেক্টে চালাও (যেমন `CounterApp.jsx` ফাইলে)।
2. প্রতিটি বাটনে ক্লিক করে দেখো মান বাড়ছে, কমছে, এবং রিসেট হচ্ছে।
3. চাইলে এটিকে আরও সুন্দর UI বানাও (রঙ, বর্ডার, অ্যানিমেশন যোগ করে)।
