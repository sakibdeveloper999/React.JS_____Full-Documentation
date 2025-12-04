
---

# 📘 **React.js-এ API Integration — Fetch API & Async Data (সম্পূর্ণ বাংলা ডকুমেন্টেশন)**

এই ডকুমেন্টেশনটি অত্যন্ত গভীরভাবে লেখা হয়েছে, যেটি আপনাকে React-এ API Integration সম্পর্কে **Beginner → Expert → Production → Enterprise** লেভেল পর্যন্ত নিয়ে যাবে।

---

# **🔵 1. API Integration কী? কেন দরকার?**

React একটি **frontend UI library** — এটি নিজে থেকে সার্ভার থেকে ডেটা ফেচ করতে পারে না।
ডেটা আনার জন্য আমাদের দরকার:

✔ API (Application Programming Interface)
✔ HTTP Request (GET, POST, PUT, DELETE, ইত্যাদি)
✔ Fetch API বা Axios বা React Query
✔ Async/Await

React-এ API Integration মানে:

> **Server → Data Fetch → React State-এ রাখা → UI-তে দেখানো**

---

# **🔵 2. API Request কীভাবে কাজ করে? (Step by Step)**

যখন আপনি API কল করেন:

1️⃣ React একটি HTTP Request পাঠায়
2️⃣ Server request গ্রহণ করে
3️⃣ JSON data পাঠায়
4️⃣ React সেই data state-এ রাখে
5️⃣ UI re-render হয়

**সংক্ষেপে:**
API → Response → UI Update

---

# **🔵 3. Fetch API Basics (React-এ সবচেয়ে বেশি ব্যবহৃত)**

### Example:

```js
fetch("https://jsonplaceholder.typicode.com/users")
  .then(res => res.json())
  .then(data => console.log(data));
```

⭕ সমস্যা:

* then() chaining দীর্ঘ হয়
* error handling কঠিন
* repetitive code বেশি

✔ সমাধান: async/await

---

# **🔵 4. Async/Await দিয়ে Fetch API**

```js
async function loadUsers() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const data = await res.json();
  console.log(data);
}
```

এটি অনেক ক্লিন ও readable।

---

# **🔵 5. React Component-এ API Call (useEffect ব্যবহার করে)**

React API call lifecycle:

✔ Component Render
✔ useEffect রান
✔ API call
✔ State update
✔ Re-render

---

### Example: GET Request (Basic)

```jsx
import { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUsers() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      setUsers(data);
      setLoading(false);
    }
    fetchUsers();
  }, []);

  if (loading) return <p>Loading...</p>;

  return (
    <div>
      {users.map(u => (
        <p key={u.id}>{u.name}</p>
      ))}
    </div>
  );
}
```

✔ useEffect শুধু একবার রান করবে
✔ async wrapper ব্যবহার করতে হবে

---

# **🔵 6. Error Handling যোগ করা (Production-Level)**

```jsx
const [error, setError] = useState(null);

useEffect(() => {
  async function fetchUsers() {
    try {
      const res = await fetch("https://api.example.com/users");
      if (!res.ok) throw new Error("Failed to load data");
      const data = await res.json();
      setUsers(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }
  fetchUsers();
}, []);
```

---

# **🔵 7. Post Request (API-তে ডেটা পাঠানো)**

```jsx
async function createUser() {
  const res = await fetch("/api/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name: "Sakib" }),
  });

  const data = await res.json();
  console.log(data);
}
```

---

# **🔵 8. PUT / PATCH / DELETE Requests**

| Method     | কাজ                   |
| ---------- | --------------------- |
| **PUT**    | সম্পূর্ণ রেকর্ড আপডেট |
| **PATCH**  | আংশিক আপডেট           |
| **DELETE** | রেকর্ড ডিলিট          |

---

### Example (DELETE):

```js
await fetch(`/api/users/1`, { method: "DELETE" });
```

---

# **🔵 9. Loading UI & Skeleton Screens**

API লোড হওয়ার সময় UI অনেক খালি মনে হয়।

BEST PRACTICES:

✔ “Loading…” দেখান
✔ Skeleton UI ব্যবহার করুন
✔ Spinner ব্যবহার করুন

---

# **🔵 10. Custom Hook দিয়ে API Call Reusable করা**

Without custom hook → কোড বার বার লিখতে হয়
With custom hook → এক জায়গায় লজিক থাকে

---

### Example: useFetch.js

```jsx
import { useEffect, useState } from "react";

export function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function load() {
      try {
        const res = await fetch(url);
        const json = await res.json();
        setData(json);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    }
    load();
  }, [url]);

  return { data, loading, error };
}
```

---

### Use in Component:

```jsx
const { data, loading, error } = useFetch("/api/users");
```

🔥 খুব clean
🔥 Reusable

---

# **🔵 11. Search + Filter API Integration (Debounce সহ)**

Search করলে অনেক সময় বহু request পাঠায় → performance সমস্যা।

✔ solution: debounce
✔ প্রতি 300ms-এ request পাঠানো

---

# **🔵 12. Pagination API Integration**

Two types:

1️⃣ Client-side pagination
2️⃣ Server-side pagination (Best)

---

### Server pagination:

```jsx
fetch(`/api/products?page=${page}&limit=10`)
```

---

# **🔵 13. Create Full CRUD App with Fetch API**

(পরবর্তী অংশে সম্পূর্ণ CRUD UI + Hooks + Services দেখানো হবে)

---

# **🔵 14. API Integration Best Practices (Must-Read)**

