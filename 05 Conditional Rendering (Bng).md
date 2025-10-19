💪 সাকিব!
📘 **05_Conditional_Rendering — If-else, Ternary, Logical && Rendering in React.js**
বাংলায় বিস্তারিতভাবে শিখবো।

---
### 📚 What You’ll Learn

In this full guide, you’ll understand:

1. What **Conditional Rendering** means in React
2. Why we use it
3. All **3 main techniques** — If-else, Ternary, Logical &&
4. Where and when to use them
5. Advanced patterns (switch, function, early return)
6. Real-world combined examples
---

# 🎯 React.js কন্ডিশনাল রেন্ডারিং (Conditional Rendering) — বিস্তারিত টিউটোরিয়াল

## 🔍 ১. Conditional Rendering কী?

👉 React-এ **Conditional Rendering** মানে হচ্ছে —
**কোনো শর্ত অনুযায়ী JSX (UI অংশ) দেখানো বা লুকানো।**

উদাহরণস্বরূপ:

> যদি ইউজার লগইন করা থাকে তাহলে ড্যাশবোর্ড দেখাও, না থাকলে লগইন ফর্ম দেখাও।

React DOM কে সরাসরি পরিবর্তন করে না — বরং “শর্ত অনুযায়ী” কোন JSX দেখাবে সেটা নির্ধারণ করে।

---

## ⚙️ ২. React কিভাবে কাজ করে?

React Component একটি **function** যা **JSX রিটার্ন** করে।

React যখন পুনরায় রেন্ডার হয়:

1. Component function আবার কল হয়
2. JavaScript লজিক (if, ternary, etc.) চালায়
3. JSX অনুযায়ী UI তৈরি করে দেখায়

### উদাহরণ:

```jsx
function Example() {
  const isNight = true;
  return (
    <div>
      {isNight ? <h1>শুভ রাত্রি 🌙</h1> : <h1>শুভ সকাল ☀️</h1>}
    </div>
  );
}
```

এখানে React শুধু ভিন্ন JSX রিটার্ন করছে — কোনো DOM ম্যানুয়ালি আপডেট করছে না।

---

## 🧩 ৩. Conditional Rendering করার ৩টি পদ্ধতি

---

### 🔹 (A) If-Else স্টেটমেন্ট ব্যবহার

👉 **ব্যবহার হয় যখন:**

* একাধিক শর্ত থাকে
* লজিক একটু জটিল
* রিডেবল কোড দরকার

