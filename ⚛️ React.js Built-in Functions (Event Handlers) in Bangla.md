
---

# ⚛️ React.js Built-in Functions (Event Handlers) — সম্পূর্ণ বাংলা গাইড

React.js আমাদেরকে অনেকগুলো **বিল্ট-ইন ইভেন্ট হ্যান্ডলার ফাংশন** (যেমন `onClick`, `onChange`, `onSubmit` ইত্যাদি) দেয়, যেগুলো দিয়ে ইউজারের বিভিন্ন অ্যাকশন (ক্লিক, টাইপ, স্ক্রল, ইত্যাদি) সহজে হ্যান্ডেল করা যায়।

---

## 🧠 React-এ Event Handler কী?

React-এ **Event Handler** হচ্ছে এমন একটি ফাংশন যা কোনো নির্দিষ্ট ইভেন্ট ঘটলে (যেমন বাটন ক্লিক, ইনপুট টাইপ, হোভার ইত্যাদি) ট্রিগার হয়।

React-এর ইভেন্ট নামগুলো **camelCase** স্টাইলে লেখা হয় (যেমন `onClick`, `onSubmit`) এবং এগুলোতে **function reference** পাস করতে হয়, string নয়।

---

## 🪄 React.js-এ Function তৈরি ও ব্যবহার করার নিয়ম

চলো একটি **বাস্তব উদাহরণে** দেখি 👇

### 🧩 উদাহরণ: Button Click Counter

```jsx
import React, { useState } from "react";

function CounterApp() {
  // ধাপ ১: state তৈরি করা
  const [count, setCount] = useState(0);

  // ধাপ ২: একটি Function তৈরি করা
  const handleClick = () => {
    setCount(count + 1);
  };

  // ধাপ ৩: Function-টি JSX-এর মধ্যে ব্যবহার করা
  return (
    <div style={{ textAlign: "center", marginTop: "40px" }}>
      <h1>তুমি {count} বার ক্লিক করেছো</h1>
      <button onClick={handleClick}>Click Me 👆</button>
    </div>
  );
}

export default CounterApp;
```

✅ **ব্যাখ্যা:**

1. `useState` দ্বারা একটি count state তৈরি হয়েছে।
2. `handleClick()` ফাংশনটি প্রতি ক্লিকে count বাড়ায়।
3. `onClick` ইভেন্টে আমরা ফাংশনটি কল করেছি।

💡 **বাস্তব জীবনের উদাহরণ:**
এই প্যাটার্নটি অনেক জায়গায় ব্যবহার হয় — যেমন শপিং কার্টে আইটেম বাড়ানো, পোস্টে লাইক দেওয়া, ভোটিং বাটন, কুইজ উত্তর দেওয়া ইত্যাদি।

---

## 🧾 React-এর সব Built-in Event Handlers (ইভেন্ট হ্যান্ডলার) তালিকা

নিচে React.js-এর সব ইভেন্ট হ্যান্ডলার ক্যাটাগরি অনুযায়ী সুন্দরভাবে সাজানো হলো 👇

---

### 🖱️ মাউস ইভেন্ট (Mouse Events)

| ইভেন্ট          | বিবরণ                                | উদাহরণ                       |
| --------------- | ------------------------------------ | ---------------------------- |
| `onClick`       | কোনো এলিমেন্টে ক্লিক করলে ট্রিগার হয় | `<button onClick={...}>`     |
| `onContextMenu` | ডান বাটনে ক্লিক করলে ট্রিগার হয়      | `<div onContextMenu={...}>`  |
| `onDoubleClick` | দুইবার ক্লিক করলে ট্রিগার হয়         | `<div onDoubleClick={...}>`  |
| `onMouseDown`   | মাউস প্রেস করলে ট্রিগার হয়           | `<button onMouseDown={...}>` |
| `onMouseUp`     | মাউস ছেড়ে দিলে ট্রিগার হয়            | `<button onMouseUp={...}>`   |
| `onMouseEnter`  | মাউস এলিমেন্টে ঢুকলে                 | `<div onMouseEnter={...}>`   |
| `onMouseLeave`  | মাউস এলিমেন্ট ছাড়লে                  | `<div onMouseLeave={...}>`   |
| `onMouseMove`   | মাউস সরালে                           | `<div onMouseMove={...}>`    |
| `onMouseOver`   | এলিমেন্টের উপর গেলে (bubble হয়)      | `<div onMouseOver={...}>`    |
| `onMouseOut`    | এলিমেন্ট থেকে বের হলে (bubble হয়)    | `<div onMouseOut={...}>`     |

---

### ⌨️ কীবোর্ড ইভেন্ট (Keyboard Events)

