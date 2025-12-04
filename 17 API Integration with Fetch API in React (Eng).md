# **📘 API Integration in React.js — Fetch API & Async Data (Complete Book-Level Guide)**

This will be extremely large — essentially like a **full chapter of a professional React book**, with:

* 80–120 sections
* Deep explanations
* Architecture diagrams (ASCII diagrams)
* Patterns
* Anti-patterns
* Best practices
* Real project structure
* Multiple approaches
* Error handling
* Pagination, Search, Infinite Scroll
* Authentication with APIs
* Custom Hooks
* Optimization
* Alternatives (Axios, SWR, React Query)
* Enterprise-level architecture

---

### ⚠ This is huge.

To avoid message limit issues, I will deliver the documentation in **5 large parts**:

---

## 📦 **PART 1 — Concepts, Basics, Fetch API, useEffect, Async Flow (Sections 1–25)**

**Delivered now below**

## 📦 PART 2 — Custom Hooks for API, Error Handling, Caching, Patterns (Sections 26–50)

➡ After you say **"Next Part"**

## 📦 PART 3 — Pagination, Search, Infinite Scroll, Forms with API (Sections 51–75)

➡ After you say **"Next Part"**

## 📦 PART 4 — Authentication APIs, Protected Routes, Refresh Tokens, Optimistic Updates (Sections 76–100)

➡ After "Next Part"

## 📦 PART 5 — Enterprise Architecture, Axios vs Fetch, React Query, SWR, Best Practices (Sections 101–120)

➡ After "Next Part"

---

# 📘 **PART 1 — API Integration in React.js (Sections 1–25)**

### *(Full Depth, Textbook-Level)*

---

# **SECTION 1 — What Is API Integration in React?**

In React, **API integration** means connecting your React frontend with a backend server to:

* Fetch data
* Send data
* Update resources
* Delete resources
* Authenticate users
* Sync UI with server state

React is **frontend only**, so it must call an external server to get data.

---

# **SECTION 2 — Why API Integration Matters**

API integration is the core of modern React apps:

✔ E-commerce product lists
✔ Auth login/register
✔ Chat messages
✔ News feed
✔ Dashboard analytics
✔ Forms

Without APIs, React apps can’t interact with stored data.

---

# **SECTION 3 — How React Handles Async Data**

React renders **synchronously**, but data comes **asynchronously** from API.

Async creates challenges:

* Component renders before data arrives
* Errors must be handled
* Loading states must be shown
* Preventing infinite loops
* Canceling requests on unmount

We solve this with:

→ `useEffect`
→ `useState`
→ Async functions
→ Custom hooks

---

# **SECTION 4 — Understanding the Fetch API**

`fetch()` is the built-in browser method for making HTTP calls.

Example simple usage:

```js
fetch("https://api.example.com/users")
  .then(res => res.json())
  .then(data => console.log(data));
```

---

# **SECTION 5 — Fetch: The Request/Response Cycle**

Fetch returns a **Promise**.

Process:

1. Make request
2. Get response object
3. Convert body → `.json()`
4. React updates UI

---

# **SECTION 6 — Common HTTP Methods**

| Method | Use                   |
| ------ | --------------------- |
| GET    | Fetch data            |
| POST   | Create data           |
| PUT    | Replace existing data |
| PATCH  | Modify partially      |
| DELETE | Remove data           |

---

# **SECTION 7 — Basic React Fetch Example**

```jsx
import { useEffect, useState } from "react";

export default function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return (
    <div>
      <h1>Users</h1>
      {users.map(u => (
        <p key={u.id}>{u.name}</p>
      ))}
    </div>
  );
}
```

---

# **SECTION 8 — Why Use useEffect for API Calls?**

`useEffect` runs:

* after component renders
* avoids blocking UI
* avoids multiple calls (with dependency array)

---

# **SECTION 9 — Avoiding Infinite API Calls**

This is wrong:

```js
useEffect(() => {
  fetchData();
});
```

This runs **on every render** → infinite loop.

Correct:

```js
useEffect(() => {
  fetchData();
}, []); // runs once
```

---

# **SECTION 10 — Converting .then() to async/await**

Better readable:

```jsx
useEffect(() => {
  async function loadUsers() {
    const res = await fetch(url);
    const data = await res.json();
    setUsers(data);
  }

  loadUsers();
}, []);
```

---

# **SECTION 11 — Handling Loading State**

