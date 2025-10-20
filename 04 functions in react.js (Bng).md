
# ⚛️ React.js-এ Function তৈরি ও ব্যবহার করা

React.js-এ **Functions** হলো কোডের পুনঃব্যবহারযোগ্য ব্লক যা নির্দিষ্ট কাজ সম্পাদন করে। এই গাইডে আমরা দেখব কিভাবে **Functions তৈরি**, **ব্যবহার** এবং **রিয়েল-লাইফ উদাহরণে** প্রয়োগ করা যায়।  

---

## 🚀 React-এ Function কী?

**Function** হলো কোডের একটি ব্লক যা নির্দিষ্ট কাজ করে। React-এ Functions ব্যবহার করা হয়:

- **ইভেন্ট হ্যান্ডলিং** (যেমন: button click)  
- **State এবং Props পরিবর্তন**  
- **ডেটা প্রক্রিয়াকরণ** ও ফরম্যাটিং  
- JSX return করা (Functional Components এর জন্য)

---

## 🧩 React-এ Functions-এর ধরন

| ধরন | ব্যাখ্যা | উদাহরণ |
|------|----------|---------|
| **Regular Function** | সাধারণ JavaScript function | `function sayHello() {}` |
| **Arrow Function** | সংক্ষিপ্ত ও আধুনিক ফাংশন | `const sayHello = () => {}` |
| **Event Handler** | ব্যবহারকারীর action handle করা | `const handleClick = () => {}` |
| **Helper Function** | logic বা formatting করার জন্য | `const formatDate = (date) => {}` |
| **Component Function** | মূল React component function | `function App() { return <h1>Hello</h1> }` |

---

## 🧠 উদাহরণ ১ — সাধারণ Function

```jsx
import React from "react";

function Welcome() {
  function sayHello() {
    alert("হ্যালো, Md. Sakib! React-এ স্বাগতম!");
  }

  return (
    <div>
      <h2>React Function উদাহরণ</h2>
      <button onClick={sayHello}>Click করুন 👋</button>
    </div>
  );
}

export default Welcome;
````

### ব্যাখ্যা:

* `sayHello()` function component-এর ভিতরে ডিফাইন করা হয়েছে।
* button এর `onClick` event এ function attach করা হয়েছে।
* click করলে alert message দেখাবে।

---

## 💼 উদাহরণ ২ — প্রোডাক্ট ক্যালকুলেটর

```jsx
import React, { useState } from "react";

function ProductCalculator() {
  const [price, setPrice] = useState(0);
  const [quantity, setQuantity] = useState(1);

  const calculateTotal = () => {
    return price * quantity;
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>🛒 Product Calculator</h2>

      <label>Price: </label>
      <input
        type="number"
        value={price}
        onChange={(e) => setPrice(Number(e.target.value))}
      />

      <br /><br />

      <label>Quantity: </label>
      <input
        type="number"
        value={quantity}
        onChange={(e) => setQuantity(Number(e.target.value))}
      />

      <h3>Total: ${calculateTotal()}</h3>
    </div>
  );
}

export default ProductCalculator;
```

### ব্যাখ্যা:

* `calculateTotal()` function price × quantity হিসাব করে।
* JSX-এ সরাসরি ব্যবহার করা হয়েছে।
* input পরিবর্তন হলে React auto re-render করে total দেখাবে।

---

## ⚙️ উদাহরণ ৩ — Function আলাদা করা (Clean Code Practice)

```jsx
import React from "react";

function formatName(user) {
  return `${user.firstName} ${user.lastName}`;
}

function UserCard() {
  const user = { firstName: "Md.", lastName: "Sakib" };

  return (
    <div>
      <h2>👤 User Info</h2>
      <p>Name: {formatName(user)}</p>
    </div>
  );
}

export default UserCard;
```

✅ **ভালো অভ্যাস:**
Logic আলাদা function-এ রাখা কোডকে করে **সুন্দর**, **পুনঃব্যবহারযোগ্য**, এবং **টেস্টিং সহজ**।

---

## 💬 উদাহরণ ৪ — Arrow Function ইভেন্টে ব্যবহার

```jsx
import React from "react";

function Greeting() {
  return (
    <div>
      <h2>Hello Developer 👨‍💻</h2>
      <button onClick={() => alert("Welcome Back, Sakib!")}>
        Say Hi 👋
      </button>
    </div>
  );
}

export default Greeting;
```

⚠️ **টিপ:**

* সহজ logic এর জন্য inline arrow function ব্যবহার করতে পারেন।
* জটিল logic এর জন্য আলাদা function define করা ভালো।

---

## 🧠 Summary Table

| Concept         | ব্যাখ্যা                        | উদাহরণ                   |
| --------------- | ------------------------------- | ------------------------ |
| Function তৈরি   | `function` বা arrow syntax      | `function sayHello() {}` |
| Function কল করা | parenthesis দিয়ে                | `sayHello()`             |
| Event Handler   | ব্যবহারকারীর action handle করা  | `onClick={sayHello}`     |
| Data Return করা | function value return করতে পারে | `return price * qty;`    |
| JSX-এ ব্যবহার   | `{}` braces দিয়ে embed করা      | `{calculateTotal()}`     |

---

## 💡 বাস্তব জীবনের উদাহরণ

| Scenario    | Function ব্যবহার                      |
| ----------- | ------------------------------------- |
| Login form  | Input validation                      |
| To-Do App   | Task add/remove/toggle                |
| E-commerce  | Total price, discount, tax            |
| Weather App | Temperature conversion বা date format |
| Portfolio   | Button click বা animation handle করা  |

---

## 🧱 সংক্ষেপে

✅ Functions React app কে **interactive** ও **dynamic** করে।
✅ Logic এবং UI আলাদা রাখা code clean রাখে।
✅ Arrow functions দিয়ে code concise হয়।
✅ Reusable functions app-কে scalable করে।

---

### 🏁 পরবর্তী ধাপ:

> "একটি simple counter app তৈরি করুন, state এবং functions ব্যবহার করে।"

---

### 👨‍💻 Author: [**Md. Sakib**](https://github.com/sakibdeveloper999)

💻 *Full-Stack Web Developer | WordPress Expert | MERN Stack Learner*

```

---

