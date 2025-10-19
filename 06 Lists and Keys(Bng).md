
---

# 🔢 **React Lists & Keys (রেন্ডারিং লিস্ট ও কীস)**

---

## 🧩 ১) **React এ List কী?**

React এ আমরা যখন একাধিক একই ধরণের আইটেম (যেমন নামের তালিকা, কার্ড, টেবিলের সারি ইত্যাদি) দেখাতে চাই — তখন আমরা **array.map()** ব্যবহার করি।

```jsx
const fruits = ['Apple', 'Banana', 'Cherry'];

return (
  <ul>
    {fruits.map(fruit => <li>{fruit}</li>)}
  </ul>
);
```

👉 এটা ৩টা `<li>` রেন্ডার করবে।
কিন্তু React একটা সতর্কবার্তা দিবে —
**“Each child in a list should have a unique key prop.”**

এবার বুঝি এই **key** জিনিসটা কী।

---

## 🔑 ২) **Key কী এবং কোথায় ব্যবহার হয়?**

`key` হলো React এর একটি **বিশেষ prop** যেটা React কে সাহায্য করে কোন এলিমেন্ট পরিবর্তন হয়েছে, কোনটা একই আছে, কোনটা নতুন — সেটা বুঝতে।

`key` সবসময় **map() এর ভিতরে রিটার্ন করা টপ লেভেল এলিমেন্টে** দিতে হয়।

```jsx
{items.map(item => (
  <div key={item.id}>
    {item.name}
  </div>
))}
```

👉 `key` প্রতিটি আইটেমের জন্য **unique** হতে হবে (একই লিস্টের মধ্যে)।

---

## ⚙️ ৩) **React কেন key ব্যবহার করে? (Reconciliation)**

React যখন কোনো লিস্ট আপডেট করে, তখন সে আগের লিস্টের সঙ্গে নতুন লিস্ট মিলিয়ে দেখে — কোনটা **add/remove/update** হয়েছে।

এই সময় `key` React কে বলে দেয় —

> “এই এলিমেন্টটা আগেরটার সমান না ভিন্ন।”

🔹 এর ফলে:

* React DOM এ কম কাজ করে (ফাস্ট রেন্ডার হয়)
* এলিমেন্টের state ঠিক থাকে
* অপ্রয়োজনীয় রি-রেন্ডার এড়ানো যায়

---

## ✅ ৪) **ভালো key বনাম খারাপ key**

| ভালো key            | খারাপ key                            |
| ------------------- | ------------------------------------ |
| item.id (স্থায়ী id) | array index (0,1,2,3...)             |
| ডাটাবেসের id        | Math.random() (প্রতি রেন্ডারে বদলায়) |

👉 **index key** তখনই ব্যবহার করা যাবে যদি লিস্ট কখনও **reorder, add, remove না হয়।**

---

## 🧠 ৫) **সঠিক উদাহরণ**

```jsx
// App.jsx
import React from 'react';

const todos = [
  { id: 1, text: 'Buy milk' },
  { id: 2, text: 'Walk dog' },
  { id: 3, text: 'Write notes' }
];

export default function App() {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

---

## ⚠️ ৬) **ভুল উদাহরণ (index key এর সমস্যা)**

```jsx
// BadExample.jsx
import React, { useState } from 'react';

export default function BadExample() {
  const [items, setItems] = useState([
    { id: 'a', text: 'Alpha' },
    { id: 'b', text: 'Beta' },
    { id: 'c', text: 'Gamma' }
  ]);

  const removeFirst = () => setItems(prev => prev.slice(1));

  return (
    <div>
      <button onClick={removeFirst}>Remove first item</button>
      {items.map((item, index) => (
        <div key={index}>
          <input defaultValue={item.text} />
        </div>
      ))}
    </div>
  );
}
```

🧩 এখন যদি তুমি দ্বিতীয় ইনপুটে টাইপ করো আর প্রথম আইটেম ডিলিট করো, তাহলে দেখা যাবে ইনপুট ভুলভাবে শিফট হয়ে গেছে।
👉 কারণ React index key দেখে ভেবেছে “এই ইনপুট তো একই, শুধু টেক্সট বদলেছে!”

---

## 🔧 ৭) **সঠিক সমাধান (স্থায়ী id ব্যবহার করে)**

```jsx
// GoodExample.jsx
import React, { useState } from 'react';