#### উদাহরণ ১ — সাধারণ If-Else:

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>Welcome back!</h1>;
  } else {
    return <h1>Please sign in.</h1>;
  }
}
```

#### উদাহরণ ২ — Variable এ JSX রেখে রিটার্ন করা:

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

✅ **সুবিধা:**

* কোড পড়তে সহজ
* অনেকগুলো শর্ত হ্যান্ডেল করা যায়

❌ **অসুবিধা:**

* ছোট ছোট JSX পরিবর্তনের জন্য একটু লম্বা হয়

---

### 🔹 (B) Ternary Operator `condition ? expr1 : expr2`

👉 **ব্যবহার হয় যখন:**

* ২টা অবস্থায় ভিন্ন JSX দেখাতে হয়
* ইনলাইন (return এর ভেতরে) শর্ত দিতে হয়

#### উদাহরণ ১ — ইনলাইন JSX:

```jsx
function LoginStatus({ loggedIn }) {
  return (
    <p>{loggedIn ? "আপনি লগইন করেছেন ✅" : "আপনি লগআউট আছেন ❌"}</p>
  );
}
```

#### উদাহরণ ২ — বাটনের টেক্সট পরিবর্তন:

```jsx
function SaveButton({ isSaving }) {
  return (
    <button disabled={isSaving}>
      {isSaving ? "সেভ হচ্ছে..." : "সেভ"}
    </button>
  );
}
```

✅ **সুবিধা:**

* ছোট ও পরিষ্কার
* JSX এর মধ্যে ব্যবহার করা যায়

❌ **অসুবিধা:**

* Nested (ভেতরে ভেতরে) বেশি হলে পড়তে কষ্ট হয়

💡 **টিপ:**
যদি অনেকগুলো শর্ত থাকে (৩ বা তার বেশি), তাহলে if-else বা switch ব্যবহার করাই ভালো।

---

### 🔹 (C) Logical AND (`&&`) ব্যবহার

👉 **ব্যবহার হয় যখন:**

* কোনো শর্ত সত্য হলে **কিছু দেখাতে হয়**, না হলে **কিছুই না**।

#### উদাহরণ ১ — সাধারণ ব্যবহার:

```jsx
function UserGreeting({ username }) {
  return (
    <div>
      <h2>হ্যালো!</h2>
      {username && <p>স্বাগতম, {username}!</p>}
    </div>
  );
}
```

যদি `username` থাকে → JSX দেখাবে, না থাকলে কিছুই দেখাবে না।

#### উদাহরণ ২ — শর্ত উল্টো করে ব্যবহার:

```jsx
function Alert({ loggedIn }) {
  return (
    <div>
      {!loggedIn && <p className="alert">লগইন করে চালিয়ে যান</p>}
    </div>
  );
}
```

✅ **সুবিধা:**

* খুব ছোট ও পরিষ্কার
* একপাশের (একটি condition) রেন্ডারিং এর জন্য ভালো

❌ **অসুবিধা:**

* `0` বা `""` এর মতো falsy value থাকলে React ভুলভাবে 0 দেখাতে পারে

#### উদাহরণ সমস্যা:

```jsx
const count = 0;
return <div>{count && <p>Items: {count}</p>}</div>; // আউটপুট: 0 ❌
```

✅ সমাধান:

```jsx
return <div>{count > 0 && <p>Items: {count}</p>}</div>;
```

---

## 🧭 ৪. Early Return প্যাটার্ন

একাধিক শর্তের জন্য **early return** ব্যবহার করলে কোড অনেক পরিষ্কার হয়।

#### উদাহরণ:

```jsx
function DataStatus({ data, loading }) {
  if (loading) return <p>লোড হচ্ছে...</p>;
  if (!data) return <p>কোনো তথ্য পাওয়া যায়নি</p>;
  return <p>তথ্য সফলভাবে লোড হয়েছে!</p>;
}
```

✅ Nested `if` থেকে মুক্তি
✅ কোড পড়তে সহজ

---

## 🔄 ৫. Switch বা Object Mapping

যখন অনেকগুলো স্টেট বা অবস্থার জন্য আলাদা JSX দরকার হয়।

#### Example (switch):

```jsx
function StatusMessage({ status }) {
  switch (status) {
    case "loading":
      return <p>লোড হচ্ছে...</p>;
    case "error":
      return <p style={{ color: "red" }}>ত্রুটি ঘটেছে!</p>;
    case "success":
      return <p style={{ color: "green" }}>সফলভাবে সম্পন্ন ✅</p>;
    default:
      return <p>অপেক্ষায়...</p>;
  }
}
```

#### Example (Object mapping):

```jsx
const messages = {
  loading: <p>লোড হচ্ছে...</p>,
  error: <p>ত্রুটি ঘটেছে ❌</p>,
  success: <p>সফল ✅</p>,
};

