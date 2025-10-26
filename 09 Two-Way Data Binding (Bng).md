
---

# 📘 React.js — দুই-মুখী ডেটা বাইন্ডিং (Two-Way Data Binding) — সম্পূর্ণ ডকুমেন্টেশন

---

## 🧠 ১. ডেটা বাইন্ডিং কি?

**ডেটা বাইন্ডিং** মানে হলো আপনার UI (View) এবং ডেটা (Model) এর মধ্যে সংযোগ স্থাপন করা।

* ডেটা পরিবর্তিত হলে → UI আপডেট হয়।
* UI পরিবর্তিত হলে (যেমন ইউজার টাইপ করলে) → ডেটা আপডেট হয়।

প্রধান দুটি ধরন আছে:

| ধরন                 | দিকনির্দেশ                         | উদাহরণ                                 |
| ------------------- | ---------------------------------- | -------------------------------------- |
| **One-way binding** | ডেটা একদিক থেকে যায় (Model → View) | React default                          |
| **Two-way binding** | ডেটা উভয়দিকে চলে (Model ↔ View)    | Angular, Vue (default), React (manual) |

---

## ⚛️ ২. React এর ডেটা ফ্লো

React মূলত **one-way data flow** এর উপর কাজ করে।

* Parent component ডেটা child component কে **props** এর মাধ্যমে দেয়।
* Child সরাসরি parent এর ডেটা পরিবর্তন করতে পারে না — callback ব্যবহার করতে হয়।

React এ **explicit** ভাবে দুই-মুখী বাইন্ডিং করা হয়:

1. **State** দিয়ে ডেটা ধরে রাখা।
2. **Controlled components** দিয়ে UI তে state প্রদর্শন করা।
3. **Event handlers** দিয়ে UI পরিবর্তন হলে state আপডেট করা।

---

## 🔁 ৩. দুই-মুখী ডেটা বাইন্ডিং কিভাবে কাজ করে

Step-by-step:

1️⃣ ইউজার input element এ টাইপ বা interact করে।
2️⃣ `onChange` event trigger হয়।
3️⃣ Handler `setState()` বা `setValue()` কল করে model আপডেট করে।
4️⃣ React component re-render হয়।
5️⃣ নতুন `state` input এর `value` prop এ প্রতিফলিত হয়।
6️⃣ Input নতুন value দেখায়।

এভাবেই UI ↔ state synchronized থাকে।

---

## ⚙️ ৪. React এ দুই-মুখী বাইন্ডিং ইমপ্লিমেন্ট করা

### 🧩 Example ১ — Basic Text Input

```jsx
import React, { useState } from 'react';

function TwoWayBindingExample() {
  const [name, setName] = useState('');

  const handleChange = (event) => {
    setName(event.target.value);
  };

  return (
    <div className="p-5">
      <h2>Two-Way Data Binding Example</h2>
      <input
        type="text"
        value={name}
        onChange={handleChange}
        placeholder="আপনার নাম লিখুন"
      />
      <p>আপনার নাম: {name}</p>
    </div>
  );
}

export default TwoWayBindingExample;
```

Flow:

```
User typing → onChange → setName() → component re-render → input value update
```

---

### 🧩 Example ২ — Multiple Inputs (Form)

```jsx
import React, { useState } from 'react';

function UserForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    age: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
  };

  return (
    <form className="p-5">
      <h2>Two-Way Binding Form</h2>

      <label>
        Username:
        <input name="username" value={formData.username} onChange={handleChange} />
      </label>
      <br />

      <label>
        Email:
        <input name="email" value={formData.email} onChange={handleChange} />
      </label>
      <br />

      <label>
        Age:
        <input name="age" value={formData.age} onChange={handleChange} />
      </label>
      <br />

      <p>Preview:</p>
      <pre>{JSON.stringify(formData, null, 2)}</pre>
    </form>
  );
}

export default UserForm;
```

এটি দেখায় কিভাবে **একাধিক ফিল্ডের দুই-মুখী বাইন্ডিং** করা হয় — ফর্মে সবচেয়ে সাধারণ প্যাটার্ন।

---

### 🧩 Example ৩ — Checkbox ও Select Input

```jsx
function Preferences() {
  const [isDarkMode, setIsDarkMode] = useState(false);
  const [language, setLanguage] = useState("English");

  return (
    <div className="p-5">
      <h2>Preferences</h2>

      <label>
        <input
          type="checkbox"
          checked={isDarkMode}
          onChange={(e) => setIsDarkMode(e.target.checked)}
        />
        Dark Mode
      </label>

      <br />

      <select value={language} onChange={(e) => setLanguage(e.target.value)}>
        <option>English</option>
        <option>Bangla</option>
        <option>Spanish</option>
      </select>

      <p>Mode: {isDarkMode ? "Dark" : "Light"}</p>
      <p>Language: {language}</p>
    </div>
  );
}
```

* `checked` ↔ Boolean state
* `value` ↔ String state

---

### 🧩 Example ৪ — Parent ↔ Child দুই-মুখী বাইন্ডিং

```jsx
function Parent() {
  const [email, setEmail] = useState("");

  return (
    <div>
      <h2>Parent ↔ Child Two-Way Binding</h2>
      <EmailInput value={email} onChange={setEmail} />
      <p>Email from parent: {email}</p>
    </div>
  );
}

function EmailInput({ value, onChange }) {
  return (
    <input
      type="email"
      value={value}
      onChange={(e) => onChange(e.target.value)}
      placeholder="Email লিখুন"
    />
  );
}
```

✅ এই প্যাটার্নকে বলা হয় **“state lifting”** — parent state child কে data + update function দেয়।

---