```jsx
const [loading, setLoading] = useState(true);

useEffect(() => {
  async function fetchData() {
    const res = await fetch(url);
    const data = await res.json();
    setUsers(data);
    setLoading(false);
  }
  fetchData();
}, []);
```

---

# **SECTION 12 — Handling Error State**

```jsx
const [error, setError] = useState(null);

try {
  const res = await fetch(url);
  if (!res.ok) throw new Error("Failed to fetch");
} catch (err) {
  setError(err.message);
}
```

---

# **SECTION 13 — Full Basic Pattern: Loading + Error + Data**

```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function load() {
      try {
        const res = await fetch("https://api.example.com/users");
        if (!res.ok) throw new Error("Network error");

        const data = await res.json();
        setUsers(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }
    load();
  }, []);

  if (loading) return <p>Loading…</p>;
  if (error) return <p>Error: {error}</p>;

  return users.map(u => <p key={u.id}>{u.name}</p>);
}
```

---

# **SECTION 14 — POST Request (Create Resource)**

```jsx
await fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Sakib",
    email: "sakib@example.com"
  })
});
```

---

# **SECTION 15 — PUT Request**

```js
await fetch(`/users/3`, {
  method: "PUT",
  body: JSON.stringify(updatedUser),
  headers: { "Content-Type": "application/json" }
});
```

---

# **SECTION 16 — PATCH Request**

```js
await fetch(`/users/3`, {
  method: "PATCH",
  body: JSON.stringify({ email: "new@mail.com" }),
  headers: { "Content-Type": "application/json" }
});
```

---

# **SECTION 17 — DELETE Request**

```js
await fetch(`/users/3`, {
  method: "DELETE"
});
```

---

# **SECTION 18 — AbortController to Cancel API Requests**

Prevents errors on quick navigation.

```js
useEffect(() => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal });

  return () => controller.abort();
}, []);
```

---

# **SECTION 19 — Why Abort?**

Without aborting:

❌ Memory leaks
❌ “State update on unmounted component” warnings
❌ Performance issues

---

# **SECTION 20 — Avoiding Race Conditions**

If user types fast (search):

* earlier slow request may overwrite newer one
* abort previous requests

---

# **SECTION 21 — API Integration Flow Diagram**

```
Component Mounted
        │
        ▼
  useEffect Triggered
        │
        ▼
  Fetch API → Async Call
        │
        ├── Success → setData → Re-render
        │
        └── Error → setError → Re-render
```

---

# **SECTION 22 — Mapping API Data to UI**

Always check for missing fields.

```jsx
<p>{user?.address?.city ?? "Unknown"}</p>
```

---

# **SECTION 23 — Why Not Fetch Inside Render?**

This is wrong:

```js
function App() {
  fetch(url); // ❌ runs every render
}
```

Reasons:

* infinite loops
* re-fetch every render
* crashes app

---

# **SECTION 24 — Folder Structure for API Calls**

```
src/
 ├─ api/
 │   ├─ users.js
 │   ├─ posts.js
 │   └─ auth.js
 ├─ hooks/
 │   └─ useUsers.js
 └─ components/
     └─ UserList.jsx
```

---

# **SECTION 25 — Creating API Service Layer (Best Practice)**

### `src/api/users.js`

```js
const BASE_URL = "https://api.example.com";

export async function getUsers() {
  const res = await fetch(`${BASE_URL}/users`);
  if (!res.ok) throw new Error("Failed to fetch users");
  return res.json();
}
```

### Use in Component:

```jsx
import { getUsers } from "../api/users";

useEffect(() => {
  getUsers().then(setUsers).catch(setError);
}, []);
```

---

Great!
Here comes **📦 PART 2 — Sections 26–50** of the full textbook-level documentation.

---
# **SECTION 26 — Why Create Custom Hooks for API?**

A custom hook like `useUsers()` solves major problems:

❌ Repeated fetch logic in many components
❌ Repeated loading + error state
❌ Hard to maintain API calls
❌ Hard to test
❌ Business logic mixed with UI logic

✔ Custom hooks convert API logic → reusable modules.

---

# **SECTION 27 — Basic Custom Hook Structure**

```
src/hooks/useUsers.js
```

```js
import { useEffect, useState } from "react";
import { getUsers } from "../api/users";

export function useUsers() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    getUsers()
      .then(res => setData(res))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  return { data, loading, error };
}
```

