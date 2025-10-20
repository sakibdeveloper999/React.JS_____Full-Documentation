
---

# 🧩 **8_UseEffect_Hook — সম্পূর্ণ বাংলা ডকুমেন্ট**

---


## 1️⃣ এক লাইনে সারসংক্ষেপ

`useEffect` হলো React-এর একটি হুক যা **component render হওয়ার পরে side-effect চালায়** (mount + update সময়), এবং চাইলে cleanup কাজও করে, যা dependency array দ্বারা নিয়ন্ত্রিত হয়।

---

## 2️⃣ এটি কিভাবে কাজ করে (Step-by-Step)

1. তুমি component-এর ভেতর `useEffect(effectFn, deps)` লিখবে।
2. React যখন UI render শেষ করে, তখন সে `effectFn()` চালায়।
3. যদি `deps` না দাও → এটি **প্রতিবার render হওয়ার পর** চলবে।
4. যদি `deps` দাও `[]` → এটি **শুধু একবার** (mount হলে) চলবে।
5. যদি `deps` দাও `[a, b]` → এটি **mount এবং যখন a বা b পরিবর্তন হয়** তখন চলবে।
6. যদি `effectFn` থেকে একটি function return করো → সেটি cleanup হিসেবে চলবে (unmount বা পরের effect চালানোর আগে)।
7. React dependency গুলো compare করে দেখে (primitive value হলে value, object হলে reference) — কিছু বদলালে effect আবার চলে।

---

## 3️⃣ Syntax (কাঠামো)

```js
import React, { useEffect } from "react";

// প্রতিবার render
useEffect(() => {
  // কাজ
});

// একবারই (mount)
useEffect(() => {
  // কাজ
}, []);

// নির্দিষ্ট dependency পরিবর্তনে
useEffect(() => {
  // কাজ
  return () => {
    // cleanup
  };
}, [dep]);
```

---

## 4️⃣ কিভাবে ব্যবহার করতে হয়

* **Mount সময় কিছু করতে চাইলে**: `useEffect(..., [])`
* **State বা Props বদলালে effect চালাতে চাইলে**: `useEffect(..., [state])`
* **Async data লোড করতে**: async ফাংশন effect-এর ভেতরে declare করে চালাও
* **Cleanup দরকার হলে**: return করে cleanup function দাও
* **বড় effect কে ছোট ছোট effect এ ভাগ করে দাও** — কোড পরিষ্কার থাকবে

---

## 5️⃣ কেন ব্যবহার করব (Why Use)

* Render-এর ভিতর side-effect চালানো যায় না, তাই React এর পরে চালাতে `useEffect` দরকার।
* “যখন এই value বদলাবে তখন এটা করো” — এই declarative ধরণে কাজ করা যায়।
* Cleanup সাপোর্ট করে — event listener, timer, subscription remove করতে পারে।
* Class component-এর `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` এর বিকল্প।
* আলাদা আলাদা effect রাখলে কোড পড়তে ও maintain করতে সহজ হয়।

---

## 6️⃣ কখন ব্যবহার করা উচিত নয় (Why Not Use)

* ভারী computation effect-এ দিও না — `useMemo` ব্যবহার করো।
* UI derive করা যায় এমন state effect দিয়ে আপডেট করো না — render-এই করো।
* DOM পরিবর্তন paint হওয়ার আগে করতে হলে `useLayoutEffect` ব্যবহার করো।
* ছোট ছোট কাজের জন্য effect বেশি ব্যবহার করলে কোড জটিল হয়ে যায়।
* Object/Array literal সরাসরি dependency তে দিও না — `useMemo` বা `useCallback` ব্যবহার করো।

---

## 7️⃣ সাধারণ ভুল ও সমাধান

| সমস্যা                                               | কারণ                                | সমাধান                               |
| ---------------------------------------------------- | ----------------------------------- | ------------------------------------ |
| Effect একটানা চলতে থাকে                              | state update dependency-র মধ্যে আছে | condition দাও বা লজিক বদলাও          |
| মান আপডেট হচ্ছে না                                   | dependency বাদ গেছে                 | dependency array ঠিক করো             |
| Object/Array কারণে effect প্রতিবার চলে               | reference বদলাচ্ছে                  | `useMemo` বা `useCallback` দাও       |
| Fetch বা async result দেরি করে এলে পুরনো state আপডেট | race condition                      | `AbortController` বা cancel flag দাও |

---

## 8️⃣ useEffect বনাম অন্য Hook

| Hook                    | কাজ                                | ব্যবহার সময়                 |
| ----------------------- | ---------------------------------- | --------------------------- |
| `useEffect`             | render পরের side-effect            | data fetch, subscription    |
| `useLayoutEffect`       | DOM paint হওয়ার আগে কাজ            | layout measure, scroll sync |
| `useMemo`/`useCallback` | pure computation / stable function | performance optimization    |

