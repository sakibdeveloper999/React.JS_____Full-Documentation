
---

# 🌐 Context API — ধাপে ধাপে গাইড (React.js-এ Global State Management)

---

## 🧠 1) Context কী?

`React.createContext()` ব্যবহার করে আমরা একটা **Context Object** তৈরি করি — যেখানে কিছু ডেটা বা স্টেট রাখা হয়।
এরপর, একটা **Provider** কম্পোনেন্ট সেই Context-এর ভ্যালু অ্যাপের নির্দিষ্ট অংশে সরবরাহ করে, আর কোনো **Child Component** সেই ডেটা নিতে পারে `useContext(MyContext)` দিয়ে।

👉 সহজভাবে বললে: Context API মানে হচ্ছে — **prop drilling ছাড়াই ডেটা শেয়ার করা**।

---

## 📌 2) Context কখন ব্যবহার করবে?

**Context ব্যবহার করো যখন —**

* ডেটা অনেকগুলো কম্পোনেন্টে শেয়ার করতে হবে।
* ডেটা যেমন: ইউজারের লগইন ইনফো, থিম, ভাষা, ইউজার সেটিংস, ইত্যাদি।
* ডেটা ঘন ঘন পরিবর্তন হয় না।

**Context ব্যবহার করো না যখন —**

* স্টেট ছোট, শুধু প্যারেন্ট → চাইল্ডে পাঠালেই হয়।
* ঘন ঘন আপডেট হয় এমন ডেটা (যেমন লাইভ কাউন্টার) — এতে অনেক অপ্রয়োজনীয় re-render হবে।

---

## 🧩 3) বেসিক উদাহরণ — থিম টগল (Light/Dark Mode)

```jsx
// ThemeContext.js
import React, { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  const toggle = () => setTheme(t => (t === "light" ? "dark" : "light"));

  const value = { theme, toggle };

  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>;
}

export const useTheme = () => {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider");
  return ctx;
};
```

```jsx
// App.jsx
import React from "react";
import { ThemeProvider, useTheme } from "./ThemeContext";

function Header() {
  const { theme, toggle } = useTheme();
  return (
    <header>
      <p>Current theme: {theme}</p>
      <button onClick={toggle}>Toggle theme</button>
    </header>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <Header />
    </ThemeProvider>
  );
}
```

**ধাপে ধাপে কী হচ্ছে:**

1. `createContext()` দিয়ে Context বানানো হলো।
2. `ThemeProvider` নামে Provider বানিয়ে `<ThemeContext.Provider>` দিয়ে value পাঠানো হলো।
3. যেকোনো চাইল্ডে `useTheme()` ব্যবহার করে সেই value নেওয়া হচ্ছে।

---

## ⚙️ 4) Context + useReducer (বড় বা জটিল লজিকের জন্য)

যখন তোমার Context-এ একাধিক Action বা জটিল state change দরকার, তখন `useReducer()` দারুণ কাজ করে।

```jsx
// AuthContext.js
import React, { createContext, useContext, useReducer, useEffect } from "react";

const AuthContext = createContext();

const initialState = { user: null, loading: true };

function reducer(state, action) {
  switch (action.type) {
    case "LOGIN": return { ...state, user: action.payload, loading: false };
    case "LOGOUT": return { ...state, user: null, loading: false };
    case "SET_LOADING": return { ...state, loading: action.payload };
    default: return state;
  }
}

export function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);

  useEffect(() => {
    const token = localStorage.getItem("token");
    if (token) {
      const fakeUser = { id: 1, name: "সাকিব" };
      dispatch({ type: "LOGIN", payload: fakeUser });
    } else {
      dispatch({ type: "SET_LOADING", payload: false });
    }
  }, []);

  const login = (user) => {
    localStorage.setItem("token", "token");
    dispatch({ type: "LOGIN", payload: user });
  };
  const logout = () => {
    localStorage.removeItem("token");
    dispatch({ type: "LOGOUT" });
  };

  const value = { state, login, logout };
  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

export const useAuth = () => {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error("useAuth must be used within AuthProvider");
  return ctx;
};
```

👉 এখানে Provider-এর ভেতরে reducer আছে, তাই Auth logic centralize হয়ে গেছে।

---

## ⚡ 5) পারফরম্যান্স ও ভালো প্র্যাকটিস

1. **একাধিক Context ভাগ করো** — যেমন ThemeContext, AuthContext আলাদা রাখো।
2. **Memoize করো** — `useMemo()` দিয়ে provider value স্থির রাখো:

   ```jsx
   const value = useMemo(() => ({ theme, toggle }), [theme]);
   ```