Usage:

```jsx
const { data: users, loading, error } = useUsers();
```

---

# **SECTION 28 — Benefits of Custom Hooks**

✔ Clean components
✔ Centralized API logic
✔ Easy refactoring
✔ Testable
✔ Reusable
✔ Works with any endpoint
✔ Easy to plug into service layer

---

# **SECTION 29 — Custom Hook With Async/Await + Retry Logic**

Add retry on network fail.

```js
export function useFetch(url, retryCount = 3) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  async function load() {
    let attempts = 0;

    while (attempts < retryCount) {
      try {
        const res = await fetch(url);
        if (!res.ok) throw new Error("API Error");

        const json = await res.json();
        setData(json);
        return;
      } catch (err) {
        attempts++;
        if (attempts >= retryCount) setError(err.message);
      }
    }
  }

  useEffect(() => {
    load().finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

---

# **SECTION 30 — Retry Logic (Why?)**

✔ Network is unstable
✔ Mobile users often drop connection
✔ Servers may throttle requests
✔ Smooth user experience

---

# **SECTION 31 — Custom Hook with AbortController Support**

```js
export function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const controller = new AbortController();

    async function load() {
      const res = await fetch(url, { signal: controller.signal });
      const json = await res.json();
      setData(json);
      setLoading(false);
    }

    load();

    return () => controller.abort();
  }, [url]);

  return { data, loading };
}
```

---

# **SECTION 32 — Parallel API Requests**

Use **Promise.all**.

```js
const fetchUsers = fetch("/users").then(r => r.json());
const fetchPosts = fetch("/posts").then(r => r.json());

const [users, posts] = await Promise.all([fetchUsers, fetchPosts]);
```

Benefits:

✔ Faster
✔ No dependency between calls

---

# **SECTION 33 — Sequential API Requests (Chained)**

```js
const user = await fetch(`/user/1`).then(r => r.json());
const posts = await fetch(`/posts?user=${user.id}`).then(r => r.json());
```

Use when:

* Post depends on user
* Must wait for first response

---

# **SECTION 34 — Conditional Fetching**

Do NOT fetch when data is empty or invalid.

```js
useEffect(() => {
  if (!userId) return; // guard

  fetch(`/user/${userId}`);
}, [userId]);
```

---

# **SECTION 35 — Debouncing API Calls (Search Input)**

Use `setTimeout`.

```js
useEffect(() => {
  const delay = setTimeout(() => {
    fetch(`/search?q=${query}`);
  }, 500);

  return () => clearTimeout(delay);
}, [query]);
```

Benefits:

✔ Reduces load
✔ Smooth UX
✔ Prevent API spam

---

# **SECTION 36 — Throttling API Calls**

Useful for infinite scrolling.

```js
let throttle = false;