✔ Always handle loading & error
✔ Never call API without try/catch
✔ Create reusable hooks
✔ Use ENV variables for baseURL
✔ Separate API layer from UI

---

# **🔵 15. Avoid Common Mistakes**

❌ useEffect-এ async directly ব্যবহার করা
❌ fetch without error handling
❌ infinite loop dependency mistake
❌ storing sensitive data in localStorage

---

# **🔵 16. Clean Architecture for API Calls**

```
/src
 ├─ services/
 │    ├─ userService.js
 │    ├─ productService.js
 ├─ hooks/
 │    └─ useUsers.js
 ├─ components/
```

---

# **🔵 17. Fetch vs Axios vs React Query**

Same table translated:

| Feature         | Fetch     | Axios        | React Query | SWR                 |
| --------------- | --------- | ------------ | ----------- | ------------------- |
| Built-in        | ✔         | ❌            | ❌           | ❌                   |
| Simplicity      | মাঝারি    | খুব সহজ      | উন্নত       | উন্নত               |
| Interceptors    | ❌         | ✔            | N/A         | N/A                 |
| Auto caching    | ❌         | ❌            | ✔           | ✔                   |
| Auto refetch    | ❌         | ❌            | ✔           | ✔                   |
| Infinite scroll | ❌         | ❌            | ✔           | ✔                   |
| Best use case   | ছোট অ্যাপ | মাঝারি অ্যাপ | বড় অ্যাপ   | ড্যাশবোর্ড/UI অ্যাপ |

---

# **🔵 18. Advanced Axios Setup (Bangla Translation)**

```js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com",
  timeout: 12000,
});
```

### Request interceptor:

```js
axiosInstance.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${localStorage.getItem("token")}`;
  return config;
});
```

---

# **🔵 19. React Query – বাংলা ব্যাখ্যা**

React Query সব কঠিন অংশ করে দেয়:

✔ caching
✔ retry
✔ refetch
✔ background sync
✔ infinite scroll
✔ mutation
✔ optimistic updates

---

### Example:

```jsx
const { data, isLoading, error } = useQuery({
  queryKey: ["users"],
  queryFn: () => fetch("/api/users").then(r => r.json())
});
```

---

# **🔵 20. Infinite Scroll (Bangla)**

Scrolling শেষ হলে পরবর্তী ডেটা লোড:

```jsx
window.onscroll = () => {
  if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
    setPage((p) => p + 1);
  }
};
```

---

# **📘 8. Handling Loading, Error & Empty States **

React অ্যাপ্লিকেশনে **API Integration** এর সময় তিনটি UI স্টেট খুবই গুরুত্বপূর্ণ:

1. **Loading State**
2. **Error State**
3. **Empty State**

এই স্টেটগুলো ঠিকমতো না করলে ব্যবহারকারীর অভিজ্ঞতা (UX) খারাপ হয় — তাই এগুলো প্রফেশনাল অ্যাপের মতো হ্যান্ডেল করতে হবে।

---

## **🔹 8.1 Loading State কি?**

API কল যখন চলছে, তখন UI জানে না ডেটা এসেছে কি না।
এই সময় একটা লোডার বা skeleton দেখানো উচিত।

### ✅ কেন জরুরি?

* ব্যবহারকারীকে জানাতে যে ডেটা আসছে
* অ্যাপ হ্যাং হয়েছে বলে মনে না হয়
* UX মসৃণ হয়

### ❌ না করলে সমস্যা?

* ফ্লিকার
* হঠাৎ UI change
* ধরে নেয় API কাজ করছে না

---

## **🔹 8.2 Basic Loading State Example**

```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true);

    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setUsers(data))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <p>Loading...</p>;

  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

---

## **🔹 8.3 Error State কি?**

API কল ব্যর্থ হলে UI ব্যবহারকারীকে ভুলের কারণ দেখায়।

মনে রাখবেন:

* API সবসময় সফল নাও হতে পারে
* 500 server error
* 404 API not found
* Network error
* Internet disconnected

### 🎯 Error state ব্যবহারকারীর সঙ্গে যোগাযোগের মাধ্যম

---

## **🔹 8.4 Error State Example (Bangla Translation)**

```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("https://wrong-api-url.com")
      .then(res => {
        if (!res.ok) {
          throw new Error("API failed: " + res.status);
        }
        return res.json();
      })
      .then(data => setUsers(data))
      .catch(err => setError(err.message));
  }, []);

  if (error) return <p>Error: {error}</p>;

  return <p>Loaded</p>;
}
```

---

## **🔹 8.5 Empty State কি?**

ডেটা থাকলে দেখাবে
ডেটা না থাকলে user-friendly message দেখাবে।

### Example:

```jsx
if (!loading && users.length === 0) {
  return <p>No Users Found.</p>;
}
```

---

## **🔹 8.6 Loading + Error + Empty — Full Combined Example**

