এখন চল **বাংলায়**, ধাপে ধাপে এই বিষয়টা গভীরভাবে ও ব্যবহারিকভাবে বুঝে ফেলি 👇

---

## 🧩 ** 3 — কম্পোনেন্টস বেসিকস (Components Basics)**

### 🏷️ বিষয়

**Functional Components & Props**
👉 কীভাবে পুনঃব্যবহারযোগ্য (reusable) UI অংশ তৈরি করা যায় এবং কীভাবে এক কম্পোনেন্ট থেকে আরেকটিতে ডেটা পাঠানো যায় তা শেখা।

---

## 🧠 ১. কম্পোনেন্ট কী?

একটি **Component** হচ্ছে একটি JavaScript ফাংশন যা কিছু UI (সাধারণত JSX) রিটার্ন করে।

এটি হতে পারে—

* একটি ছোট অংশ যেমন বাটন, কার্ড, ন্যাভবার, বা ফর্ম
* অথবা অনেকগুলো ছোট কম্পোনেন্ট দিয়ে তৈরি একটি বড় পেজ

👉 সহজভাবে বললে: কম্পোনেন্ট হলো তোমার নিজের তৈরি **custom HTML ট্যাগ**।

উদাহরণ:

```jsx
function Welcome() {
  return <h2>Hello, World!</h2>;
}
```

এখন তুমি এটা `App` কম্পোনেন্টে ব্যবহার করতে পারো:

```jsx
export default function App() {
  return (
    <div>
      <Welcome />
    </div>
  );
}
```

✅ নিয়ম: কম্পোনেন্টের নাম অবশ্যই **বড় হাতের অক্ষরে (Capital Letter)** শুরু হতে হবে।
(React এভাবেই HTML এলিমেন্ট আর কম্পোনেন্ট আলাদা করে চিনে।)

---

## 🧩 ২. কম্পোনেন্টের ধরন

React 18+ ভার্সনে আমরা মূলত **Functional Components** ব্যবহার করি।

আগের ভার্সনগুলোতে **Class Components** জনপ্রিয় ছিল, কিন্তু এখন প্রায় সবাই Functional Components-ই ব্যবহার করে।

---

## 🧠 ৩. Props (“Properties”)

**Props** হচ্ছে React-এ *parent → child component* এর মধ্যে ডেটা পাঠানোর মাধ্যম।

👉 এটি তোমার কম্পোনেন্টকে **dynamic এবং reusable** করে।

উদাহরণ:

```jsx
function Welcome(props) {
  return <h2>Hello, {props.name}!</h2>;
}

export default function App() {
  return (
    <div>
      <Welcome name="Sakib" />
      <Welcome name="React Learner" />
    </div>
  );
}
```

এখানে `<Welcome />` কম্পোনেন্টে `name` নামে props পাঠানো হয়েছে।

💡 কোড আরও পরিষ্কার করতে props **destructure** করা যায়:

```jsx
function Welcome({ name }) {
  return <h2>Hello, {name}!</h2>;
}
```

---

## ⚙️ ৪. একাধিক Props পাঠানো

```jsx
function Profile({ name, age, location }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>বয়স: {age}</p>
      <p>অবস্থান: {location}</p>
    </div>
  );
}

export default function App() {
  return (
    <Profile name="Md. Sakib" age={24} location="Bangladesh" />
  );
}
```

---

## 🔁 ৫. Children Props

কখনও কখনও তুমি চাও এক কম্পোনেন্টের ভিতরে অন্য কনটেন্ট রাখতে — তখন ব্যবহার করো **children props**।

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
}

export default function App() {
  return (
    <Card title="My Card">
      <p>এটি কার্ডের ভিতরের কনটেন্ট!</p>
    </Card>
  );
}
```

👉 `children` হলো একটি **বিশেষ prop** যা `<Card>...</Card>` ট্যাগের ভিতরের কনটেন্ট ধরে রাখে।

---

## 🧠 ৬. Component Composition

React-এ তুমি ছোট ছোট কম্পোনেন্ট জোড়া দিয়ে বড় কিছু তৈরি করতে পারো — এটিই **component composition**।

```jsx
function Avatar({ url }) {
  return <img src={url} width={80} alt="avatar" />;
}

function UserCard({ name, avatar }) {
  return (
    <div>
      <Avatar url={avatar} />
      <h4>{name}</h4>
    </div>
  );
}

export default function App() {
  return (
    <UserCard name="Sakib" avatar="https://i.pravatar.cc/80" />
  );
}
```

✅ এভাবেই বাস্তবে React অ্যাপ তৈরি করা হয় — ছোট ছোট কম্পোনেন্ট একত্রে ব্যবহার করে বড় ফিচার বানানো হয়।

---

## ⚡ ৭. ভালো প্র্যাকটিস

* কম্পোনেন্টের নাম সবসময় **PascalCase** রাখো (যেমন `UserCard`, `ProductList`)
* বড় হলে প্রতিটি কম্পোনেন্ট **নিজস্ব ফাইল**ে রাখো
* শুধু **প্রয়োজনীয় props** পাঠাও
* **Deep prop drilling** এড়াও (→ Day 11 এ শিখবে Context API)
* **Default props** বা **PropTypes** ব্যবহার করতে পারো (missing props হ্যান্ডেল করতে)

---

## 🎯 ৮. বোনাস চ্যালেঞ্জ (আজকের জন্য)

একটি **User Profile Card** কম্পোনেন্ট বানাও যা নিচের props নেবে:

* `name`
* `email`
* `image`
* `joinedDate`

এই তথ্যগুলো সুন্দরভাবে দেখাও, এবং তিনজন ভিন্ন ইউজারের জন্য কার্ডটি পুনঃব্যবহার করো।

👉 চাইলে CSS hover effect যোগ করো।

---

## 💡 ৯. সাধারণ ইন্টারভিউ প্রশ্ন

1. React কম্পোনেন্ট কী?
2. Props কীভাবে কাজ করে?
3. Props আর state এর মধ্যে পার্থক্য কী?
4. `children` prop কী করে?
5. একটি কম্পোনেন্টকে কীভাবে reusable করা যায়?

---

## 📚 ১০. অতিরিক্ত শেখার রিসোর্স

* 📘 React অফিসিয়াল ডকস: [https://react.dev/learn](https://react.dev/learn)
* 🎥 ইউটিউব: *“React Components & Props Crash Course – Traversy Media”*
* 🧩 প্র্যাকটিস সাইট: [https://codesandbox.io/](https://codesandbox.io/)

---