function onScroll() {
  if (throttle) return;

  throttle = true;
  fetch(nextPage);

  setTimeout(() => (throttle = false), 500);
}
```

---

# **SECTION 37 — Transform API Response**

APIs often send in weird formats.

Normalize:

```js
const formatted = data.items.map(item => ({
  id: item.ID,
  title: item.Name,
  price: item.Cost
}));
```

---

# **SECTION 38 — API Wrapper Function (Reusable)**

Create a universal fetch wrapper.

```
src/utils/apiClient.js
```

```js
export async function apiClient(url, options = {}) {
  const res = await fetch(url, {
    headers: { "Content-Type": "application/json" },
    ...options
  });

  if (!res.ok) {
    const message = await res.text();
    throw new Error(message || "API Error");
  }

  return res.json();
}
```

Now use anywhere:

```js
apiClient("/users");
apiClient("/posts/3", { method: "DELETE" });
```

---

# **SECTION 39 — Why Use an API Wrapper?**

✔ Centralized error handling
✔ Avoid repeating headers
✔ Add interceptors later
✔ Logging
✔ Tokens
✔ Analytics

---

# **SECTION 40 — Adding Token Support to Wrapper**

```js
export async function apiClient(url, options = {}) {
  const token = localStorage.getItem("token");

  const res = await fetch(url, {
    ...options,
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${token}`,
      ...options.headers
    }
  });

  if (!res.ok) throw new Error("API Error");

  return res.json();
}
```

---

# **SECTION 41 — Error Handling Patterns**

### 1. **UI Friendly Error**

```js
setError("Failed to load users. Try again.");
```

### 2. **Technical Error For Developer**

```js
console.error(err);
```

### 3. **API Error Response**

```js
if (!response.ok) {
  const error = await response.json();
  throw new Error(error.message);
}
```

---

# **SECTION 42 — Loading Skeletal Screens (Not Spinners)**

Better UX:

* Skeleton loaders
* Placeholder cards
* Gray boxes

Example:

```jsx
{loading && <SkeletonUserCard />}
```

---

# **SECTION 43 — Avoiding Unnecessary Re-Renders**

Use `useMemo()` when formatting large data sets.

```js
const bigList = useMemo(() => process(data), [data]);
```

---

# **SECTION 44 — Caching API Results (Manual)**

```js
const cache = {};

async function fetchCached(url) {
  if (cache[url]) return cache[url];

  const data = await fetch(url).then(r => r.json());
  cache[url] = data;

  return data;
}
```

---

# **SECTION 45 — Stale-While-Revalidate Concept**

Show **cached data immediately**, fetch fresh data in background.

Flow:

```
Render cached data
      ↓
Fetch new data
      ↓
Update UI
```

This is the foundation of:

* SWR
* React Query
* Next.js App Router

---

# **SECTION 46 — API Polling (Refreshing Every X Seconds)**

Useful for:

* Dashboards
* Stock prices
* Crypto
* Chat

```js
useEffect(() => {
  const interval = setInterval(() => {
    fetchData();
  }, 5000);

  return () => clearInterval(interval);
}, []);
```

---

# **SECTION 47 — WebSocket vs Polling**

| Feature    | Polling           | WebSocket         |
| ---------- | ----------------- | ----------------- |
| Connection | Repeated requests | 1 long connection |
| Real-time  | Medium            | True real-time    |
| Load       | Higher            | Lower             |
| Use cases  | Small updates     | Live chat, games  |

---

# **SECTION 48 — Form Submit With API (POST)**

```jsx
async function handleSubmit(e) {
  e.preventDefault();

  const res = await fetch("/api/create", {
    method: "POST",
    body: JSON.stringify(form),
    headers: { "Content-Type": "application/json" }
  });

  const json = await res.json();
  console.log(json);
}
```

---

# **SECTION 49 — Optimistic UI Basics**

Update UI **before** API succeeds.

Example:

```js
setPosts(prev => [...prev, newPost]); // optimistic
apiClient("/posts", { method: "POST", body: JSON.stringify(newPost) });
```

If API fails → rollback.

---

# **SECTION 50 — API Status Handling Pattern**

UI states:

```
idle
loading
success
error
```

State machine:

```js
const [status, setStatus] = useState("idle");

if (status === "loading") return <Loader />;
if (status === "error") return <ErrorBox />;
```

---

## **PART 3 — Practical Patterns, Real-World Architecture & Professional Techniques**

🥳 *Now we move from “how it works” → to “how real production apps use API integration in React.”*

---

# **🔵 11. API Calling Patterns in React (Standard → Advanced)**

Here are the **most important patterns** you must know.

---

## **### 11.1 Pattern 1 — Fetch on Component Mount (Basic)**

Use when:
✔ Load data right after the page loads (e.g., product list, dashboard)
✔ No user action needed

```jsx
useEffect(() => {
  async function load() {
    try {
      const res = await fetch("/api/products");
      const data = await res.json();
      setProducts(data);
    } catch (err) {
      setError(err);
    }
  }
  load();
}, []);
```

👍 Simple
👎 Not reusable, not scalable

---

## **### 11.2 Pattern 2 — Fetch on User Action**

Use when:
✔ Form submission
✔ Search button
✔ Manual refresh button

```jsx
const handleSearch = async () => {
  const res = await fetch(`/api/users?search=${text}`);
  setUsers(await res.json());
};
```

---

## **### 11.3 Pattern 3 — Fetch on Dependency Change**

Use when:
✔ Query parameters change
✔ Filters change
✔ Selected category changes
✔ Pagination page changes

```jsx
useEffect(() => {
  fetch(`/api/posts?page=${page}&category=${category}`)
    .then(res => res.json())
    .then(setPosts);
}, [page, category]);
```

---

## **### 11.4 Pattern 4 — Fetch inside Custom Hook (Professional)**

Use when:
✔ You want reusable logic
✔ Multiple components need same API
✔ Want separation of concerns

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData);
  }, [url]);

  return data;
}
```

Usage:

```jsx
const users = useFetch("/api/users");
```

---

## **### 11.5 Pattern 5 — Fetch using Service Layer (Enterprise)**

This is how **large companies structure React API logic**.

```
src/
 └── services/
      └── userService.js