```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function loadUsers() {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/users");

        if (!res.ok) throw new Error("Failed to load users");

        const data = await res.json();
        setUsers(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    loadUsers();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  if (users.length === 0) return <p>No Users Found</p>;

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

---

## **🔹 8.7 Best Practices for UI States (Bangla Translation)**

### ✔ Always show a loader

অ্যাপ ধীর গতির মনে হবে না।

### ✔ Always show a clear error

যেমন:
⚠ "Something went wrong"
⚠ “Failed to load data”

### ✔ Empty state must be friendly

❌ "No data"
✔ "No users found. Try adding a new one."

### ✔ Avoid flashing UI

Loader → Real data
Not
Loader → Empty → Data (Bad)

---

## **🔹 8.8 Skeleton Loader (Professional UX)**

Skeleton loader UI আরও smooth করে।

Example:

```jsx
function Skeleton() {
  return (
    <div className="skeleton">
      <div className="line" />
      <div className="line short" />
    </div>
  );
}
```

---

## **🔹 8.9 Spinner vs Skeleton — কোনটা ভালো?**

| Feature      | Spinner    | Skeleton          |
| ------------ | ---------- | ----------------- |
| UX Quality   | Medium     | High              |
| Feeling      | Waiting    | Loading actual UI |
| Used in      | Small data | Large list/table  |
| Recommended? | Sometimes  | Always for list   |

---

## **🔹 8.10 Real-life Examples (Bangla Translation)**

👉 Loading product list
👉 Fetching user profile
👉 Rendering dashboard stats
👉 Searching items
👉 Filtering table

প্রতিটি জায়গায় loader/error state থাকা আবশ্যক।

---

# **📘 9. Paginated API Calls )**

Pagination মানে হলো — সব ডেটা একসাথে না এনে,
**ধাপে ধাপে অংশ করে আনা** (page-by-page)।

উদাহরণ:
Page 1 → first 10 items
Page 2 → next 10 items
Page 3 → next 10 items

---

# **🔹 9.1 Pagination কেন প্রয়োজন?**

Pagination এর প্রধান সুবিধা:

### ✔ Performance ভাল হয়

১০০০০ ডেটা একসাথে আনলে UI ব্লক হয়ে যাবে।

### ✔ Bandwidth কম লাগে

মোবাইলে ডেটা ব্যবহার কম হয়।

### ✔ UX smooth হয়

ডেটা লোড হতে হতে স্ক্রোল করতে পারে।

### ✔ Server load কমে

API রিকোয়েস্ট হালকা থাকে।

---

# **🔹 9.2 Real API Pagination Example**

ধরি API এমন রেসপন্স দেয়:

```
GET /products?page=1&limit=10
```

Response:

```json
{
  "page": 1,
  "total": 42,
  "limit": 10,
  "data": [
    { "id": 1, "name": "Shirt" },
    ...
  ]
}
```

---

# **🔹 9.3 React Pagination Example (useEffect + fetch)**

```jsx
function Products() {
  const [products, setProducts] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    async function loadProducts() {
      const res = await fetch(`https://api.example.com/products?page=${page}&limit=10`);
      const data = await res.json();
      setProducts(data.data);
    }

    loadProducts();
  }, [page]);

  return (
    <div>
      <ul>
        {products.map(p => (
          <li key={p.id}>{p.name}</li>
        ))}
      </ul>

      <button onClick={() => setPage(page - 1)} disabled={page === 1}>
        Previous
      </button>

      <button onClick={() => setPage(page + 1)}>
        Next
      </button>
    </div>
  );
}
```

---

# **🔹 9.4 Breakdown (Bangla Translation)**

1. `page` state রাখে আমরা কোন পেজে আছি
2. `useEffect` API কল করে যখন page পরিবর্তন হয়
3. Next/Previous বোতাম পেজ পরিবর্তন করে

---

# **🔹 9.5 Disable/Enable Buttons সঠিকভাবে করা**

```jsx
<button disabled={page === 1}>Previous</button>
<button disabled={page === totalPages}>Next</button>
```

---

# **🔹 9.6 Server-Side vs Client-Side Pagination (Bangla Translation)**

| Type                       | Description                       | When to Use                  |
| -------------------------- | --------------------------------- | ---------------------------- |
| **Client-side pagination** | সব ডেটা এনে React-এ paginate করা  | ছোট ডেটাসেট                  |
| **Server-side pagination** | API limit & page দিয়ে slice পাঠায় | বড় ডেটাসেট / Enterprise apps |

### ✔ Best practice → Always use **Server-side Pagination**

কারণ: scalable, faster, memory-friendly।

---

# **🔹 9.7 Maintaining Scroll Position**

Pagination করার পরে scroll top-এ চলে গেলে UX খারাপ।

Ideal UX:

* Page change → scroll stays
* Or smooth scroll:

```js
window.scrollTo({ top: 0, behavior: "smooth" });
```

---

# **🔹 9.8 Pagination + Loading State**

```jsx
if (loading) return <p>Loading page...</p>;
```

---

# **🔹 9.9 Pagination + Error Handling**

```jsx
catch(err => setError("Failed to load page " + page));
```

---

# **🔹 9.10 Numbered Pagination UI Example**

```jsx
<ul className="pagination">
  {[1,2,3,4,5].map(p => (
    <li
      key={p}
      onClick={() => setPage(p)}
      style={{ fontWeight: p === page ? "bold" : "normal" }}
    >
      {p}
    </li>
  ))}