---

## 9️⃣ বাস্তব উদাহরণ

### 🧠 (A) Data Fetch with Cleanup

```js
import { useEffect, useState } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    async function load() {
      try {
        const res = await fetch(`/api/users/${userId}`, { signal: controller.signal });
        const data = await res.json();
        setUser(data);
      } catch (err) {
        if (err.name !== "AbortError") console.error(err);
      }
    }

    load();
    return () => controller.abort(); // cleanup
  }, [userId]);

  return <div>{user ? user.name : "Loading..."}</div>;
}
```

---

### 🧠 (B) Event Listener

```js
useEffect(() => {
  const onResize = () => console.log(window.innerWidth);
  window.addEventListener("resize", onResize);
  return () => window.removeEventListener("resize", onResize);
}, []);
```

---

### 🧠 (C) Debounced Search

```js
useEffect(() => {
  const id = setTimeout(() => {
    searchApi(query);
  }, 500);
  return () => clearTimeout(id);
}, [query]);
```

---

### 🧠 (D) Custom Hook

```js
function useOnlineStatus() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    const onOnline = () => setOnline(true);
    const onOffline = () => setOnline(false);

    window.addEventListener("online", onOnline);
    window.addEventListener("offline", onOffline);

    return () => {
      window.removeEventListener("online", onOnline);
      window.removeEventListener("offline", onOffline);
    };
  }, []);

  return online;
}
```

---

## 🔟 সাধারণ Use Case

* Data fetch করা (mount বা parameter change হলে)
* Timer চালানো বা বন্ধ করা
* Window resize / scroll event ধরার জন্য
* LocalStorage বা Document Title sync রাখা
* Debounce করা (search input delay)
* External API call বা subscription

---

## 1️⃣1️⃣ কখন অন্য Hook ভালো

* DOM মাপ বা layout-related কাজ → `useLayoutEffect`
* গণনা বা caching → `useMemo` / `useCallback`
* Derived UI logic → render-এ করো

---

## 1️⃣2️⃣ ভালো Effect লেখার চেকলিস্ট ✅

* [ ] Side-effect দরকার আছে তো?
* [ ] Dependency array ঠিক আছে তো?
* [ ] Cleanup function লাগবে কি?
* [ ] Infinite loop হবে না তো?
* [ ] Object/Array memoized কি না?
* [ ] DOM পড়া দরকার? তবে `useLayoutEffect` ব্যবহার করো।

---

## 1️⃣3️⃣ Debugging টিপস

* `console.log` দাও effect ও cleanup এ।
* React DevTools দিয়ে দেখো কবে re-render হয়।
* ESLint rule `react-hooks/exhaustive-deps` চালাও।
* Effect simplify করে সমস্যার root বের করো।

---

## 1️⃣4️⃣ চিটশিট (Short Notes)

| ফর্ম                       | কাজ                    |
| -------------------------- | ---------------------- |
| `useEffect(()=>{})`        | প্রতিবার render পর চলে |
| `useEffect(()=>{}, [])`    | শুধু একবার (mount)     |
| `useEffect(()=>{}, [a,b])` | a,b পরিবর্তনে চলে      |
| `return ()=>{}`            | cleanup                |

---

## 1️⃣5️⃣ সম্পূর্ণ Mini Project Idea 💡

**Debounced Search Component**

* `useState` → query, result, loading
* `useEffect` → 500ms timeout
* Timeout cleanup এবং abort controller cancel → smooth experience
  ✅ ব্যবহার করলে তোমার UI fast, responsive, leak-free হবে।

---

## 1️⃣6️⃣ প্র্যাকটিসের জন্য টাস্ক

1. `Clock` বানাও যা প্রতি ১ সেকেন্ডে সময় আপডেট করে।
2. `useFetch(url)` custom hook তৈরি করো যা data নেয় এবং cancel করে।
3. Debounce Search বানাও — ৫০০ms delay এ fetch করে।
4. `useLayoutEffect` ব্যবহার করে element এর width measure করো।

---

## 1️⃣7️⃣ TL;DR (সংক্ষিপ্ত সারসংক্ষেপ)

👉 `useEffect` side-effect চালায় render এর পর
👉 Dependency array দিয়ে নিয়ন্ত্রণ করো কখন effect চলবে
👉 Cleanup ব্যবহার করো timer বা listener বন্ধ করতে
👉 `useLayoutEffect` কেবল layout কাজের জন্য
👉 ছোট ছোট effect ব্যবহার করো — কোড পরিষ্কার রাখো

---