| ইভেন্ট       | বিবরণ                 | উদাহরণ                     |
| ------------ | --------------------- | -------------------------- |
| `onKeyDown`  | যখন কোনো কী প্রেস হয়  | `<input onKeyDown={...}>`  |
| `onKeyPress` | কী প্রেস (deprecated) | `<input onKeyPress={...}>` |
| `onKeyUp`    | কী ছেড়ে দিলে          | `<input onKeyUp={...}>`    |

---

### 🧾 ফর্ম ইভেন্ট (Form Events)

| ইভেন্ট      | বিবরণ                     | উদাহরণ                     |
| ----------- | ------------------------- | -------------------------- |
| `onChange`  | ইনপুটের মান পরিবর্তন হলে  | `<input onChange={...}>`   |
| `onInput`   | প্রতিটি টাইপে ট্রিগার হয়  | `<textarea onInput={...}>` |
| `onSubmit`  | ফর্ম সাবমিট হলে           | `<form onSubmit={...}>`    |
| `onReset`   | ফর্ম রিসেট হলে            | `<form onReset={...}>`     |
| `onInvalid` | ইনপুট ভ্যালিডেশন ফেল করলে | `<input onInvalid={...}>`  |

---

### 🎯 ফোকাস ইভেন্ট (Focus Events)

| ইভেন্ট    | বিবরণ               | উদাহরণ                  |
| --------- | ------------------- | ----------------------- |
| `onFocus` | এলিমেন্ট ফোকাস পেলে | `<input onFocus={...}>` |
| `onBlur`  | ফোকাস হারালে        | `<input onBlur={...}>`  |

---

### 📋 ক্লিপবোর্ড ইভেন্ট (Clipboard Events)

| ইভেন্ট    | বিবরণ      | উদাহরণ                  |
| --------- | ---------- | ----------------------- |
| `onCopy`  | কপি করলে   | `<input onCopy={...}>`  |
| `onCut`   | কাট করলে   | `<input onCut={...}>`   |
| `onPaste` | পেস্ট করলে | `<input onPaste={...}>` |

---

### 🧩 ড্র্যাগ অ্যান্ড ড্রপ ইভেন্ট (Drag & Drop Events)

| ইভেন্ট        | বিবরণ                    | উদাহরণ                    |
| ------------- | ------------------------ | ------------------------- |
| `onDrag`      | কোনো কিছু টেনে নিয়ে গেলে | `<div onDrag={...}>`      |
| `onDragStart` | ড্র্যাগ শুরু হলে         | `<div onDragStart={...}>` |
| `onDragEnd`   | ড্র্যাগ শেষ হলে          | `<div onDragEnd={...}>`   |
| `onDragEnter` | ড্রপ জোনে ঢুকলে          | `<div onDragEnter={...}>` |
| `onDragLeave` | ড্রপ জোন ছাড়লে           | `<div onDragLeave={...}>` |
| `onDragOver`  | ড্রপ জোনের উপর গেলে      | `<div onDragOver={...}>`  |
| `onDrop`      | কিছু ফেলে দিলে           | `<div onDrop={...}>`      |

---

### 📱 টাচ ইভেন্ট (Touch Events)

| ইভেন্ট          | বিবরণ               | উদাহরণ                      |
| --------------- | ------------------- | --------------------------- |
| `onTouchStart`  | আঙুল স্ক্রিনে রাখলে | `<div onTouchStart={...}>`  |
| `onTouchMove`   | আঙুল সরালে          | `<div onTouchMove={...}>`   |
| `onTouchEnd`    | আঙুল তুলে নিলে      | `<div onTouchEnd={...}>`    |
| `onTouchCancel` | টাচ বন্ধ হলে        | `<div onTouchCancel={...}>` |

---

### 🖐️ পয়েন্টার ইভেন্ট (Pointer Events)

| ইভেন্ট            | বিবরণ               | উদাহরণ                        |
| ----------------- | ------------------- | ----------------------------- |
| `onPointerDown`   | পয়েন্টার প্রেস করলে | `<div onPointerDown={...}>`   |
| `onPointerMove`   | পয়েন্টার সরালে      | `<div onPointerMove={...}>`   |
| `onPointerUp`     | পয়েন্টার ছেড়ে দিলে  | `<div onPointerUp={...}>`     |
| `onPointerEnter`  | পয়েন্টার ঢুকলে      | `<div onPointerEnter={...}>`  |
| `onPointerLeave`  | পয়েন্টার ছাড়লে      | `<div onPointerLeave={...}>`  |
| `onPointerOver`   | পয়েন্টার উপর এলে    | `<div onPointerOver={...}>`   |
| `onPointerOut`    | পয়েন্টার বের হলে    | `<div onPointerOut={...}>`    |
| `onPointerCancel` | পয়েন্টার বাতিল হলে  | `<div onPointerCancel={...}>` |

