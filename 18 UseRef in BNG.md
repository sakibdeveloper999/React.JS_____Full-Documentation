
---

# ⚛️ UseRef & DOM in React — ধাপে ধাপে বাংলা গাইড (উদাহরণ, কেন/কেন নয়, বেস্ট প্র্যাকটিসসহ)

চলুন গভীরে দেখি `useRef` কীভাবে কাজ করে এবং এটি DOM অ্যাক্সেস/ম্যানিপুলেশনে কীভাবে ব্যবহার করা হয়। এখানে থাকবে—কি, কেন, উপযোগিতা, উদাহরণ, ব্রেকডাউন, বিকল্প পদ্ধতি, ভুল করার জায়গা, এবং বেস্ট প্র্যাকটিস।

---

# **1) useRef কী (সংক্ষিপ্ত উত্তর)**

`useRef` হলো React-এর একটি হুক, যা একটি **mutable object** দেয় `{ current: ... }` যা রি-রেন্ডারের মধ্যেও একই থাকে।

useRef সাধারণত ব্যবহৃত হয়:

* DOM element অ্যাক্সেস করতে (যেমন input ফোকাস করা)
* এমন mutable ডাটা রাখতে যেটা আপডেট হলেও re-render করে না (যেমন timer id)
* থার্ড-পার্টি imperative লাইব্রেরি (map, chart) DOM-এ attach করতে

⚠️ মনে রাখবেন: DOM সরাসরি পরিবর্তন করা **imperative**, আর React **declarative**। তাই প্রয়োজন ছাড়া ref দিয়ে DOM পরিবর্তন করা উচিত নয়।

---

# **2) বেসিক উদাহরণ: ইনপুট ফোকাস করা**

### উদাহরণ — বাটনে ক্লিক করলে ইনপুট ফোকাস করা

```jsx
import React, { useRef } from "react";

function FocusInput() {
  const inputRef = useRef(null);

  function handleClick() {
    // Imperatively focus the input DOM node
    inputRef.current?.focus();
  }

  return (
    <div>
      <input ref={inputRef} placeholder="Type here..." />
      <button onClick={handleClick}>Focus the input</button>
    </div>
  );
}
```

### ব্যাখ্যা:

* `useRef(null)` → একটি ref তৈরি হলো।
* `<input ref={inputRef} />` → DOM node এখন `inputRef.current` এ রাখা।
* `inputRef.current.focus()` → ফোকাস করার ব্রাউজারের বিল্ট-ইন মেথড।

📌 কখন ব্যবহার করবেন: input focus, text select, accessibility-related keyboard navigation।

---

# **3) এলিমেন্টের মাপ/পজিশন মাপা**

### উদাহরণ — একটি বক্সের width/height মাপা

```jsx
import React, { useRef, useEffect, useState } from "react";

function MeasureBox() {
  const boxRef = useRef(null);
  const [size, setSize] = useState({ width: 0, height: 0 });

  useEffect(() => {
    if (!boxRef.current) return;
    const rect = boxRef.current.getBoundingClientRect();
    setSize({ width: Math.round(rect.width), height: Math.round(rect.height) });
  }, []);

  return (
    <div>
      <div ref={boxRef} style={{ padding: 20, border: "1px solid #ccc" }}>
        Box content
      </div>
      <p>Width: {size.width}px, Height: {size.height}px</p>
    </div>
  );
}
```

💡 যদি responsive measurement দরকার হয় → ব্যবহার করুন `ResizeObserver`।

---

# **4) Mutable values সংরক্ষণ (re-render ছাড়া)**

`useRef` re-render ছাড়াই ডাটা ধরে রাখে। যেমন interval/timer id, previous props, animation frame id।

```jsx
function Timer() {
  const intervalRef = useRef(null);
  const [count, setCount] = useState(0);

  useEffect(() => {
    intervalRef.current = setInterval(() => setCount(c => c + 1), 1000);
    return () => clearInterval(intervalRef.current);
  }, []);

  return <div>Seconds: {count}</div>;
}
```

🔍 কেন state নয়?
কারণ timer id পরিবর্তন হলে UI আপডেট করার প্রয়োজন নেই—শুধু সংরক্ষণের প্রয়োজন।

---

# **5) তৃতীয়-পক্ষ imperative লাইব্রেরি সংযোগ**

যে লাইব্রেরিগুলো DOM node চায় (map, chart), তাদের জন্য `ref` ব্যবহার করা হয়।