```

```js
// userService.js
export const getUsers = () => {
  return fetch("/api/users").then(res => res.json());
};
```

Component:

```jsx
useEffect(() => {
  getUsers().then(setUsers);
}, []);
```

👍 Clean
👍 Good for testing
👍 Easy to mock
👍 Scalable

---

## **### 11.6 Pattern 6 — AbortController (Cancel Requests)**

Necessary when:

✔ Fast typing (search)
✔ Route changes quickly
✔ Prevent “state update on unmounted component”

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => {
      if (err.name === "AbortError") return;
    });

  return () => controller.abort();
}, [url]);
```

---

# **🔵 12. Pagination, Filters & Search with API**

---

## **12.1 Pagination Example**

```jsx
function Users() {
  const [page, setPage] = useState(1);
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch(`/api/users?page=${page}`)
      .then(res => res.json())
      .then(setUsers);
  }, [page]);

  return (
    <>
      <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>
      <button onClick={() => setPage(p => p - 1)}>Prev</button>
      <button onClick={() => setPage(p => p + 1)}>Next</button>
    </>
  );
}
```

---

## **12.2 Search Example (Optimized)**

If user types fast, we must avoid multiple API calls.

⭐ Solution: **Debounce**

```jsx
useEffect(() => {
  const timeout = setTimeout(() => {
    fetch(`/api/search?q=${query}`)
      .then(res => res.json())
      .then(setResults);
  }, 600);

  return () => clearTimeout(timeout);
}, [query]);
```

---

# **🔵 13. Loading State Patterns**

Different apps handle loading differently.

### **13.1 Boolean Loading Flag (Simple)**

```jsx
const [loading, setLoading] = useState(true);
```

---

### **13.2 Skeleton UI (Modern)**

Use placeholder blocks.

```jsx
{loading ? <Skeleton count={5} /> : <UserList />}
```

---

### **13.3 Shimmer Effects**

Useful in e-commerce UI (Amazon-style shimmer)

---

# **🔵 14. Professional Error Handling Patterns**

---

## **### 14.1 Basic Error State**

```jsx
const [error, setError] = useState(null);
```

---

## **### 14.2 Handle both fetch & API errors**

```jsx
try {
  const res = await fetch("/api/data");

  if (!res.ok) {
    throw new Error(`API Error: ${res.status}`);
  }

  const data = await res.json();
  setData(data);

} catch (err) {
  setError(err.message);
}
```

---

## **### 14.3 User-friendly messages**

❌ “500 Internal Server Error”
✔ “Something went wrong. Please try again.”

---

# **🔵 15. POST, PUT, DELETE Requests with Fetch**

---

## **15.1 POST Example**

```jsx
async function createUser() {
  const res = await fetch("/api/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name, email })
  });
}
```

---

## **15.2 PUT Example**

```jsx
fetch(`/api/users/${id}`, {
  method: "PUT",
  body: JSON.stringify(updatedUser)
});
```

---

## **15.3 DELETE Example**

```jsx
fetch(`/api/users/${id}`, { method: "DELETE" });
```

---

# **🔵 16. Optimistic UI (Fast UX like Twitter)**

Optimistic update = update UI **before** API response.

Example: “Like” button

```jsx
function handleLike() {
  setLikes(likes + 1); // optimistic update

  fetch(`/api/like`, { method: "POST" }).catch(() => {
    setLikes(likes - 1); // rollback
  });
}
```

---

# **🔵 17. API Integration with Forms**

---

## **Submit form & send data**

```jsx
const handleSubmit = async (e) => {
  e.preventDefault();

  const res = await fetch("/api/contact", {
    method: "POST",
    body: JSON.stringify({ name, message })
  });

  const json = await res.json();
  console.log(json);
};
```

---

# **🔵 18. Uploading Files with Fetch**

```jsx
const formData = new FormData();
formData.append("file", file);

fetch("/api/upload", {
  method: "POST",
  body: formData
});
```

---

# **🔵 19. Token-Based API Requests (JWT / Bearer)**

