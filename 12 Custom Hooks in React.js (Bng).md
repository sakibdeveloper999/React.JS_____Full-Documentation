
---
# 🧠 React.js Custom Hooks — ধাপে ধাপে বাংলা ব্যাখ্যা

---

## 🔹 ১) Custom Hook কী?

👉 **Custom Hook** হচ্ছে একধরনের JavaScript ফাংশন, যার নাম সবসময় `use` দিয়ে শুরু হয় (যেমন `useFetch`, `useToggle` ইত্যাদি)।
এটা মূলত এমন এক পদ্ধতি যার মাধ্যমে আমরা **reusable logic (পুনঃব্যবহারযোগ্য লজিক)** বানাতে পারি — অর্থাৎ যেকোনো component-এ একই ধরনের stateful logic আবার ব্যবহার করা যায়, কোড কপি না করেই।

✅ **নিয়ম:**

* নাম অবশ্যই `use` দিয়ে শুরু হতে হবে
* শুধুমাত্র React ফাংশন কম্পোনেন্ট বা অন্য কোনো Hook এর ভিতরে ব্যবহার করা যাবে
* Hook গুলো সবসময় টপ লেভেলে কল করতে হবে (if বা loop এর ভিতরে নয়)

---

## 🔹 ২) কখন Custom Hook ব্যবহার করব (Use Case)

🔸 নিচের পরিস্থিতিতে Custom Hook খুব কাজে লাগে:

1. **ডেটা ফেচিং:** যেমন `useFetch`, `useQuery`
2. **ফর্ম হ্যান্ডলিং ও ভ্যালিডেশন:** যেমন `useForm`
3. **LocalStorage বা SessionStorage এর সাথে কাজ:** যেমন `useLocalStorage`
4. **ইভেন্ট লিসেনার:** যেমন `useEventListener`
5. **Debounce বা Throttle করা মান:** যেমন `useDebouncedValue`
6. **Authentication বা Pagination লজিক শেয়ার করা**
7. **API কলের রিইউজেবল লজিক**
8. **টাইমার, সাবস্ক্রিপশন বা effect clean-up হ্যান্ডল করা**

👉 সহজভাবে — যদি দেখো একাধিক কম্পোনেন্টে একই `useState` বা `useEffect` প্যাটার্ন বারবার লিখছো, তাহলে সেটাকে Custom Hook বানিয়ে ফেলো।

---

## 🔹 ৩) বেসিক উদাহরণ: `useToggle`

```jsx
// useToggle.js
import { useCallback, useState } from "react";

export default function useToggle(initial = false) {
  const [on, setOn] = useState(Boolean(initial));
  const toggle = useCallback(() => setOn(v => !v), []);
  const setTrue = useCallback(() => setOn(true), []);
  const setFalse = useCallback(() => setOn(false), []);
  return { on, toggle, setTrue, setFalse };
}
```

🔸 **ব্যবহার:**

```jsx
import useToggle from "./useToggle";

function LikeButton() {
  const { on, toggle } = useToggle(false);
  return <button onClick={toggle}>{on ? "Liked" : "Like"}</button>;
}
```

📘 এখানে `useToggle` হুক একটি ছোট utility যা `true/false` অবস্থা টগল করতে দেয়।

---

## 🔹 ৪) বাস্তব উদাহরণ ১ — `useFetch`

API থেকে ডেটা ফেচ করার সময় লোডিং, ডেটা, এরর স্টেট ম্যানেজ করা হয় এইভাবে 👇

```jsx
// useFetch.js
import { useEffect, useState } from "react";

export default function useFetch(url, options = null, deps = []) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!url) return;
    const ac = new AbortController();
    const signal = ac.signal;
    let mounted = true;

    setLoading(true);
    setError(null);
    setData(null);

    fetch(url, { ...options, signal })
      .then(async res => {
        if (!res.ok) throw new Error(`${res.status} ${res.statusText}`);
        const json = await res.json();
        if (mounted) setData(json);
      })
      .catch(err => {
        if (err.name === "AbortError") return;
        if (mounted) setError(err);
      })
      .finally(() => {
        if (mounted) setLoading(false);
      });

    return () => {
      mounted = false;
      ac.abort();
    };
  }, [url, ...(deps || [])]);

  return { data, error, loading };
}
```

🔸 **ব্যবহার:**

```jsx
import useFetch from "./useFetch";

function UsersList() {
  const { data, loading, error } = useFetch("/api/users");

  if (loading) return <div>Loading…</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {data?.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

💡 `AbortController` ব্যবহার করে fetch বন্ধ করা যায় component unmount হলে — মেমরি লিক ঠেকাতে।

---

## 🔹 ৫) বাস্তব উদাহরণ ২ — `useLocalStorage`

LocalStorage এর সাথে state sync করার জন্য 👇

```jsx
// useLocalStorage.js
import { useCallback, useEffect, useState } from "react";