```jsx
function MapWrapper() {
  const containerRef = useRef(null);

  useEffect(() => {
    const node = containerRef.current;
    const map = new SomeImperativeLibrary.Map(node, options);
    return () => map.destroy();
  }, []);

  return <div ref={containerRef} style={{ width: 600, height: 400 }} />;
}
```

✔️ DOM পাওয়া যাবে কেবল render-এর পর → তাই useEffect ব্যবহার করতে হবে।
✔️ Cleanup করা বাধ্যতামূলক।

---

# **6) Ref ফরওয়ার্ডিং + Imperative Handle**

কখনো parent component child component-এর কোনো method কল করতে চায় (যেমন ফোকাস করানো)। তখন `forwardRef` + `useImperativeHandle` ব্যবহার হয়।

### উদাহরণ — child custom method expose করছে

```jsx
import React, { forwardRef, useRef, useImperativeHandle } from "react";

const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
  }));

  return <input ref={inputRef} {...props} />;
});

// Parent:
function Parent() {
  const fancyRef = useRef(null);
  return (
    <>
      <FancyInput ref={fancyRef} />
      <button onClick={() => fancyRef.current?.focus()}>Focus Fancy</button>
    </>
  );
}
```

📝 কেন এটি দরকার?
এটি child-এর অভ্যন্তরীণ DOM parent থেকে সরাসরি অ্যাক্সেস করা প্রতিরোধ করে এবং নিয়ন্ত্রিত API দেয়।

---

# **7) Callback refs বনাম Object refs**

**Object ref** (সাধারণত ব্যবহৃত):

```jsx
const r = useRef();
<div ref={r} />
```

**Callback ref** (ref সেট/ফিরে যাওয়ার সময় কাজ করতে হলে):

```jsx
<div ref={(el) => { myRef = el; console.log("ref changed"); }} />
```

👑 যত সম্ভব Object ref ব্যবহার করুন। Callback ref কেবল বিশেষ ক্ষেত্রে।

---

# **8) কেন অত্যধিক useRef ব্যবহার করা উচিত নয় (কেন/কেন নয়)**

❌ DOM সরাসরি পরিবর্তন করলে React-এর declarative UI নষ্ট হয়
❌ DOM পরিবর্তন React-এর পরবর্তী render-এ হারিয়ে যেতে পারে
❌ কোড জটিল, unpredictable হয়ে যেতে পারে

কখনই ব্যবহার করবেন না:

* state/props দিয়ে কাজ করা সম্ভব হলে
* স্টাইল/ক্লাস পরিবর্তনের জন্য (state ব্যবহার করুন)
* conditional rendering বা UI update-এর জন্য

---

# **9) সাধারণ ভুল এবং সেগুলো এড়ানোর উপায়**

| ভুল                                     | কারণ                    | সমাধান                                 |
| --------------------------------------- | ----------------------- | -------------------------------------- |
| `ref.current` null পাওয়া                | DOM এখনো mount হয়নি     | সবসময় `if (ref.current)` দিয়ে চেক করুন |
| DOM পরিবর্তনের পরে React overwrite করছে | React নিজেই render করছে | UI পরিবর্তন state দিয়ে করুন            |
| cleanup ভুলে যাওয়া                      | memory leak             | useEffect cleanup return ব্যবহার করুন  |
| state-এর মতো আচরণে ref ব্যবহার          | UI আপডেট হবে না         | UI ট্রিগার দরকার হলে অবশ্যই state      |

---

# **10) Best Practices চেকলিস্ট**

✔ DOM access শুধু প্রয়োজন হলে
✔ focus, measurement, third-party libs — এগুলোর জন্য ref
✔ UI update-এর জন্য state ব্যবহার
✔ DOM-dependent logic → useEffect-এ
✔ Cleanup সবসময় করুন
✔ `forwardRef + useImperativeHandle` শুধুমাত্র বিশেষ প্রয়োজনে
✔ Object ref ডিফল্ট রাখুন

---

# **11) বিকল্প (Alternatives)**

* Declarative UI → state/props দিয়ে কাজ করুন
* `createRef` → class component-এ, function component-এ নয়
* Animation → Framer Motion, React Spring
* Forms → Formik / React Hook Form (DOM manipulation নয়)

---

# **12) useRef সমস্যার debug checklist**

* `ref.current === null` → DOM তৈরি হয়নি?
* React কি আপনার DOM পরিবর্তন override করছে?
* Cleanup করেছেন? (listeners, observers, timers)
* parent-child imperative interaction দরকার হলে → forwardRef?

---

