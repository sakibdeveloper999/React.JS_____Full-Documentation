
---

# 🧠 React.js — Form Handling & Controlled Components (পূর্ণ বিস্তারিত বাংলা ডকুমেন্টেশন)

---

## ⚙️ ১️⃣ React-এ Form Handling কী?

HTML-এ `<input>`, `<textarea>`, `<select>` এই এলিমেন্টগুলো নিজেরাই তাদের মান (value) ম্যানেজ করে।
কিন্তু **React** চায় UI-এর প্রতিটি তথ্য **React state** থেকে আসুক — অর্থাৎ, DOM নয়, React নিয়ন্ত্রণ করবে ডেটা।

👉 তাই Form Handling মানে হলো —

* ইনপুটে যা টাইপ হচ্ছে তা `onChange` ইভেন্টে ধরা,
* সেই মান `useState`-এ রাখা,
* আর সেই মানই ইনপুটের `value` প্রপসে দেখানো।

এভাবেই React ফর্মের পুরো ডেটা নিয়ন্ত্রণ করে।

---

## 🧩 ২️⃣ Controlled Components কী?

**Controlled Component** মানে এমন ইনপুট এলিমেন্ট যার মান (value) React state দ্বারা নিয়ন্ত্রিত।

```jsx
<input value={stateValue} onChange={handleChange} />
```

### 💡 এটি কীভাবে কাজ করে:

1. ইনপুটে যা দেখা যাচ্ছে তা React-এর state থেকে আসে।
2. ইউজার টাইপ করলে → `onChange` ট্রিগার হয় → state আপডেট হয় → React re-render করে → ইনপুটে নতুন মান দেখা যায়।

অর্থাৎ React সর্বদা জানে ইনপুটে কী আছে।

---

## ⚪ ৩️⃣ Uncontrolled Component — বিপরীত ধারণা

**Uncontrolled Component** মানে ইনপুটের মান React নয়, DOM নিজে ম্যানেজ করে।
React শুধু প্রয়োজনে ref দিয়ে সেই মান পড়ে।

```jsx
function Uncontrolled() {
  const inputRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(inputRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} defaultValue="Hello" />
      <button>Submit</button>
    </form>
  );
}
```

✅ সহজ
❌ React দ্বারা লাইভ ভ্যালিডেশন কঠিন
❌ Reset বা Sync করা জটিল

📌 তাই বেশিরভাগ ক্ষেত্রে Controlled Components ব্যবহার করা হয়।

---

## 🧱 ৪️⃣ Controlled Component Step-by-Step

```jsx
import React, { useState } from "react";

export default function ControlledInput() {
  const [name, setName] = useState("");

  function handleChange(e) {
    setName(e.target.value);
  }

  function handleSubmit(e) {
    e.preventDefault();
    alert(`Submitted Name: ${name}`);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="name">Enter Name:</label>
      <input
        id="name"
        type="text"
        value={name}
        onChange={handleChange}
        placeholder="Type your name"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

**ধাপে ধাপে ব্যাখ্যা:**

* `useState("")` দিয়ে ইনপুটের মানের state বানানো হয়েছে।
* `value={name}` ইনপুটের মান React state থেকে দিচ্ছে।
* ইউজার কিছু টাইপ করলেই `onChange` → `setName()` → state আপডেট → নতুন মান ইনপুটে দেখা যায়।

---

## 🧩 ৫️⃣ একাধিক ইনপুট একসাথে হ্যান্ডেল করা

```jsx
function MultiInputForm() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    password: "",
    isAgreed: false,
  });

  function handleChange(e) {
    const { name, type, value, checked } = e.target;
    setFormData({
      ...formData,
      [name]: type === "checkbox" ? checked : value,
    });
  }

  function handleSubmit(e) {
    e.preventDefault();
    console.log(formData);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>Name:</label>
      <input name="name" value={formData.name} onChange={handleChange} />

      <label>Email:</label>
      <input name="email" type="email" value={formData.email} onChange={handleChange} />

      <label>Password:</label>
      <input name="password" type="password" value={formData.password} onChange={handleChange} />

      <label>
        <input name="isAgreed" type="checkbox" checked={formData.isAgreed} onChange={handleChange} />
        I agree to the terms
      </label>

      <button type="submit">Register</button>
    </form>
  );
}
```

এখানে একাধিক ইনপুট একই state অবজেক্টে রাখা হয়েছে।
`name` attribute অনুযায়ী React জানে কোন ইনপুট পরিবর্তিত হয়েছে।

---

## 🧠 ৬️⃣ Textarea এবং Select হ্যান্ডেল করা

React-এ এগুলোও `value` প্রপস দিয়ে নিয়ন্ত্রিত হয়।

```jsx
<textarea value={message} onChange={e => setMessage(e.target.value)} />
```

```jsx
<select value={fruit} onChange={e => setFruit(e.target.value)}>
  <option value="apple">Apple</option>
  <option value="orange">Orange</option>
