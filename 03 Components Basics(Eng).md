
Let’s go **deep and practical** into this day’s topic step by step.

---

## 🧩 ** 3 — Components Basics**

### 🏷️ Topic

**Functional Components & Props**
Learn how to build reusable UI blocks (components) and how to pass data between them.

---

## 🧠 1. What is a Component?

A **component** is simply a JavaScript function that returns UI (usually JSX).
It can represent:

* a button, card, navbar, form, etc.
* or even a large page composed of smaller components.

👉 Think of a component as a **custom HTML tag** that you design yourself.

Example:

```jsx
function Welcome() {
  return <h2>Hello, World!</h2>;
}
```

You can then render this component inside your app:

```jsx
export default function App() {
  return (
    <div>
      <Welcome />
    </div>
  );
}
```

✅ Rule of thumb: Component names must start with a **capital letter** (React uses that to distinguish components from plain HTML elements).

---

## 🧩 2. Types of Components

In React 18+, we primarily use **Functional Components** with hooks.

Old React also had **Class Components**, but we rarely use those today.
Here we focus on **Functional Components**.

---

## 🧠 3. Props (“Properties”)

**Props** are how data flows *from parent → child components*.
They make your components dynamic and reusable.

Example:

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

📦 Each `<Welcome />` receives a `name` prop and displays it differently.

💡 You can **destructure** props for cleaner code:

```jsx
function Welcome({ name }) {
  return <h2>Hello, {name}!</h2>;
}
```

---

## ⚙️ 4. Passing Multiple Props

```jsx
function Profile({ name, age, location }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>Age: {age}</p>
      <p>Lives in: {location}</p>
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

## 🔁 5. Children Props

Sometimes you want to wrap content inside another component:

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
      <p>This is the content inside the card!</p>
    </Card>
  );
}
```

`children` is a **special prop** that includes whatever is between `<Card>…</Card>`.

---

## 🧠 6. Component Composition

React encourages you to **compose** components like building blocks:

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

✅ This is real-world React design — small components combined into larger ones.

---

## ⚡ 7. Best Practices

* Always use **PascalCase** names (`UserCard`, `ProductList`).
* Keep each component in its **own file** if it grows large.
* Pass only the **necessary props**.
* Avoid **deep prop drilling** → learn Context API (Day 11).
* Use **default props** or **PropTypes** (optional) to handle missing props.

---

## 🎯 8. Bonus Challenge (for today)

Build a **“User Profile Card”** component that accepts these props:

* `name`
* `email`
* `image`
* `joinedDate`

Show all details beautifully styled.
Then, reuse it three times for three different users.

👉 Optional: Add a small CSS hover effect.

---

## 💡 9. Common Interview Questions

1. What are React components?
2. How do props work in React?
3. What is the difference between props and state?
4. What is the `children` prop?
5. How do you make a component reusable?

---

## 📚 10. Further Learning Resources

* 📘 React Docs: [https://react.dev/learn](https://react.dev/learn)
* 🎥 YouTube: *“React Components & Props Crash Course – Traversy Media”*
* 🧩 Practice site: [https://codesandbox.io/](https://codesandbox.io/)
