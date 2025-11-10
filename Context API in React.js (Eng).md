Context in React.js

Context is the right tool when you want to share state across many components without prop-drilling. Below I’ll walk you through what Context is, when to use it, then give several practical, working examples (simple → advanced), plus performance tips and common pitfalls.


---

1) What is Context (short)

React.createContext() creates a context object that can hold a value. A <Provider> at the top of a subtree supplies that value; any descendant can read it with useContext(MyContext) (or Context.Consumer). It's not a replacement for component state — it’s a way to share state.

Use Context for app-level concerns: theme, locale, auth user object, feature flags, UI preferences, cart metadata, etc.


---

2) When to use Context (rules of thumb)

Use it for data that many components need and that rarely changes in tiny pieces (theme, current user, locale).

Don't use Context to replace every state: heavy/very frequently-updating data (e.g., many independent list items frequently updating) can cause re-renders.

If you need advanced features (time-travel, devtools, complex selectors), consider a state library (Redux, Zustand) or combine with reducer patterns.



---

3) Minimal example — Theme toggle

// ThemeContext.js
import React, { createContext, useContext, useState } from "react";

const ThemeContext = createContext(); // default undefined

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  const toggle = () => setTheme(t => (t === "light" ? "dark" : "light"));

  // keep value shape stable: memoize if needed (small apps often fine)
  const value = { theme, toggle };

  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>;
}

// custom hook for nicer API
export const useTheme = () => {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider");
  return ctx;
};

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
      {/* other components can use useTheme() as well */}
    </ThemeProvider>
  );
}

Step-by-step: create context → provide it high in tree → consume with useContext (encapsulate consumption in a custom hook).


---

4) Pattern: Context + useReducer (for complex logic)

When state has many actions or transitions, combine useReducer inside provider.

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

  // e.g., restore session from localStorage / API
  useEffect(() => {
    const token = localStorage.getItem("token");
    if (token) {
      // validate token / fetch user
      const fakeUser = { id: 1, name: "Sakib" }; // replace with real fetch
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

This pattern centralizes logic (auth flows, token refresh) inside the provider.


---

5) Performance considerations & best practices

1. Split contexts: put unrelated data in different contexts (e.g., ThemeContext and AuthContext). Each provider change only re-renders consumers of that context.


2. Memoize provider value: if you pass an object, wrap in useMemo/stable callbacks so value identity doesn't change every render.

const value = useMemo(() => ({ theme, toggle }), [theme, toggle]);


3. Expose small hooks: expose useAuth() rather than useContext(AuthContext) everywhere. Encapsulate logic, hide internals.


4. Avoid passing large objects that frequently change. Provide methods to update smaller pieces where possible.


5. Selector pattern: if you need to read only a small piece and avoid re-renders, either split contexts or use a library that supports selectors (e.g., Zustand). Native Context lacks selector API.


6. useCallback for functions passed through context to keep references stable.


7. Lazy initialization: for expensive initial state, use lazy init with useReducer or useState(() => expensiveInit()).


8. Server-side rendering: on SSR (Next.js), hydrate initial state carefully and avoid reading browser-only APIs in initial render.




---

6) Common pitfalls & how to avoid them

Too many re-renders: caused by providing a new object each render or holding rapidly updating data in context. Fix: memoize or split contexts.

Overuse: don't use Context for everything. If only 1–2 components need the value, pass props.

Missing provider: calling useContext outside provider returns default value (or undefined). Use a custom hook that throws to catch mistakes early.

Stale closures: if your context exposes functions that close over stale values, ensure you use latest refs or include dependencies.



---

7) Advanced patterns

Compound providers: create a single AppProviders component that composes multiple providers:

export function AppProviders({ children }) {
  return (
    <AuthProvider>
      <ThemeProvider>
        <SettingsProvider>{children}</SettingsProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}

Context for API clients: provide a configured API client (axios instance) via context so components can call api.get(...).

Context + localStorage: persist parts of context state with useEffect or with a useLocalStorage helper.

Context with observables: if you need fine-grained updates, combine Context with an observable/store inside provider (e.g., use EventTarget or tiny state libraries) and subscribe in consumers.



---

8) Full working example — Cart (split contexts + reducer + memo)

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

  // memoize value to avoid unnecessary re-renders
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

This example demonstrates splitting logic (reducer) and memoizing the provider value.


---

9) Alternatives & when to pick them

Local component state / prop passing — simplest, lowest overhead.

Redux — for very large apps with complex flows, many devs prefer Redux for tooling and predictable updates.

Zustand — small, fast store with selective subscriptions (better per-component re-render control).

Recoil / Jotai — atom-based fine-grained state.


If you need selector-like re-render control for many small bits of state, prefer a store library (Zustand) over native Context.


---

10) Quick checklist to implement Context correctly

1. Identify the shared data and who needs it.


2. Create context file, Provider component, and a custom hook (e.g., useAuth).


3. Use useReducer for complex state transitions.


4. Memoize provider value objects and stable callback functions.


5. Split contexts for unrelated state slices.


6. Add tests for provider (e.g., render provider + consumer and assert changes).


7. Document the public API of the context (what consumers should call).




---