export default function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = window.localStorage.getItem(key);
      return raw !== null ? JSON.parse(raw) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch {}
  }, [key, value]);

  const remove = useCallback(() => {
    try {
      window.localStorage.removeItem(key);
      setValue(undefined);
    } catch {}
  }, [key]);

  return [value, setValue, remove];
}
```

🔸 **ব্যবহার:**

```jsx
const [theme, setTheme] = useLocalStorage("theme", "light");
```

এখানে `theme` state localStorage এ save থাকবে, reload করলেও হারাবে না।

---

## 🔹 ৬) একাধিক Hook একসাথে ব্যবহার (Composition)

তুমি একাধিক ছোট Hook একত্রে ব্যবহার করে বড় Hook বানাতে পারো।
যেমন:
👉 `useDebouncedLocalStorage` = `useDebouncedValue` + `useLocalStorage`

এভাবে কোড Modular ও Reusable হয়।

---

## 🔹 ৭) আরও কিছু ছোট উপকারী Hook

### 🔸 `usePrevious`

```jsx
import { useEffect, useRef } from "react";

export function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; }, [value]);
  return ref.current;
}
```

👉 আগের value ট্র্যাক রাখে (যেমন আগের state তুলনা করা যায়)

### 🔸 `useDebouncedValue`

```jsx
import { useEffect, useState } from "react";

export function useDebouncedValue(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const t = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(t);
  }, [value, delay]);
  return debounced;
}
```

👉 value পরিবর্তন থেমে গেলে কিছু সময় পরে আপডেট করে — সার্চ ইনপুটে দারুণ কাজ করে।

---

## 🔹 ৮) TypeScript উদাহরণ

```ts
// useLocalStorage.ts
import { useEffect, useState } from "react";

export default function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    try {
      const raw = localStorage.getItem(key);
      return raw ? (JSON.parse(raw) as T) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch {}
  }, [key, value]);

  return [value, setValue] as const;
}
```

---

## 🔹 ৯) Custom Hook টেস্ট করা

Testing এর জন্য `@testing-library/react-hooks` ব্যবহার করা যায়।

```js
import { renderHook, act } from '@testing-library/react-hooks';
import useToggle from './useToggle';

test('toggle changes state', () => {
  const { result } = renderHook(() => useToggle(false));
  expect(result.current.on).toBe(false);
  act(() => result.current.toggle());
  expect(result.current.on).toBe(true);
});
```

---

## 🔹 ১০) পারফরম্যান্স টিপস

✅ ছোট এবং নির্দিষ্ট কাজের জন্য Hook তৈরি করো
✅ `useCallback` দিয়ে function memoize করো
✅ রিটার্ন করা অবজেক্টে `useMemo` ব্যবহার করো
✅ Dependency array ঠিক রাখো
✅ Hook এর ভেতরে JSX রিটার্ন করো না
✅ Premature abstraction এড়িয়ে চলো

---

## 🔹 ১১) সাধারণ ভুল

❌ `use` দিয়ে নাম না শুরু করা
❌ Hook কে `if` বা `loop` এর ভিতরে কল করা
❌ Non-memoized ফাংশন রিটার্ন করা
❌ LocalStorage এ non-serializable ডেটা রাখা
❌ অপ্রয়োজনে abstraction করা

---

## 🔹 ১২) কখন Custom Hook না বানানো ভালো

* শুধু UI logic হলে — component হিসেবে রাখো
* যদি কেবল একটি কম্পোনেন্টেই প্রয়োজন হয়
* অতিরিক্ত abstraction কোড বুঝতে কঠিন করে ফেলে

---

## 🔹 ১৩) ফোল্ডার স্ট্রাকচার (উদাহরণ)

```
/hooks
  useFetch.js
  useLocalStorage.js
  useToggle.js
/components
  UsersList.jsx
  ThemeSwitcher.jsx
```

👉 `UsersList` ব্যবহার করে `useFetch`,
👉 `ThemeSwitcher` ব্যবহার করে `useLocalStorage` + `useToggle`

---

## 🔹 ১৪) Hook বানানোর আগে চেকলিস্ট

* কি বারবার একই লজিক ব্যবহার হচ্ছে?
* কি `useState` বা `useEffect` এর সাথে সম্পর্কিত?
* কি আলাদা করে টেস্ট করা যাবে?
* কি ইনপুট/আউটপুট ক্লিয়ার?
* কি নিয়ম মেনে কল করা হচ্ছে?

---

## 🔹 ১৫) Quick Cheat Sheet

| উদ্দেশ্য           | Hook নাম                              |
| ------------------ | ------------------------------------- |
| ডেটা ফেচিং         | `useFetch(url, options, deps)`        |
| Local Storage Sync | `useLocalStorage(key, initial)`       |
| Debounce মান       | `useDebouncedValue(value, 300)`       |
| ইভেন্ট লিসেনার     | `useEventListener('resize', handler)` |
| টগল স্টেট          | `useToggle(initial)`                  |

---

## ✅ উপসংহার

**Custom Hook** মানে হলো — এমন একটি ফাংশন যা stateful logic কে আলাদা করে reusable করে।
এর ফলে কোড হয় পরিষ্কার, ছোট, ও মেইনটেইন করা সহজ।
React প্রজেক্ট বড় হলে, custom hooks তোমার সেরা বন্ধু হয়ে যাবে 💪

---