</ul>
```

---

# **🔹 9.11 Best Practices for Pagination (Bangla Translation)**

### ✔ Limit ছোট রাখুন (10–20 items per page)

### ✔ Loading indicator ব্যবহার করুন

### ✔ Buttons disabled logic সঠিক রাখুন

### ✔ Same API shape maintain করুন

### ✔ Data append করবেন না (unless infinite scroll)

---

# **🔹 9.12 Pagination vs Infinite Scroll**

| Feature     | Pagination  | Infinite Scroll |
| ----------- | ----------- | --------------- |
| Control     | High        | Low             |
| UX          | Traditional | Modern          |
| Performance | Good        | Very good       |
| Best for    | Admin panel | Social feeds    |

বেশিরভাগ ড্যাশবোর্ডে Pagination ভাল।
Feed টাইপ অ্যাপে Infinite Scroll।

---

# **📘 10. Infinite Scroll API Handling **

Infinite Scroll মানে হলো:

✔ স্ক্রল যত নিচে যাবে →
✔ তত নতুন ডেটা অটোমেটিক লোড হবে →
✔ Pagination বাটনের প্রয়োজন নেই

এই ফিচারটি প্রধানত ব্যবহার হয়:

* Facebook News Feed
* Instagram feed
* YouTube feed
* TikTok
* E-commerce product feed
* Blog list

---

# **🔹 10.1 Infinite Scroll কেন জনপ্রিয়?**

### ✔ Smooth User Experience

User বারবার Next বাটন চাপতে হয় না।

### ✔ Engagement বাড়ে

User continuous ডেটা দেখতে থাকে → bounce কমে।

### ✔ Modern UI

২০২৫ এর UI স্ট্যান্ডার্ডে Infinite scroll খুব common।

### ✔ Mobile-friendly

স্ক্রিন ছোট হওয়ায় অটো-লোড UX আরও ভাল।

---

# **🔹 10.2 Infinite Scroll এর মূল ধারণা**

Infinite scroll করার জন্য ৪টি জিনিস লাগে:

1. **Page number**
2. **Load more condition** (যেমন: user bottom এ এলে)
3. **Data append** (পুরানো ডেটার সাথে নতুন ডেটা যুক্ত করা)
4. **Loading indicator**

---

# **🔹 10.3 Basic Infinite Scroll API Example (Bangla Translation)**

```jsx
function Posts() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadPosts();
  }, [page]);

  async function loadPosts() {
    setLoading(true);

    const res = await fetch(
      `https://jsonplaceholder.typicode.com/posts?_limit=10&_page=${page}`
    );

    const data = await res.json();

    setPosts(prev => [...prev, ...data]); // append new data
    setLoading(false);
  }

  // scroll listener
  useEffect(() => {
    function handleScroll() {
      if (
        window.innerHeight + window.scrollY >=
        document.body.offsetHeight - 200
      ) {
        setPage(prev => prev + 1);
      }
    }

    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, []);

  return (
    <div>
      {posts.map(p => (
        <p key={p.id}>{p.title}</p>
      ))}

      {loading && <p>Loading more...</p>}
    </div>
  );
}
```

---

# **🔹 10.4 কী হলো গুরুত্বপূর্ণ অংশের ব্যাখ্যা**

### ✔ **append new data**

```js
setPosts(prev => [...prev, ...data]);
```

Infinite scroll-এর মূল শক্তি এটি।

### ✔ Scroll condition

যখন user screen-এর নিচে আসে:

```js
window.innerHeight + window.scrollY >= document.body.offsetHeight - 200
```

### ✔ Loading state

নতুন ডেটা আসছে → UI জানাবে।

---

# **🔹 10.5 Intersection Observer — আধুনিক পদ্ধতি**

Scroll listener এর পরিবর্তে আধুনিক পদ্ধতি হলো:

✔ **Intersection Observer**
✔ Smooth
✔ Lightweight
✔ No scroll calculation needed

---

# **🔹 10.6 Infinite Scroll using Intersection Observer (Best Way)**

```jsx
import { useEffect, useRef, useState } from "react";