---

### 🧭 স্ক্রল ও হুইল ইভেন্ট (Scroll & Wheel Events)

| ইভেন্ট     | বিবরণ                 | উদাহরণ                 |
| ---------- | --------------------- | ---------------------- |
| `onScroll` | এলিমেন্ট স্ক্রল হলে   | `<div onScroll={...}>` |
| `onWheel`  | মাউস হুইল স্ক্রল করলে | `<div onWheel={...}>`  |

---

### 🎧 মিডিয়া ইভেন্ট (Media Events)

| ইভেন্ট           | বিবরণ                  | উদাহরণ                         |
| ---------------- | ---------------------- | ------------------------------ |
| `onPlay`         | মিডিয়া চালু হলে        | `<video onPlay={...}>`         |
| `onPause`        | মিডিয়া বন্ধ হলে        | `<video onPause={...}>`        |
| `onEnded`        | ভিডিও শেষ হলে          | `<video onEnded={...}>`        |
| `onTimeUpdate`   | সময় আপডেট হলে          | `<video onTimeUpdate={...}>`   |
| `onVolumeChange` | ভলিউম পরিবর্তন হলে     | `<video onVolumeChange={...}>` |
| `onError`        | কোনো ত্রুটি হলে        | `<video onError={...}>`        |
| `onLoadedData`   | ডেটা লোড হলে           | `<video onLoadedData={...}>`   |
| `onCanPlay`      | ভিডিও চালানো সম্ভব হলে | `<video onCanPlay={...}>`      |
| `onWaiting`      | বাফারিং হলে            | `<video onWaiting={...}>`      |

---

### 💫 অ্যানিমেশন ইভেন্ট (Animation Events)

| ইভেন্ট                 | বিবরণ                | উদাহরণ                             |
| ---------------------- | -------------------- | ---------------------------------- |
| `onAnimationStart`     | অ্যানিমেশন শুরু হলে  | `<div onAnimationStart={...}>`     |
| `onAnimationEnd`       | অ্যানিমেশন শেষ হলে   | `<div onAnimationEnd={...}>`       |
| `onAnimationIteration` | অ্যানিমেশন রিপিট হলে | `<div onAnimationIteration={...}>` |

---

### 🌈 ট্রানজিশন ইভেন্ট (Transition Events)

| ইভেন্ট            | বিবরণ                 | উদাহরণ                        |
| ----------------- | --------------------- | ----------------------------- |
| `onTransitionEnd` | CSS ট্রানজিশন শেষ হলে | `<div onTransitionEnd={...}>` |

---

### 🈹 কম্পোজিশন ইভেন্ট (Composition Events)

| ইভেন্ট                | বিবরণ              | উদাহরণ                              |
| --------------------- | ------------------ | ----------------------------------- |
| `onCompositionStart`  | কম্পোজিশন শুরু হলে | `<input onCompositionStart={...}>`  |
| `onCompositionUpdate` | আপডেট হলে          | `<input onCompositionUpdate={...}>` |
| `onCompositionEnd`    | শেষ হলে            | `<input onCompositionEnd={...}>`    |

---

### 🧠 অন্যান্য ইভেন্ট (Miscellaneous)

| ইভেন্ট     | বিবরণ              | উদাহরণ                   |
| ---------- | ------------------ | ------------------------ |
| `onSelect` | টেক্সট সিলেক্ট হলে | `<input onSelect={...}>` |
| `onLoad`   | রিসোর্স লোড হলে    | `<img onLoad={...}>`     |
| `onError`  | লোডিং এ ত্রুটি হলে | `<img onError={...}>`    |

---

## 🧩 সারসংক্ষেপ

React Event Handlers প্রধানত নিচের ক্যাটাগরিতে ভাগ করা যায়:

* 🖱️ Mouse Events
* ⌨️ Keyboard Events
* 🧾 Form Events
* 🎯 Focus Events
* 📋 Clipboard Events
* 🧩 Drag & Drop Events
* 📱 Touch & Pointer Events
* 🎧 Media Events
* 💫 Animation / Transition Events
* 🈹 Composition Events
* 🧭 Scroll / Wheel / Miscellaneous

---

## 🚀 চূড়ান্ত কথা

✅ React-এর সব ইভেন্ট **synthetic**, অর্থাৎ সব ব্রাউজারে একভাবে কাজ করে।
✅ ইভেন্ট নাম সবসময় **camelCase** স্টাইলে লেখো (যেমন `onClick`, না যে `onclick`)।
✅ সবসময় **function reference** দাও, function call নয় →
`onClick={handleClick}` ✅
`onClick={handleClick()}` ❌

---