```jsx
fetch("/api/profile", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

---

# **🔵 20. Refresh Token Logic**

Professionally:

* Short-lived access token
* Long-lived refresh token
* Auto-refresh before expiration

Example architecture:

```
API → accessToken expires → React → refreshToken → new accessToken → repeat
```

---

# 👉 **NEXT PART CONTENTS (Part 4 Coming Next)**

Part 4 will cover **advanced professional techniques**, including:

### ✔ API Service Layer (Large-scale Architecture)

### ✔ Axios vs Fetch vs TanStack Query vs SWR

### ✔ React Query Complete Guide

### ✔ Caching, Refetching, Retry, Background Sync

### ✔ Infinite Scroll

### ✔ WebSockets + Real-Time Data

### ✔ Deployment considerations

### ✔ Security best practices

### ✔ Performance optimization in API calls

### ✔ API mocking (MSW)

### ✔ Complete project folder structure

### ✔ Full real-world project example

---

## **PART 4 — Advanced, Production-Ready API Integration (Enterprise Level)**

Now we go beyond basics and make this a **real-world, company-grade documentation**.
This part covers:

✔ Service Layer Architecture
✔ Axios vs Fetch
✔ SWR, React Query
✔ Caching, Retry, Background updates
✔ Infinite Scroll
✔ WebSockets
✔ Security
✔ Performance optimization
✔ Testing & API mocking
✔ Complete folder structure
✔ Real project example

---

# **🔵 21. API Service Layer Architecture (Enterprise Standard)**

In a large-scale React application,
❌ components should NOT call APIs directly.
✔ All API logic should live in **services**.

### Example Folder Structure

```
src/
 └─ services/
 │    ├─ apiClient.js
 │    ├─ userService.js
 │    └─ productService.js
 └─ hooks/
      ├─ useUsers.js
      └─ useProducts.js
```

---

## **21.1 apiClient.js (Reusable HTTP Client)**

### Using Fetch:

```jsx
// apiClient.js
const BASE_URL = "https://api.example.com";

export async function apiClient(endpoint, options = {}) {
  const res = await fetch(`${BASE_URL}${endpoint}`, {
    headers: {
      "Content-Type": "application/json",
      ...options.headers,
    },
    ...options
  });

  if (!res.ok) {
    const error = await res.json();
    throw new Error(error.message || "API Error");
  }

  return res.json();
}
```

---

## **21.2 userService.js**

```jsx
import { apiClient } from "./apiClient";

export const userService = {
  getAll() {
    return apiClient("/users");
  },

  getById(id) {
    return apiClient(`/users/${id}`);
  },

  create(data) {
    return apiClient("/users", {
      method: "POST",
      body: JSON.stringify(data)
    });
  },

  update(id, data) {
    return apiClient(`/users/${id}`, {
      method: "PUT",
      body: JSON.stringify(data)
    });
  },

  delete(id) {
    return apiClient(`/users/${id}`, { method: "DELETE" });
  }
};
```

---

## **21.3 Using in Component**

```jsx
useEffect(() => {
  userService.getAll().then(setUsers).catch(setError);
}, []);
```

👍 Clean
👍 Testable
👍 Easy to scale

---

# **🔵 22. Fetch API vs Axios vs React Query vs SWR**

Here is the **ultimate comparison**:

| Feature                | Fetch      | Axios       | React Query | SWR               |
| ---------------------- | ---------- | ----------- | ----------- | ----------------- |
| Built-in               | ✔ Yes      | ❌ No        | ❌ No        | ❌ No              |
| Simplicity             | Medium     | Easy        | Advanced    | Advanced          |
| Interceptors           | ❌ No       | ✔ Yes       | N/A         | N/A               |
| Auto Caching           | ❌ No       | ❌ No        | ✔ Yes       | ✔ Yes             |
| Auto Refetch           | ❌ No       | ❌ No        | ✔ Yes       | ✔ Yes             |
| Background Sync        | ❌ No       | ❌ No        | ✔ Yes       | ✔ Yes             |
| Stale-While-Revalidate | ❌ No       | ❌ No        | ✔ Yes       | ✔ Yes             |
| Pagination Tools       | ❌ No       | ❌ No        | ✔ Yes       | ✔ Yes             |
| Best for               | Small apps | Medium apps | Large apps  | Dashboard/UI apps |

---

# **🔵 23. Axios Professional Setup**

### install:

```
npm install axios
```

---

## **23.1 Create axiosInstance.js**

```jsx
import axios from "axios";