function Posts() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(1);
  const loaderRef = useRef(null);

  useEffect(() => {
    async function loadPosts() {
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/posts?_limit=10&_page=${page}`
      );
      const data = await res.json();
      setPosts(prev => [...prev, ...data]);
    }

    loadPosts();
  }, [page]);

  useEffect(() => {
    const observer = new IntersectionObserver(
      entries => {
        if (entries[0].isIntersecting) {
          setPage(prev => prev + 1);
        }
      },
      { threshold: 1 }
    );

    if (loaderRef.current) {
      observer.observe(loaderRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div>
      {posts.map(post => (
        <p key={post.id}>{post.title}</p>
      ))}

      <div ref={loaderRef}>
        <p>Loading...</p>
      </div>
    </div>
  );
}
```

---

# **🔹 10.7 Intersection Observer কেন সেরা?**

### ✔ Scroll listener দরকার নেই

### ✔ Browser optimized

### ✔ Low CPU usage

### ✔ Clean code

### ✔ Precise trigger (smooth loading)

---

# **🔹 10.8 Infinite Scroll এর বিশেষ চ্যালেঞ্জ**

| Problem            | Why happens                   | Solution                      |
| ------------------ | ----------------------------- | ----------------------------- |
| Too many API calls | scroll আবার আবার trigger হয়   | Debounce / threshold increase |
| Duplicate data     | API returns overlapping items | Use unique IDs                |
| Slow loading       | Large data                    | Use caching                   |
| Memory leak        | পুরানো listener               | Cleanup required              |

---

# **🔹 10.9 Debounced Infinite Scroll (Optimized)**

```js
let timer;
function handleScroll() {
  clearTimeout(timer);
  timer = setTimeout(() => {
    // check if bottom reached
  }, 200);
}
```

---

# **🔹 10.10 Infinite Scroll Loading Skeleton**

Better UX:

```jsx
<div className="skeleton-list">
  <div className="skeleton" />
  <div className="skeleton" />
  <div className="skeleton" />
</div>
```

---

# **🔹 10.11 Best Practices for Infinite Scroll **

### ✔ API must support `limit` & `page`

### ✔ Reduce API calls

### ✔ Show loading skeleton

### ✔ Stop loading when no more data

### ✔ Use Intersection Observer

### ✔ Avoid infinite loop (page increases too fast)

### ✔ Use caching when possible

### ✔ Do not fetch same page twice

---

# **🔹 10.12 UX Considerations**

### ✔ Show "You reached the end"

### ✔ Provide Back to Top button

### ✔ List should not jump

### ✔ Product list should include lazy-loaded images

---

# **🔹 10.13 Infinite Scroll vs Pagination**

| Feature      | Infinite Scroll | Pagination  |
| ------------ | --------------- | ----------- |
| UX           | Modern, smooth  | Traditional |
| Performance  | Better          | Depends     |
| User control | Low             | High        |
| Admin Panel  | ❌ No            | ✔ Yes       |
| Social Feed  | ✔ Yes           | ❌ No        |

---
# **📘 11. Search API Integration **

Search functionality হলো একটি গুরুত্বপূর্ণ ফিচার যেটি ব্যবহারকারীর জন্য ডেটা খোঁজা সহজ করে।
React-এ search API integration করলে আমরা user input অনুযায়ী **server থেকে filtered data** fetch করতে পারি।

---

# **🔹 11.1 Search API কেন দরকার?**

### ✔ User-friendly

* ব্যবহারকারী তার প্রয়োজনীয় item দ্রুত খুঁজে পায়।

### ✔ Performance optimization

* Server-side filtering → frontend-এ সব ডেটা লোড করতে হয় না।

### ✔ Real-time results

* Typing করে সাথে সাথে ডেটা দেখানো যায়।

---

# **🔹 11.2 Basic Search API Example**

```jsx
function SearchUsers() {
  const [query, setQuery] = useState("");
  const [users, setUsers] = useState([]);

  useEffect(() => {
    if (!query) return;

    async function fetchUsers() {
      const res = await fetch(`https://api.example.com/users?search=${query}`);
      const data = await res.json();
      setUsers(data);
    }

    fetchUsers();
  }, [query]);

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search users..."
      />
      <ul>
        {users.map(u => <li key={u.id}>{u.name}</li>)}
      </ul>
    </div>
  );
}
```

---

# **🔹 11.3 Important Notes**

1. **Query debounce করা উচিত**

   * প্রতিটি key stroke-এ API কল করলে server overload হয়।
   * সাধারণত 300–500ms debounce করা হয়।

2. **Empty query handling**

   * যদি input খালি → সব ডেটা বা কিছু default দেখানো যায়।

3. **Error & Loading state**

   * User জানুক ডেটা আসছে বা কোন error হয়েছে।

---

# **🔹 11.4 Debounce Example (Bangla Translation)**

```jsx
import { useState, useEffect } from "react";

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

export default useDebounce;
```

### ব্যবহার:

```jsx
const debouncedQuery = useDebounce(query, 500);

useEffect(() => {
  if (!debouncedQuery) return;

  fetch(`https://api.example.com/users?search=${debouncedQuery}`)
    .then(res => res.json())
    .then(data => setUsers(data));
}, [debouncedQuery]);
```

---

# **🔹 11.5 Search + Pagination / Infinite Scroll**

Search করলে pagination বা infinite scroll একইভাবে কাজ করে।

* Page parameter রাখুন
* Query parameter update করুন
* Data append বা replace করুন

Example:

```jsx
fetch(`/api/users?search=${query}&page=${page}&limit=10`);
```

---

# **🔹 11.6 Best Practices for Search API Integration**

### ✔ Always debounce input

### ✔ Handle empty query gracefully

### ✔ Show loading indicator

### ✔ Show “No results found” message

### ✔ Cancel previous requests (Optional: Axios cancel token)

### ✔ Combine with pagination or infinite scroll

### ✔ Server-side search preferred (performance)

---

# **🔹 11.7 Error Handling Example**

```jsx
try {
  const res = await fetch(`/api/users?search=${query}`);
  if (!res.ok) throw new Error("Search failed");
  const data = await res.json();
  setUsers(data);
} catch (err) {
  setError(err.message);
}
```

---

# **🔹 11.8 UX Considerations**

* Typing → Loading indicator show
* No results → Friendly message
* Highlight matched query (optional)
* Keyboard navigation (Arrow Up/Down)

---

# **🔹 11.9 Alternative Libraries**

* **Axios** → Cancel previous requests easily
* **React Query / SWR** → Auto caching, background update, stale-while-revalidate

---

# **📘 12. Caching, Retry & Background Updates **

Advanced API handling-এ caching, retry, এবং background updates হলো প্রফেশনাল React অ্যাপ্লিকেশনের মূল স্তম্ভ।
React Query বা SWR ব্যবহার করলে এগুলো খুব সহজ হয়।

---

# **🔹 12.1 Caching কি? কেন দরকার?**

### ✔ অর্থ:

Cache হলো **previously fetched ডেটা সংরক্ষণ**।

### ✔ সুবিধা:

1. একাধিক component এক API call ব্যবহার করতে পারে।
2. Data আবার fetch করতে হয় না → fast UI
3. Server load কমে
4. Offline বা slow network-এ smooth experience

---

# **🔹 12.2 React Query দিয়ে Cache Example**

```jsx
import { useQuery } from "@tanstack/react-query";