</select>
```

---

## 📂 ৭️⃣ File Input (Uncontrolled)

ফাইল ইনপুটের `value` React দ্বারা পরিবর্তন করা যায় না (security কারণে)।

```jsx
function FileUpload() {
  const fileRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(fileRef.current.files[0]);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" ref={fileRef} />
      <button>Upload</button>
    </form>
  );
}
```

---

## ✅ ৮️⃣ Form Submission ও Validation

```jsx
function SubmitForm() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    if (!email.includes("@")) setError("Invalid email");
    else {
      alert("Form submitted");
      setError("");
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} placeholder="Enter email" />
      {error && <p style={{ color: "red" }}>{error}</p>}
      <button>Submit</button>
    </form>
  );
}
```

---

## 🧾 ৯️⃣ Validation-এর ধরন

| ধরন             | ব্যাখ্যা                        | উদাহরণ                  |
| --------------- | ------------------------------- | ----------------------- |
| **Client-side** | ইউজার টাইপ করার সাথে সাথে যাচাই | ইমেইল, পাসওয়ার্ড লেন্থ |
| **Server-side** | সার্ভারে ডেটা যাচাই             | ডুপ্লিকেট ইমেইল চেক     |
| **Real-time**   | প্রতি ইনপুটে চেক                | লাইভ ভ্যালিডেশন         |
| **OnBlur**      | ইনপুট ছাড়ার পর চেক             | কম বিভ্রান্তিকর         |

---

## 🧰 🔟 গুরুত্বপূর্ণ Form Event

| ইভেন্ট     | কাজ                 |
| ---------- | ------------------- |
| `onChange` | ইনপুট পরিবর্তিত হলে |
| `onSubmit` | ফর্ম সাবমিট হলে     |
| `onBlur`   | ইনপুট ফোকাস হারালে  |
| `onFocus`  | ইনপুটে ফোকাস এলে    |
| `onReset`  | ফর্ম রিসেট হলে      |

---

## 🎛️ ১১️⃣ Controlled বনাম Uncontrolled

| বৈশিষ্ট্য      | Controlled          | Uncontrolled |
| -------------- | ------------------- | ------------ |
| State          | React `useState`    | DOM `ref`    |
| Validation     | সহজ                 | জটিল         |
| পারফরম্যান্স   | সামান্য ধীর         | দ্রুত        |
| কোড            | একটু বেশি লেখা লাগে | সহজ          |
| সাধারণ ব্যবহার | প্রায় সব ইনপুট     | File Upload  |

---

## 🧩 ১২️⃣ Radio Button Example

```jsx
function RadioExample() {
  const [gender, setGender] = useState("male");

  return (
    <>
      <label>
        <input type="radio" name="gender" value="male" checked={gender === "male"} onChange={e => setGender(e.target.value)} />
        Male
      </label>

      <label>
        <input type="radio" name="gender" value="female" checked={gender === "female"} onChange={e => setGender(e.target.value)} />
        Female
      </label>
    </>
  );
}
```

---

## 🧮 ১৩️⃣ কাস্টম হুক দিয়ে ফর্ম ম্যানেজমেন্ট

```jsx
export function useForm(initialValues, validate, onSubmit) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues({ ...values, [name]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate(values);
    setErrors(validationErrors);
    if (Object.keys(validationErrors).length === 0) onSubmit(values);
  };

  return { values, errors, handleChange, handleSubmit };
}
```

---

## 🌐 ১৪️⃣ বাস্তব ব্যবহার

| ব্যবহার       | উদাহরণ             |
| ------------- | ------------------ |
| লগইন ফর্ম     | ইমেইল + পাসওয়ার্ড |
| সাইনআপ        | একাধিক ইনপুট       |
| সার্চ বার     | ডিবাউন্স সহ ইনপুট  |
| ফিডব্যাক ফর্ম | Textarea + Rating  |
| ফাইল আপলোড    | Uncontrolled ইনপুট |

---

## 🧩 ১৫️⃣ React Hook Form ব্যবহার

```bash
npm install react-hook-form
```

```jsx
import { useForm } from "react-hook-form";