## 🧰 ৫. Controlled vs Uncontrolled Components

| ধরন              | ব্যাখ্যা                                                  | উদাহরণ                        | React বাইন্ডিং টাইপ |
| ---------------- | --------------------------------------------------------- | ----------------------------- | ------------------- |
| **Controlled**   | Value React state দ্বারা নিয়ন্ত্রিত                       | `value={name}` + `onChange`   | দুই-মুখী বাইন্ডিং   |
| **Uncontrolled** | Value DOM দ্বারা নিয়ন্ত্রিত; React শুধুমাত্র submit এ পড়ে | `defaultValue="John"` + `ref` | One-way             |

**Uncontrolled Example:**

```jsx
function UncontrolledForm() {
  const nameRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Name: ${nameRef.current.value}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} defaultValue="Sakib" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

* **Controlled**: live validation, instant feedback, conditional UI
* **Uncontrolled**: শুধু submit এ value দরকার হলে

---

## 🧮 ৬. Custom Hook ব্যবহার করে Binding সহজ করা

```jsx
function useBinding(initialValue = "") {
  const [value, setValue] = useState(initialValue);
  const bind = {
    value,
    onChange: (e) => setValue(e.target.value)
  };
  return [value, bind];
}

// ব্যবহার:
function CustomBinding() {
  const [username, bindUsername] = useBinding("");
  return (
    <div>
      <input {...bindUsername} placeholder="Username লিখুন" />
      <p>{username}</p>
    </div>
  );
}
```

---

## 💡 ৭. বাস্তব জীবনের ব্যবহার (Use Cases)

| ব্যবহার         | বর্ণনা                                  |
| --------------- | --------------------------------------- |
| ✅ ফর্ম          | Registration, Login, Checkout, Feedback |
| 🔍 Search       | Live search / filter                    |
| 📝 Text editor  | Markdown preview / WYSIWYG              |
| 🎛️ Settings    | Toggle, Dropdown, Preferences           |
| 🎨 Live preview | Form preview, Data visualization        |

---

## ⚠️ ৮. সাধারণ ভুল ও সতর্কতা

1. **Controlled ↔ Uncontrolled mismatch**
   ❌ `<input value={undefined} />`
   ✅ সর্বদা initialize করুন: `useState('')`

2. **Performance issues**

   * অনেক controlled input → re-render প্রতিটি keystroke
   * Use **React.memo** / form libraries

3. **Type conversion ভুল**

   * `e.target.value` সবসময় string
   * Number বা Boolean দরকার হলে convert করুন: `Number(e.target.value)`

4. **Unnecessary re-renders**

   * Handler inline recreate করবেন না
   * Use `useCallback` when needed

5. **Validation**

   * টাইপিং block করবেন না
   * helper text দেখান

---

## 🧭 ৯. Form libraries

| Library              | Description                                     |
| -------------------- | ----------------------------------------------- |
| **Formik**           | Form state + validation সহজ করে                 |
| **React Hook Form**  | Lightweight, optimized, controlled/uncontrolled |
| **Final Form**       | Advanced form management                        |
| **Recoil / Zustand** | Global state store, multiple component binding  |

React Hook Form Example:

```jsx
import { useForm } from "react-hook-form";

function HookFormExample() {
  const { register, handleSubmit, watch } = useForm();
  const onSubmit = data => console.log(data);
  const name = watch("name");

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name")} placeholder="Name লিখুন" />
      <p>Preview: {name}</p>
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## 🧱 ১০. Performance optimization tips

✅ **Debounce** for live-search:

```js
useEffect(() => {
  const handler = setTimeout(() => console.log("Searching:", query), 500);
  return () => clearTimeout(handler);
}, [query]);
```

✅ **Memoize handlers**:

```js
const handleChange = useCallback((e) => setName(e.target.value), []);
```

✅ বড় ফর্ম re-render এড়াতে component ছোট করুন।

---

## 🧩 ১১. Example Project Structure

```
two-way-binding-demo/
│
├── src/
│   ├── components/
│   │   ├── TwoWayBindingExample.jsx
│   │   ├── UserForm.jsx
│   │   ├── Preferences.jsx
│   ├── App.jsx
│   └── index.js
│
├── package.json
└── tailwind.config.js
```

`App.jsx`:

```jsx
import TwoWayBindingExample from "./components/TwoWayBindingExample";
import UserForm from "./components/UserForm";
import Preferences from "./components/Preferences";

function App() {
  return (
    <div className="p-10">
      <TwoWayBindingExample />
      <UserForm />
      <Preferences />
    </div>
  );
}

export default App;
```

---

## 🧾 ১২. সারসংক্ষেপ

| Concept         | React Implementation                     |
| --------------- | ---------------------------------------- |
| Definition      | UI ↔ state sync                          |
| Mechanism       | Controlled components (value + onChange) |
| Core Hooks      | useState, useEffect, useCallback         |
| Cross-component | State + setter props                     |
| Common use      | Forms, search, toggles                   |
| Not recommended | Huge forms → use libraries               |
| Alternative     | Uncontrolled inputs + refs               |

---

## 🏁 ১৩. Final Takeaways

✔ React এ automatic two-way binding নেই — controlled components দিয়ে explicit ভাবে তৈরি করা হয়।
✔ State সবসময় single source of truth।
✔ Value prop + onChange handler সবসময় যুক্ত করুন।
✔ Controlled ↔ uncontrolled transition করবেন না।
✔ বড় ফর্মের জন্য form libraries ব্যবহার করুন।
✔ Expensive operations এর জন্য debounce ব্যবহার করুন।
✔ দুই-মুখী বাইন্ডিং predictability এবং reactive UI দেয়।

---