function Status({ status }) {
  return messages[status] || <p>নিষ্ক্রিয়</p>;
}
```

---

## 🎨 ৬. Conditional CSS বা Style

শর্ত অনুযায়ী ক্লাস বা স্টাইল পরিবর্তন করা যায়।

#### উদাহরণ ১ — ClassName পরিবর্তন:

```jsx
function Button({ primary }) {
  return (
    <button className={`btn ${primary ? "btn-primary" : "btn-secondary"}`}>
      ক্লিক করুন
    </button>
  );
}
```

#### উদাহরণ ২ — Inline Style:

```jsx
function Alert({ type }) {
  const style = {
    color: type === "error" ? "red" : "green",
  };
  return <p style={style}>এটি একটি বার্তা।</p>;
}
```

---

## 🗂️ ৭. লিস্টে Conditional Rendering

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

## 🧮 ৮. পূর্ণাঙ্গ উদাহরণ (সব টেকনিক একসাথে)

```jsx
import React, { useState, useEffect } from "react";

function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [showInfo, setShowInfo] = useState(false);

  // ডেটা লোড সিমুলেশন
  useEffect(() => {
    setTimeout(() => {
      setUser({ name: "সাকিব", age: 21, email: "sakib@example.com" });
      setLoading(false);
    }, 1000);
  }, []);

  // Early Return
  if (loading) return <h2>লোড হচ্ছে...</h2>;
  if (!user) return <h2>কোনো ইউজার পাওয়া যায়নি।</h2>;

  return (
    <div className="user-card">
      <h2>স্বাগতম, {user.name}!</h2>

      {/* Ternary Operator */}
      <p>অবস্থা: {user.age >= 18 ? "প্রাপ্তবয়স্ক" : "অপ্রাপ্তবয়স্ক"}</p>

      {/* Logical && */}
      {user.email && <p>ইমেইল: {user.email}</p>}

      <button onClick={() => setShowInfo(!showInfo)}>
        {showInfo ? "ডিটেইলস লুকাও" : "ডিটেইলস দেখাও"}
      </button>

      {showInfo && (
        <div className="details">
          <p>অতিরিক্ত ইউজার তথ্য এখানে দেখানো হবে...</p>
        </div>
      )}
    </div>
  );
}

export default UserProfile;
```

---

## 📊 ৯. কোন ক্ষেত্রে কোন পদ্ধতি ব্যবহার করবেন?

| পরিস্থিতি                              | ব্যবহারযোগ্য টেকনিক | উদাহরণ                            |
| -------------------------------------- | ------------------- | --------------------------------- |
| অনেকগুলো শর্ত                          | `if / else`         | `if (x) return A; else return B;` |
| দুইটা ফলাফল                            | **Ternary**         | `{x ? A : B}`                     |
| শুধু সত্য হলে দেখাবে                   | **&&**              | `{x && <div>Show</div>}`          |
| একাধিক স্টেট (loading, error, success) | **switch / map**    | `switch(status)`                  |
| আগেই থামাতে চান                        | **Early return**    | `if (!data) return null;`         |

---

## 🧠 ১০. সারসংক্ষেপ

| টেকনিক           | বর্ণনা                   | সিনট্যাক্স                            |
| ---------------- | ------------------------ | ------------------------------------- |
| **If-else**      | পূর্ণ জাভাস্ক্রিপ্ট শর্ত | `if (condition) { ... } else { ... }` |
| **Ternary**      | ইনলাইন সংক্ষিপ্ত শর্ত    | `{condition ? A : B}`                 |
| **Logical &&**   | সত্য হলে দেখাবে          | `{condition && A}`                    |
| **Switch / Map** | একাধিক স্টেট হ্যান্ডেল   | `switch(status)`                      |
| **Early return** | আগে থেকেই JSX ফেরত       | `if (!data) return <div>...</div>`    |

---

## 💻 ছোট প্রজেক্ট চ্যালেঞ্জ (Practice)

একটি ছোট অ্যাপ বানাও — **Login Panel**

1. `loading` অবস্থায় “লোড হচ্ছে...” দেখাবে
2. লগইন না থাকলে Login form দেখাবে
3. লগইন থাকলে Profile info দেখাবে
4. “Show details” বাটন দিয়ে Logical && ব্যবহার করবে
5. Active state অনুযায়ী CSS ক্লাস পরিবর্তন করবে

---
