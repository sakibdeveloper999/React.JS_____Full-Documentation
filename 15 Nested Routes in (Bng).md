
---

# 🔥 **উল্টিমেট ডীপ ডাইভ: Nested Routes & Dynamic Routes React.js-এ (React Router v6+)**

---

# সংক্ষিপ্ত ওভারভিউ — Nested & Dynamic Routes কী?

* **Nested routes** আপনাকে parent/child route ডিফাইন করতে দেয় যাতে UI কম্পোজ হয়: parent route কিছু layout (header/sidebar) রেন্ডার করে এবং child route তার ভিতরে রেন্ডার হয়। এতে layout কোড বারবার লিখতে হয় না এবং nested UI structure route configuration-এর সাথে মিলে যায়।
* **Dynamic routes** route parameters ব্যবহার করে (যেমন `/products/:productId`) যাতে এক route definition দিয়ে অনেক URL মেলানো যায় এবং component-এর ভিতরে `useParams` দিয়ে parameters পড়া যায়।

React Router v6 route গুলিকে একটি ট্রি হিসেবে মডেল করে। Parent route `<Outlet />` ব্যবহার করে child route render করে।

---

# Install & Basic Setup

```bash
npm install react-router-dom@6
# অথবা
yarn add react-router-dom@6
```

App wrap করা:

```jsx
// index.jsx
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

---

# 3) Minimal Nested Routes উদাহরণ (ধাপে ধাপে)

ফাইল স্ট্রাকচার (প্রস্তাবিত):

```
src/
  App.jsx
  pages/
    Dashboard.jsx
    Users/
      UsersLayout.jsx
      UsersList.jsx
      UserDetails.jsx
      UserSettings.jsx
  components/
    Header.jsx
```

App routes (v6):

```jsx
// App.jsx
import { Routes, Route, Link } from "react-router-dom";
import Dashboard from "./pages/Dashboard";
import UsersLayout from "./pages/Users/UsersLayout";
import UsersList from "./pages/Users/UsersList";
import UserDetails from "./pages/Users/UserDetails";
import UserSettings from "./pages/Users/UserSettings";

export default function App() {
  return (
    <>
      <nav>
        <Link to="/">Dashboard</Link> | <Link to="/users">Users</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Dashboard />} />

        {/* Parent route: Users layout */}
        <Route path="users" element={<UsersLayout />}>
          {/* index route shown when /users */}
          <Route index element={<UsersList />} />

          {/* dynamic child route: /users/:userId */}
          <Route path=":userId" element={<UserDetails />} />

          {/* nested deeper: /users/:userId/settings */}
          <Route path=":userId/settings" element={<UserSettings />} />
        </Route>

        {/* fallback */}
        <Route path="*" element={<div>404 Not Found</div>} />
      </Routes>
    </>
  );
}
```

Users layout যেখানে `<Outlet />` ব্যবহার করছে:

```jsx
// pages/Users/UsersLayout.jsx
import { Outlet, Link } from "react-router-dom";

export default function UsersLayout() {
  return (
    <div>
      <h2>Users Area</h2>
      <p>Shared অংশ: filters, tabs, breadcrumbs...</p>
      <nav>
        <Link to="/users">All users</Link>
      </nav>

      {/* Child routes এখানে render হবে */}
      <Outlet />
    </div>
  );
}
```

Child component যেখানে `:userId` পড়ছে:

```jsx
// pages/Users/UserDetails.jsx
import { useParams, Link } from "react-router-dom";

export default function UserDetails() {
  const { userId } = useParams();
  return (
    <div>
      <h3>User #{userId}</h3>
      <p>Profile content...</p>
      <Link to="settings">Settings</Link> {/* relative link */}
    </div>
  );
}
```

Notes:

* `<Route path="users" element={<UsersLayout />}>` parent route ডিফাইন করে।
* Child routes parent `<Route>`-এর ভিতরে ডিক্লেয়ার করা হয় (nested), এবং `<Outlet />`-এ render হয়।
* `index` route parent path পুরোপুরি match হলে render হয় (`/users`)।

---

# Dynamic Nested উদাহরণ: deeper nesting

Example route: `/products/:productId/reviews/:reviewId`

```jsx
<Routes>
  <Route path="products" element={<ProductsLayout />}>
    <Route index element={<ProductsList />} />
    <Route path=":productId" element={<ProductPage />}>
      <Route index element={<ProductOverview />} />
      <Route path="reviews/:reviewId" element={<ReviewDetails />} />
    </Route>
  </Route>