export default function GoodExample() {
  const [items, setItems] = useState([
    { id: 'a', text: 'Alpha' },
    { id: 'b', text: 'Beta' },
    { id: 'c', text: 'Gamma' }
  ]);

  const removeFirst = () => setItems(prev => prev.slice(1));

  return (
    <div>
      <button onClick={removeFirst}>Remove first item</button>
      {items.map(item => (
        <div key={item.id}>
          <input defaultValue={item.text} />
        </div>
      ))}
    </div>
  );
}
```

👉 এখন React প্রতিটি ইনপুটের DOM ঠিকভাবে ধরে রাখবে।

---

## 🧩 ৮) **Key কোথায় দিতে হয়**

`key` সবসময় **map() এর ভিতরে রিটার্ন করা element এ** দিতে হবে।

```jsx
// ✅ সঠিক
{todos.map(todo => <ListItem key={todo.id} todo={todo} />)}

// ❌ ভুল
{todos.map(todo => <ListItem todo={todo} />)} // ভিতরে key দিলে কাজ হবে না
```

---

## 🧱 ৯) **Fragment এর key**

একটা আইটেম একাধিক element রিটার্ন করলে `React.Fragment` ব্যবহার করা যায়:

```jsx
{items.map(item => (
  <React.Fragment key={item.id}>
    <dt>{item.term}</dt>
    <dd>{item.desc}</dd>
  </React.Fragment>
))}
```

---

## 🧩 ১০) **Key তৈরি করার নিয়ম**

* 🔹 ব্যাকএন্ডের থেকে পাওয়া id ব্যবহার করো
* 🔹 client side এ id বানাতে হলে (যেমন `uuid`), সেটা item তৈরির সময় একবারই বানাও
* ❌ প্রতি render এ `Math.random()` বা `Date.now()` ব্যবহার করো না

---

## 💡 ১১) **Key পরিবর্তন হলে কী হয়**

একটা কম্পোনেন্টের key যদি বদলে যায়, React ওটা **unmount করে নতুন করে mount করে**।
👉 ফলে ওই কম্পোনেন্টের **state হারিয়ে যায়**।

তাই key যেন অকারণে বদলানো না হয়।

---

## ⚡ ১২) **পারফরম্যান্স টিপস**

* সঠিক key দিলে React কম DOM পরিবর্তন করে (ফাস্টার আপডেট)
* key পরিবর্তন করলে React ভাবে “নতুন এলিমেন্ট এসেছে”
* বড় লিস্টে performance এর জন্য `react-window` বা `react-virtualized` ব্যবহার করা যায়

---

## ✅ ১৩) **চেকলিস্ট (যাচাই করে নাও)**

| কাজ                                    | হ্যাঁ/না |
| -------------------------------------- | -------- |
| map() এর প্রতিটি আইটেমে key দেওয়া আছে? | ✅        |
| key কি unique এবং stable?              | ✅        |
| index key কি ব্যবহার করা হয়েছে?        | ❌        |
| key কি প্রতি render এ নতুন হয় না?      | ✅        |

---

## 📘 ১৪) **ছোট উদাহরণে ভালো বনাম খারাপ key**

### ✅ ভালো

```jsx
{people.map(p => (
  <PersonCard key={p.id} person={p} />
))}
```

### ⚠️ index শুধুমাত্র static হলে

```jsx
{['A','B','C'].map((v, i) => <li key={i}>{v}</li>)}
```

### ❌ খারাপ (dynamic list)

```jsx
{todos.map((t, i) => <Todo key={i} todo={t} />)}
```

---

## 🎯 সারসংক্ষেপ

| বিষয়                 | ব্যাখ্যা                                       |
| -------------------- | ---------------------------------------------- |
| **List**             | array.map() দিয়ে এলিমেন্ট রেন্ডার করা          |
| **Key**              | React কে প্রতিটি এলিমেন্ট আলাদা করে চেনার উপায় |
| **সঠিক key**         | item.id বা স্থায়ী unique মান                   |
| **index key সমস্যা** | reorder/add/remove করলে ভুল DOM আপডেট হয়       |
| **state সমস্যা**     | key বদলালে কম্পোনেন্ট নতুন হয়ে যায়             |
| **Performance**      | key ঠিক থাকলে React দ্রুত কাজ করে              |

---