function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: () =>
      fetch("/api/users").then(res => res.json()),
    staleTime: 1000 * 60 * 5, // 5 min
    cacheTime: 1000 * 60 * 30 // 30 min
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error!</p>;

  return (
    <ul>
      {data.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

---

# **🔹 12.3 Cache Settings Explained (Bangla Translation)**

* `staleTime` → কতক্ষণ পর্যন্ত data fresh মনে হবে
* `cacheTime` → কতক্ষণ পর্যন্ত data memory-তে রাখা হবে
* যদি data fresh থাকে → নতুন fetch হবে না
* Component remount হলেও cached data ব্যবহার করা হবে

---

# **🔹 12.4 Retry Failed Requests**

Network failure বা server error হলে retry করতে পারে।

```jsx
useQuery({
  queryKey: ["users"],
  queryFn: () => fetch("/api/users").then(res => res.json()),
  retry: 3, // 3 বার চেষ্টা করবে
  retryDelay: attemptIndex => Math.min(1000 * 2 ** attemptIndex, 30000)
});
```

* Exponential backoff → network overload কমায়

---

# **🔹 12.5 Background Updates (Refetching)**

Background update মানে হলো:

✔ Cached data দেখাচ্ছে
✔ ব্যাকগ্রাউন্ডে fresh data fetch হচ্ছে
✔ Data update হলেই UI auto refresh

React Query default-এ `refetchOnWindowFocus: true` দেয়।

---

# **🔹 12.6 Stale-While-Revalidate (SWR)**

SWR / React Query pattern:

1. Show cached data immediately
2. Fetch fresh data in background
3. Update UI when fresh data arrives

Example with SWR:

```jsx
import useSWR from "swr";

const fetcher = url => fetch(url).then(res => res.json());

function Users() {
  const { data, error } = useSWR("/api/users", fetcher, {
    refreshInterval: 30000 // 30 sec update
  });

  if (!data) return <p>Loading...</p>;
  if (error) return <p>Error</p>;

  return data.map(u => <p key={u.id}>{u.name}</p>);
}
```

---

# **🔹 12.7 Best Practices for Caching & Background Updates**

### ✔ Cached data show immediately (good UX)

### ✔ Always retry failed requests (network resilience)

### ✔ Avoid stale data too long

### ✔ Background refresh at reasonable intervals

### ✔ Use React Query or SWR for auto-handling

---

# **🔹 12.8 Combining Infinite Scroll + Caching**

* Previous pages cached → scroll back fast
* Next page fetched in background → smooth experience

---

# **🔹 12.9 Error + Retry UX**

* Show loader while retrying
* Limit max retry attempts
* Friendly message if failed after all retries

```jsx
{error && <p>Failed to load data. Retrying...</p>}
```

---

# **🔹 12.10 Notes for Large Scale Apps**

1. Cached API data helps offline or slow networks
2. Shared cache between multiple components
3. Avoid duplicate API calls → performance boost
4. Use proper staleTime / cacheTime
5. SWR / React Query handles this elegantly

---

# **📘 13. WebSockets / Real-Time Data Integration **

React অ্যাপ্লিকেশনে অনেক ক্ষেত্রে **real-time data** দরকার হয়, যেমন:

* Chat application
* Live notifications
* Stock market apps
* Multiplayer games
* Live sports score

এই ক্ষেত্রে **API polling** ব্যবহার করলে performance খারাপ হয়। এর পরিবর্তে **WebSocket** ব্যবহার করা হয়।

---

# **🔹 13.1 WebSocket কি?**

### ✔ সংজ্ঞা:

WebSocket হলো **full-duplex connection** যা client ↔ server এর মধ্যে **continuous open connection** রাখে।

### ✔ সুবিধা:

1. Server instant message পাঠাতে পারে
2. Low latency
3. Network requests কম
4. Real-time updates smooth

---

# **🔹 13.2 WebSocket vs HTTP**

| Feature    | HTTP             | WebSocket                |
| ---------- | ---------------- | ------------------------ |
| Connection | Request/Response | Persistent               |
| Data       | Polling          | Push / Instant           |
| Latency    | High             | Low                      |
| Use case   | API fetch        | Live chat, notifications |

---

# **🔹 13.3 Basic WebSocket in React**

```jsx
import { useEffect, useState } from "react";

function Chat() {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const ws = new WebSocket("wss://example.com/socket");

    ws.onmessage = event => {
      const data = JSON.parse(event.data);
      setMessages(prev => [...prev, data]);
    };

    ws.onerror = err => {
      console.error("WebSocket error:", err);
    };

    return () => ws.close(); // cleanup on unmount
  }, []);

  return (
    <ul>
      {messages.map((m, i) => (
        <li key={i}>{m.text}</li>
      ))}
    </ul>
  );
}
```

---

# **🔹 13.4 WebSocket Lifecycle in React**

1. Component mount → WebSocket open
2. Server message → onmessage → update state
3. Component unmount → close connection
4. Error handling → onerror
5. Reconnect logic → optional

---

# **🔹 13.5 Best Practices for Real-Time Data**

### ✔ Cleanup connections on unmount

```js
return () => ws.close();
```

### ✔ Handle reconnects

* Network drop বা server restart হলে reconnect logic রাখতে হবে।

### ✔ Avoid memory leaks

* State update করার সময় mounted check করা দরকার।

### ✔ Throttle frequent updates

* অনেক message একসাথে এলে UI block হতে পারে।

---

# **🔹 13.6 Combining WebSocket + React Query / SWR**

* Cached API data + WebSocket → Optimistic UI
* Example: Product stock list
* Initial fetch → cached
* Real-time WebSocket → update state

---

# **🔹 13.7 Notifications Example**

```jsx
useEffect(() => {
  const ws = new WebSocket("wss://example.com/notifications");

  ws.onmessage = e => {
    const notification = JSON.parse(e.data);
    toast(notification.message); // show toast
  };

  return () => ws.close();
}, []);
```

---

# **🔹 13.8 Security Considerations for WebSocket**

* Always use **wss://** (encrypted)
* Authenticate users (token-based)
* Validate server messages
* Limit max message size
* Prevent XSS / injection attacks

---

# **🔹 13.9 Performance Optimization**

* Avoid unnecessary state updates
* Batch messages if high frequency
* Lazy load non-critical data
* Use virtualization for long lists

---

# **🔹 13.10 Summary **

* WebSocket = Real-time data
* Full-duplex, low latency, push updates
* Proper cleanup & error handling আবশ্যক
* Combine with caching & optimistic UI → Pro-level UX

---
# **📘 14. Security & Performance Optimization **

React অ্যাপ্লিকেশনে API Integration-এর সময় **Security এবং Performance** দুইই অত্যন্ত গুরুত্বপূর্ণ।
প্রফেশনাল অ্যাপ্লিকেশন তৈরি করতে হলে এগুলো অবশ্যই মানতে হবে।

---

# **🔹 14.1 Security Best Practices**

### 1. **HTTPS ব্যবহার করুন**

* সব API কল **HTTPS** দিয়ে করা উচিত
* `http://` ব্যবহার করলে man-in-the-middle attack-এর ঝুঁকি থাকে

### 2. **Authentication & Authorization**

* Token-based authentication (JWT, OAuth2) ব্যবহার করুন
* API endpoints কে protect করুন
* Sensitive action শুধুমাত্র authorized user করতে পারবে

### 3. **Hide API Keys / Secrets**

* Never commit API keys to Git
* Use environment variables (`.env`)
* Frontend-only keys should have **limited permissions**

### 4. **Validate Data**

* Server থেকে আসা ডেটা validate করুন
* Prevent XSS / Injection / Malicious content

### 5. **Rate Limiting**

* Server-side limit রাখুন → prevent DDoS
* Frontend-এ debounce / throttle ব্যবহার করুন

---

# **🔹 14.2 Performance Optimization Best Practices**

### 1. **Avoid unnecessary re-renders**

* API data পরিবর্তনের আগে check করুন
* React.memo / useMemo / useCallback ব্যবহার করুন

### 2. **Use Pagination / Infinite Scroll**

* বড় dataset একসাথে load করবেন না
* Pagination / Infinite scroll → low memory, fast render

### 3. **Lazy Loading Images / Components**

* Large image / component lazy load করুন
* Suspense + React.lazy ব্যবহার করুন

### 4. **Caching**

* React Query / SWR → cached data, background updates
* Avoid repeated network calls

### 5. **Debounce / Throttle API Calls**

* Search field / infinite scroll → debounce/throttle
* Reduce unnecessary server requests

---

# **🔹 14.3 Optimized API Integration Pattern**

```
/src
 ├─ api/
 │   ├─ axiosInstance.js
 │   ├─ userService.js
 │   └─ productService.js
 ├─ hooks/
 │   ├─ useUsers.js
 │   └─ useProducts.js
 ├─ components/
 │   ├─ Users.js
 │   └─ Products.js
 └─ utils/
     └─ debounce.js
```

* **api/** → সব API call centralized
* **hooks/** → reusable hooks
* **components/** → UI
* **utils/** → debounce, helpers

---

# **🔹 14.4 API Request Optimization**

### ✔ Batch requests when possible

### ✔ Use query parameters instead of fetching all data

### ✔ Use selective fields (`?fields=id,name`)

### ✔ Minimize payload size

---

# **🔹 14.5 Security + Performance Summary**

* **Secure API** → HTTPS, auth, validate, rate-limit
* **Optimized API** → pagination, caching, lazy loading, debounce
* **React Integration** → custom hooks, memoization, proper cleanup

---

# **🔹 14.6 Common Mistakes to Avoid**

❌ Calling API in every render without useEffect
❌ Not handling errors / loading
❌ Storing sensitive info in localStorage without encryption
❌ Fetching entire dataset at once
❌ Not cleaning up WebSocket or event listeners

---

# **📘 15. Testing API Integration / Mocking (MSW)**

React অ্যাপ্লিকেশনে **API Integration testing** খুবই গুরুত্বপূর্ণ।
Testing করলে আপনি নিশ্চিত হতে পারেন API কল ঠিকমতো কাজ করছে এবং component expected behavior দেখাচ্ছে।

---

# **🔹 15.1 কেন Testing দরকার?**

### ✔ Early bug detection

* Network error বা API response mismatch ধরতে সহজ

### ✔ Predictable UI

* Component behavior stable হয়

### ✔ Mocked APIs

* Real server dependency কমে
* Offline এও tests run করতে পারে

---

# **🔹 15.2 MSW (Mock Service Worker) কি?**

* MSW হলো **API mocking library**
* Browser বা Node environment-এ API calls intercept করে
* Real server ব্যবহার না করে fake responses return করে
* React testing এর জন্য ideal

---

# **🔹 15.3 Basic MSW Setup Example**

1️⃣ Install MSW

```bash
npm install msw --save-dev
```

2️⃣ Create `mocks/handlers.js`

```js
import { rest } from 'msw';

export const handlers = [
  rest.get('/api/users', (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json([
        { id: 1, name: 'John' },
        { id: 2, name: 'Jane' },
      ])
    );
  }),
];
```

3️⃣ Create `mocks/browser.js`

```js
import { setupWorker } from 'msw';
import { handlers } from './handlers';

export const worker = setupWorker(...handlers);
```

4️⃣ Start worker in `index.js`

```js
if (process.env.NODE_ENV === 'development') {
  const { worker } = require('./mocks/browser');
  worker.start();
}
```

---

# **🔹 15.4 Testing a Component with MSW**

```jsx
import { render, screen } from '@testing-library/react';
import Users from './Users';
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json([{ id: 1, name: 'John' }]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('loads and displays users', async () => {
  render(<Users />);
  expect(await screen.findByText('John')).toBeInTheDocument();
});
```

---

# **🔹 15.5 Best Practices for API Testing**

1. Always **mock external APIs** → avoid network dependency
2. Test **loading, error, empty states**
3. Test **retry logic & pagination**
4. Combine **MSW + React Testing Library** → realistic tests
5. Keep tests **fast & isolated**
6. Cleanup mocks after each test

---

# **🔹 15.6 Advantages of MSW over traditional mocks**

| Feature       | Traditional mocks | MSW                           |
| ------------- | ----------------- | ----------------------------- |
| Network layer | Mock function     | Intercept actual HTTP request |
| Browser       | ❌                 | ✔                             |
| Node / Jest   | ✔                 | ✔                             |
| Realistic     | Medium            | High                          |
| Reusability   | Medium            | High                          |

---

# **🔹 15.7 Notes**

* MSW ব্যবহার করলে production API untouched থাকে
* Realistic API behavior simulate করা যায়
* Combine with **React Query / SWR** → test caching, background refetch

---

# **📘 16. Full Project Folder Structure + Final Best Practices**

React অ্যাপ্লিকেশনে API Integration এবং Advanced State Management এর জন্য **সঠিক project structure** খুবই গুরুত্বপূর্ণ।
সঠিক structure থাকলে project scalable, maintainable এবং readable হয়।

---

# **🔹 16.1 Recommended Project Structure**

```
/src
 ├─ api/                     # API calls centralized
 │   ├─ axiosInstance.js      # Axios or fetch config
 │   ├─ userService.js
 │   └─ productService.js
 ├─ components/              # UI components
 │   ├─ Users.js
 │   ├─ Products.js
 │   └─ Loader.js
 ├─ hooks/                   # Custom hooks
 │   ├─ useUsers.js
 │   ├─ useProducts.js
 │   └─ useDebounce.js
 ├─ pages/                   # Route pages
 │   ├─ Home.js
 │   ├─ Dashboard.js
 │   └─ Profile.js
 ├─ context/                 # Context API (if used)
 │   └─ AuthContext.js
 ├─ utils/                   # Helper functions
 │   ├─ debounce.js
 │   └─ formatDate.js
 ├─ mocks/                   # API mocking for tests
 │   ├─ handlers.js
 │   └─ browser.js
 ├─ App.js
 ├─ index.js
 └─ routes.js
```

---

# **🔹 16.2 Folder Responsibilities (Bangla Translation)**

* **api/** → সব API কল centralized
* **components/** → reusable UI components
* **hooks/** → reusable logic hooks (useUsers, useDebounce)
* **pages/** → route-specific components
* **context/** → global state (optional, React Query preferred)
* **utils/** → helper functions
* **mocks/** → API mocking (MSW)
* **App.js / index.js** → root setup

---

# **🔹 16.3 Final Best Practices for API Integration in React**

### 1. **Use Custom Hooks**

* Reusable logic
* Cleaner components

### 2. **Always handle UI states**

* Loading, Error, Empty

### 3. **Debounce or Throttle**

* Reduce unnecessary API calls

### 4. **Use Pagination / Infinite Scroll**

* Large dataset handling

### 5. **Caching & Background Updates**

* React Query / SWR → optimized UX

### 6. **Error & Retry**

* Network resilience → retry failed requests

### 7. **Security**

* HTTPS
* Authenticated API calls
* Validate user inputs

### 8. **Testing**

* Use MSW / React Testing Library
* Test loading, error, pagination, infinite scroll

### 9. **Performance Optimization**

* Lazy load components / images
* Memoize where needed
* Avoid unnecessary re-renders

### 10. **WebSockets / Real-Time**

* Use for live data
* Cleanup and throttle updates

---

# **🔹 16.4 Summary of Full API Integration Flow**

1. **Component mounts** → API call triggered
2. **Loading state** → show loader
3. **API success** → set data → update UI
4. **API error** → show friendly error
5. **Empty data** → show empty state
6. **Pagination / Infinite Scroll** → load more data
7. **Caching / Background Update** → smooth UX
8. **Testing** → ensure predictable behavior
9. **WebSockets** → real-time updates (if required)
10. **Security & Performance** → HTTPS, auth, throttling, memoization

---

# **🔹 16.5 Pro-Level Tips**

* Keep **API logic separate** from UI
* Always **cleanup listeners** (WebSocket, scroll)
* Centralize **error handling**
* Use **environment variables** for API URLs / secrets
* Consider **global state management** (React Query / Context / Redux)
* Modular, reusable components and hooks → scale easier

---

# ✅ Congratulations!

এটি **React.js API Integration এর সম্পূর্ণ Deep Dive Bangla**।
আপনি এখন:

* Fetch API / async data
* Form validation / Formik/Yup
* Pagination / Infinite Scroll
* Real-time WebSockets
* Caching / Retry / Background Updates
* Security & Performance
* Testing (MSW)

সব কিছু **step-by-step, best practices সহ, production-ready level** শিখেছেন।

---