3. **Custom Hook বানাও** — সরাসরি `useContext()` না ডেকে `useAuth()` বা `useTheme()` ব্যবহার করো।
4. **ঘন ঘন পরিবর্তনশীল ডেটা** Context-এ দিও না।
5. **Functions** পাঠানোর সময় `useCallback` ব্যবহার করো।
6. **Server-side rendering** এ Context ব্যবহারে সতর্ক থাকো — ব্রাউজার-নির্ভর জিনিস যেন না চলে।

---

## ⚠️ 6) সাধারণ ভুলগুলো

❌ Provider বাদে useContext কল করা → undefined পাওয়া যায়
✅ সমাধান: কাস্টম হুকের ভেতর Error throw করো।

❌ খুব বেশি re-render → Provider value প্রতি render-এ নতুন object হচ্ছে
✅ সমাধান: `useMemo` ব্যবহার করো।

❌ সব কিছু Context-এ রাখা
✅ সমাধান: শুধু যেগুলো সত্যিই globally দরকার, সেগুলোই রাখো।

---

## 💡 7) অ্যাডভান্স প্যাটার্ন

* **Multiple Providers একত্র করা:**

  ```jsx
  export function AppProviders({ children }) {
    return (
      <AuthProvider>
        <ThemeProvider>
          <SettingsProvider>{children}</SettingsProvider>
        </ThemeProvider>
      </AuthProvider>
    );
  }
  ```

* **API Context** — axios instance বা API client Context-এর মাধ্যমে শেয়ার করা।

* **localStorage + Context** — Context-এর state localStorage-এ সংরক্ষণ করা।

* **Observable Context** — রিয়েল-টাইম আপডেট দরকার হলে observable প্যাটার্ন ব্যবহার করা যায়।

---

## 🛒 8) বাস্তব উদাহরণ — Cart Context

```jsx
// CartContext.js
import React, { createContext, useReducer, useContext, useMemo } from "react";

const CartContext = createContext();

function reducer(state, action) {
  switch (action.type) {
    case "ADD":
      const existing = state.items.find(i => i.id === action.payload.id);
      if (existing) {
        return {
          ...state,
          items: state.items.map(i =>
            i.id === action.payload.id ? { ...i, qty: i.qty + 1 } : i
          )
        };
      }
      return { ...state, items: [...state.items, { ...action.payload, qty: 1 }] };
    case "REMOVE":
      return { ...state, items: state.items.filter(i => i.id !== action.payload) };
    case "CLEAR": return { ...state, items: [] };
    default: return state;
  }
}

export function CartProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, { items: [] });

  const addToCart = (product) => dispatch({ type: "ADD", payload: product });
  const removeFromCart = (id) => dispatch({ type: "REMOVE", payload: id });
  const clearCart = () => dispatch({ type: "CLEAR" });

  const value = useMemo(() => ({
    items: state.items,
    addToCart,
    removeFromCart,
    clearCart,
    total: state.items.reduce((s, i) => s + i.price * i.qty, 0)
  }), [state.items]);

  return <CartContext.Provider value={value}>{children}</CartContext.Provider>;
}

export const useCart = () => {
  const ctx = useContext(CartContext);
  if (!ctx) throw new Error("useCart must be used within CartProvider");
  return ctx;
};
```

এটা হচ্ছে একটি **সম্পূর্ণ Context-based Cart System**, যেখানে reducer + memo ব্যবহার হয়েছে।

---

## 🔄 9) বিকল্প সমাধান

| বিকল্প                  | কবে ব্যবহার করবে                |
| ----------------------- | ------------------------------- |
| **Local State (props)** | ছোট স্টেট, সীমিত স্কোপ          |
| **Redux**               | বড় অ্যাপ, DevTools বা জটিল ফ্লো |
| **Zustand**             | হালকা, কম re-render             |
| **Recoil / Jotai**      | Atom-based fine-grained স্টেট   |

---

## ✅ 10) চেকলিস্ট (Context সঠিকভাবে ব্যবহারের নিয়ম)

1. কোন ডেটা শেয়ার করতে হবে ঠিক করো।
2. Context ফাইল বানাও, Provider + Custom Hook তৈরি করো।
3. বড় স্টেট হলে useReducer ব্যবহার করো।
4. Provider value `useMemo()` দিয়ে memoize করো।
5. আলাদা আলাদা Context ব্যবহার করো।
6. Provider ও consumer টেস্ট করো।
7. কনটেক্সট API ডকুমেন্টেশন করে রাখো।

---

### 🔚 সারসংক্ষেপ:

**Context API** হলো React-এর বিল্ট-ইন Global State Management system —
যেখানে তুমি state বা data এক জায়গায় রেখে অ্যাপের যেকোনো কম্পোনেন্টে দিতে পারো, prop drilling ছাড়াই।

---