</Routes>
```

এখানে:

* `ProductPage` product header এবং `<Outlet />` render করে overview বা particular review-এর জন্য।
* Child routes relative (`reviews/:reviewId`) এবং `/products/:productId` এর নিচে mount হবে।

---

# গুরুত্বপূর্ণ API গুলো যা Nested/Dynamic Routes এর সাথে ব্যবহার হয়

* `Outlet` — child routes-এর জন্য placeholder।
* `useParams()` — dynamic params পড়ার জন্য (যেমন `productId`)।
* `useNavigate()` — programmatically navigate করার জন্য (relative navigation supported)।
* `useLocation()` — URL/state inspect করার জন্য (modals-এর ক্ষেত্রে কাজে লাগে)।
* `<NavLink>` — nested menu-এর active link styling (`end` prop ব্যবহার করে exact match)।
* Route `index` এবং pathless (কোনো `path` নেই) layout routes grouping এর জন্য।

---

# Layout Routes & Pathless Routes

**Layout route**: parent route যার `element` shared UI render করে; children `<Outlet />`-এ render হয়। Headers, sidebars, breadcrumbs, admin console shells-এর জন্য Layout route ব্যবহার করা হয়।

**Pathless route** (কোনো `path` নেই) grouping/layout এর জন্য যা URL-এ কোনো segment যোগ করে না:

```jsx
<Route element={<AdminLayout />}>
  <Route path="admin/users" element={<AdminUsers />} />
  <Route path="admin/settings" element={<AdminSettings />} />
</Route>
```

Multiple route এক layout share করতে চাইলে pathless route সুবিধাজনক।

---

# Index Routes

`<Route index element={...} />` parent path-এর জন্য default child content দেখায়। `path=""` এর চেয়ে index routes প্রেফার করা হয়।

---

# Relative Links & Navigation

* `<Link to="settings">` inside `/users/:userId` goes to `/users/:userId/settings`. Relative linking nested routes-এর জন্য safer।
* `useNavigate()` relative navigation support করে: `navigate("..")` উপরের level এ যায়।

---

# Lazy-loading & Performance

বড় nested tree-এর জন্য route components lazy-load করা যায়:

```jsx
import { lazy, Suspense } from "react";
const UsersLayout = lazy(() => import("./Users/UsersLayout"));

<Route path="users" element={
  <Suspense fallback={<div>Loading...</div>}>
    <UsersLayout />
  </Suspense>
}>
  ...
</Route>
```

Best practice: lazy-load chunk boundaries layout বা top-level pages এ, ছোট component-এর জন্য নয়।

---

# Protected Routes (Authentication) Pattern

Parent element দিয়ে multiple child routes gate করা যায়:

```jsx
function RequireAuth({ children }) {
  const isAuth = useAuth(); // আপনার hook
  if (!isAuth) return <Navigate to="/login" replace />;
  return children;
}

// routes
<Route element={<RequireAuth />}>
  <Route path="dashboard" element={<Dashboard />} />
  <Route path="account" element={<Account />} />