export const axiosInstance = axios.create({
  baseURL: "https://api.example.com",
  timeout: 12000,
});
```

---

## **23.2 Add Interceptors**

```jsx
axiosInstance.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${localStorage.getItem("token")}`;
  return config;
});
```

---

## **23.3 Use in services**

```jsx
export const productService = {
  getProducts() {
    return axiosInstance.get("/products");
  }
};
```

---

# **🔵 24. React Query (TanStack Query) – The Ultimate Data Layer**

Used by:
✔ Netflix
✔ Uber
✔ TikTok
✔ Stripe

React Query solves:

* caching
* retries
* refetching
* background updates
* data synchronization
* infinite scroll
* optimistic update

---

## **24.1 Installation**

```
npm install @tanstack/react-query
```

---

## **24.2 Setup QueryClient**

```jsx
const queryClient = new QueryClient();

root.render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

---

# **24.3 Basic Example**

```jsx
import { useQuery } from "@tanstack/react-query";

function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: () => fetch("/api/users").then(res => res.json())
  });

  if (isLoading) return "Loading...";
  if (error) return "Error";

  return data.map(u => <div key={u.id}>{u.name}</div>);
}
```

🔥 No need for:
❌ useEffect
❌ loading state
❌ error state
❌ manual data updates

React Query handles everything.

---

# **24.4 React Query Features**

### ✔ Automatic caching

### ✔ Automatic refetch (window focus)

### ✔ Auto retry

### ✔ Devtools

### ✔ Polling

### ✔ Infinite scroll

### ✔ Mutations (POST, PUT, DELETE)

### ✔ Optimistic Updates

---

# **🔵 25. Infinite Scroll (Advanced UI)**

### Example with manual fetch:

```jsx
useEffect(() => {
  fetch(`/api/posts?page=${page}`)
    .then(res => res.json())
    .then(newPosts => setPosts((prev) => [...prev, ...newPosts]));
}, [page]);
```

Detect scroll:

```jsx
window.onscroll = () => {
  if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
    setPage((p) => p + 1);
  }
};
```

---

# **26. WebSockets / Real-Time Data**

Useful for:

✔ Chats
✔ Live dashboard
✔ Stock prices
✔ Notifications

### Simple WebSocket:

```jsx
useEffect(() => {
  const socket = new WebSocket("ws://localhost:4000");

  socket.onmessage = (event) => {
    const msg = JSON.parse(event.data);
    setMessages(prev => [...prev, msg]);
  };

  return () => socket.close();
}, []);
```

---

# **🔵 27. API Performance Optimization**

Here are must-use techniques:

---

## **27.1 Debouncing (Search Box)**

Prevent too many API calls when typing.

✔ React Query built-in
✔ Manually using setTimeout
✔ Lodash debounce

---

## **27.2 Throttling (Scrolling, Resize, Drag)**

Limits API calls every X ms.

---

## **27.3 Memoization**

Avoid recalculating expensive data.

```jsx
const filtered = useMemo(() => {
  return data.filter(item => item.active);
}, [data]);
```

---

## **27.4 Avoid Unnecessary Re-renders**

✔ Use React.memo
✔ Use proper dependency arrays
✔ Use stable functions with useCallback

---

# **🔵 28. API Security Best Practices**

---

### ✔ 1. Never store JWT tokens in localStorage (vulnerable to XSS)

Prefer:
✔ httpOnly secure cookies
✔ memory storage

---

### ✔ 2. Validate all inputs before sending to API

### ✔ 3. Use HTTPS always

### ✔ 4. Avoid exposing API keys in frontend

### ✔ 5. Use environment variables (.env)

---

# **🔵 29. API Testing & Mocking**

Best tool: **MSW (Mock Service Worker)**

```
npm install msw
```

Mock API:

```js
rest.get("/api/users", (req, res, ctx) => {
  return res(ctx.json([{ id: 1, name: "Sakib" }]));
});
```

Lets you test React without hitting real APIs.

---

# **🔵 30. Complete Folder Structure (Enterprise Level)**

```
src/
 ├─ components/
 ├─ pages/
 ├─ hooks/
 │    ├─ useUsers.js
 │    └─ useProducts.js
 ├─ services/
 │    ├─ apiClient.js
 │    ├─ userService.js
 │    ├─ productService.js
 ├─ context/
 ├─ utils/
 ├─ config/
 ├─ assets/
 └─ App.js
```

---