function RHFExample() {
  const { register, handleSubmit, formState: { errors } } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("email", { required: true })} placeholder="Email" />
      {errors.email && <p>Email required</p>}

      <input {...register("password", { minLength: 6 })} type="password" placeholder="Password" />
      {errors.password && <p>Min 6 chars</p>}

      <button>Submit</button>
    </form>
  );
}
```

---

## 🧩 ১৬️⃣ অ্যাক্সেসিবিলিটি (Accessibility) টিপস

* প্রতিটি ইনপুটের সাথে `<label htmlFor>` ব্যবহার করুন।
* ত্রুটি থাকলে `aria-invalid` ব্যবহার করুন।
* ইনপুটে ফোকাস নিয়ন্ত্রণ দিন।
* Placeholder একাই ব্যবহার না করা উত্তম।

---

## ⚡ ১৭️⃣ Performance টিপস

* ফর্ম state লোকাল রাখুন।
* Heavy কম্পোনেন্ট `React.memo` করুন।
* বড় ফর্ম ভেঙে পেজে ভাগ করুন।
* API কল Debounce করুন।

---

## 🧩 ১৮️⃣ সাধারণ ভুল

| ভুল                                    | সমাধান                            |
| -------------------------------------- | --------------------------------- |
| `e.preventDefault()` না দেওয়া         | পেজ রিফ্রেশ হয়ে যাবে             |
| `value` না দেওয়া                      | ইনপুট uncontrolled হয়ে যাবে      |
| Controlled + Uncontrolled মিশিয়ে ফেলা | React Warning দেবে                |
| State সরাসরি পরিবর্তন করা              | Spread করে নতুন অবজেক্ট তৈরি করুন |
| `name` না দেওয়া                       | Dynamic ইনপুটে সমস্যা হবে         |

---

## 🧩 ২০️⃣ সম্পূর্ণ ফিডব্যাক ফর্ম উদাহরণ

```jsx
export default function FeedbackForm() {
  const [form, setForm] = useState({ name: "", feedback: "", rating: 5 });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm({ ...form, [name]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`ধন্যবাদ ${form.name}! আপনার মতামত: ${form.feedback} (Rating: ${form.rating}/5)`);
  };

  return (
    <form onSubmit={handleSubmit} className="p-4 border rounded">
      <label>নাম:</label>
      <input name="name" value={form.name} onChange={handleChange} />

      <label>মতামত:</label>
      <textarea name="feedback" value={form.feedback} onChange={handleChange} />

      <label>রেটিং:</label>
      <select name="rating" value={form.rating} onChange={handleChange}>
        {[1, 2, 3, 4, 5].map(n => <option key={n}>{n}</option>)}
      </select>

      <button type="submit">জমা দিন</button>
    </form>
  );
}
```

---

## 🧾 সারাংশ

| বিষয়                   | অর্থ                                |
| ---------------------- | ----------------------------------- |
| Controlled Component   | React state দ্বারা নিয়ন্ত্রিত ইনপুট |
| Uncontrolled Component | DOM ref দ্বারা নিয়ন্ত্রিত ইনপুট     |
| `value` + `onChange`   | ইনপুট ও state কে bind করা           |
| Validation             | Client-side / Server-side           |
| Libraries              | React Hook Form, Formik             |
| Best Practice          | একক truth = React state             |

---