</Route>
```

Parent wrapper child routes-এর জন্য auth check বারবার না লিখে দেয়।

---

# Modal Routes / Overlay UI Pattern

Modal overlay route background preserve করে render করা যায়:

* Modal link open করার সময় background `location` state-এ save করুন।
* Top-level এ modal route render করুন overlay হিসেবে অথবা normal page হিসেবে direct navigation হলে।

---

# Nested Routes ব্যবহার করার কারণ (Benefits)

* **UI structure mirror**: route tree UI composition অনুযায়ী।
* **DRY layouts**: header/sidebar বারবার না লিখে shared layout।
* **Cleaner route management**: centralized route declaration।
* **Relative linking**: nested parts সহজে maintainable।
* **Supports code-splitting**: relevant chunk load করা যায়।

---

# কখন Nested Routes ব্যবহার করবেন না

* UI ছোট/simple হলে nesting অপ্রয়োজনীয়।
* Server-side routing/SSR-first SEO দরকার হলে Next.js/Remix।
* Deep nesting: reasoning কঠিন বা mapping slow → flatter structure preferable।
* প্রতিটি page custom rendering logic চাইলে route-based layout cumbersome।

---

# Best Practices (Checklist)

1. Semantic route names ব্যবহার করুন।
2. Layout components minimal রাখুন।
3. Index routes preference দিন empty-path child routes-এর চেয়ে।
4. Nested components এ relative `Link`/`navigate` ব্যবহার করুন।
5. Parent wrapper দিয়ে group protection (auth) দিন।
6. Deep nesting এড়ান (3–4 level ঠিক আছে)।
7. Lazy-loading top-level page/layout boundary তে।
8. 404 fallback routes & `errorElement` v6.4+ এ।
9. Route config declarative & colocated রাখুন।
10. Navigation flows test করুন।
11. Accessibility: keyboard & screen-reader friendly, document titles update per route।

---

# Alternatives & When to Prefer

* Manual conditional rendering: tiny apps।
* Next.js / Remix / Astro: SSR, file-based routing, SEO।
* State-driven modals: URL sharing দরকার না হলে।
* Component-driven routers: non-browser context rare।

---

# Common Pitfalls

* `<Outlet />` ভুলে যাওয়া → children render হবে না।
* Index vs path order ভুল → unexpected render।
* Absolute vs relative `to` misuse।
* `useParams()` parent param read সতর্কতার সাথে।
* Trailing slashes inconsistency।
* Route-based data fetching বেশি নির্ভর করা → central pattern বা data-router ব্যবহার করা ভালো।

---

# Advanced: Route Objects & Data Routers (v6.4+)

v6.4+ data-router API:

* Declarative loaders per route
* Form actions
* Error elements per route

UI + data lifecycle route-এ colocate করা যায়।

---

# Small Complete Demo — Products + Reviews

```jsx
// App.jsx
import { Routes, Route, Link } from "react-router-dom";
import ProductsLayout from "./ProductsLayout";
import ProductsList from "./ProductsList";
import Product from "./Product";
import Review from "./Review";

export default function App() {
  return (
    <>
      <nav><Link to="/">Home</Link> | <Link to="/products">Products</Link></nav>
      <Routes>
        <Route path="/" element={<div>Home</div>} />
        <Route path="products" element={<ProductsLayout />}>
          <Route index element={<ProductsList />} />
          <Route path=":productId" element={<Product />}>
            <Route path="reviews/:reviewId" element={<Review />} />
          </Route>
        </Route>
        <Route path="*" element={<div>404</div>} />
      </Routes>
    </>
  );
}

// ProductsLayout.jsx
import { Outlet } from "react-router-dom";
export default function ProductsLayout() {
  return (
    <div>
      <h1>Products</h1>
      <Outlet />
    </div>
  );
}

// Product.jsx
import { Outlet, useParams, Link } from "react-router-dom";
export default function Product() {
  const { productId } = useParams();
  return (
    <div>
      <h2>Product {productId}</h2>
      <p>Product info here</p>
      <Link to={`reviews/1`}>View Review 1</Link>
      <Outlet />
    </div>
  );
}

// Review.jsx
import { useParams } from "react-router-dom";
export default function Review() {
  const { productId, reviewId } = useParams();
  return <div>Review {reviewId} for product {productId}</div>;
}
```

---

# Testing Nested Routes

* `MemoryRouter` দিয়ে route rendering test করুন (`initialEntries` set করে)।
* Layout render ও child content outlet-এ inject ঠিক আছে কি না assert করুন।
* `userEvent.click` দিয়ে navigation test করুন।

---

# TL;DR — Practical Recommendations

* Shared layout বা hierarchical UI structure থাকলে nested routes ব্যবহার করুন।
* Layout ছোট ও focused রাখুন; children `<Outlet />` render করুন।
* Default child content জন্য index routes ব্যবহার করুন।
* Entity pages জন্য dynamic params + `useParams()` ব্যবহার করুন।
* বড় অ্যাপের জন্য lazy + Suspense ব্যবহার করুন।
* Parent wrapper দিয়ে route-group protection (auth) দিন।
* UI hierarchy superficial হলে deep nesting এড়ান।
* SSR/SEO-heavy apps-এর জন্য Next.js / Remix বিবেচনা করুন।

---

# ⭐ Summary Table

| Feature           | Nested Routes             | Dynamic Routes                      |
| ----------------- | ------------------------- | ----------------------------------- |
| উদ্দেশ্য          | Shared layout UI          | URL variables অনুযায়ী pages render |
| উদাহরণ            | `/dashboard/users`        | `/dashboard/users/:id`              |
| Params পড়া যায়? | ❌                         | ✔ useParams()                       |
| Use Cases         | Dashboard, settings, tabs | Product page, blog, user profile    |
| Core Component    | `<Outlet />`              | `useParams()`                       |

---

# 🎉 **সম্পূর্ণ হয়েছে! এটি Advanced, Production-ready Guide।**

---