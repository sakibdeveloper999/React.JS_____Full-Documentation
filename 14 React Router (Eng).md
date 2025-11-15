
# 🔥 **React Router & Routing System — The Deepest Complete Guide (No Links)**

---

# ⭐ Table of Contents

1. Introduction to Routing
2. SPA vs MPA – Why Client-Side Routing
3. History API & How React Router Works Internally
4. Components of React Router
5. Setting Up BrowserRouter & HashRouter
6. Route Matching Algorithm
7. Route Ranking & Resolution
8. Navigation Types (Declarative vs Imperative)
9. Links — `<Link>`, `<NavLink>`, `<Navigate>`
10. useNavigate Mechanics
11. Nested Routes & Layouts
12. Index Routes
13. Dynamic & Catch-All Routes
14. Data Routers (v6.4+)
15. Loaders Deep Mechanism
16. Actions & Form Submissions
17. Navigation Lifecycle (idle → loading → submitting → done)
18. useNavigation Deep Explanation
19. Error Boundaries
20. Search Params System
21. State Persistence During Navigation
22. Scroll Restoration
23. Lazy-loading & Code Splitting
24. Authentication Guards
25. Protected Routes
26. Role-Based Routing
27. Handling 404 & 500
28. Route-Based Animations
29. Performance Best Practices
30. Example Full Project Structure
31. Advanced Real-Life Examples

এখন প্রতিটি অংশ ধাপে ধাপে ব্যাখ্যা করছি।

---

# 1. Routing — What It Actually Means

Routing = ব্রাউজারের URL অনুসারে ভিন্ন UI দেখানো।

React Router করে:

* URL update করে
* ব্রাউজার reload করতে দেয় না
* কোন কম্পোনেন্ট কোন URL এ দেখাতে হবে সেটা নির্ধারণ করে
* Navigation state manage করে
* Data loading, caching, submitting, errors সব manage করে

---

# 2. SPA vs MPA — কেন প্রয়োজন?

### MPA (Multi Page App)

* প্রতিবার পৃষ্ঠা reload হয়
* Server প্রতিবার HTML পাঠায়
* ধীর

### SPA (Single Page App)

* একবার HTML লোড
* এরপর JS দিয়ে UI বদলায়
* দ্রুত
* কিন্তু URL পরিবর্তনের জন্য client-side routing দরকার → React Router

---

# 3. React Router Internals — History API

React Router ব্রাউজারের **History API** ব্যবহার করে:

* `pushState()` → নতুন পেজে যাওয়া
* `replaceState()` → ব্রাউজার history replace
* `popstate` event → ব্যাক/ফরোয়ার্ড detect করে

React Router কখনোই পেজ reload করে না।
শুধু:

1. URL পরিবর্তন করে
2. Matching route খুঁজে
3. সংশ্লিষ্ট কম্পোনেন্ট রেন্ডার করে

---

# 4. Core Components

### 1) BrowserRouter

HTML5 History API ব্যবহার করে route control করে।

### 2) HashRouter

URL এর পরে `#` ব্যবহার করে routing:

```
/#/about
```

Server reload সমস্যায় HashRouter ব্যবহার হয়।

### 3) Routes

Routes container যা route list ধারণ করে।

### 4) Route

Route object defines:

* path
* element (component)
* children
* loader
* action
* errorElement

---

# 5. Basic Setup (Modern Version)

```jsx
import { RouterProvider, createBrowserRouter } from "react-router-dom";
```

Router configuration is a JS array → React Router itকে internal tree structure বানায়।

---

# 6. Route Matching Algorithm (কিভাবে Route পাওয়া হয়?)

React Router:

1. URL কে segments এ ভাঙে
2. Routes tree স্ক্যান করে
3. Rank করে
4. প্রথম best match route দেয়

### Ranking Rules:

* Static path highest priority → `/about`
* Dynamic path → `/user/:id`
* Splat (wildcard) lowest → `*`

---

# 7. Route Resolution

Nested routes parent+child মিলিয়ে final path তৈরি করে।

Example:

* Parent → `/dashboard`
* Child → `settings`

Final path:

```
/dashboard/settings
```

---

# 8. Navigation Types

### Declarative Navigation

Using components:

```jsx
<Link to="/home" />
<NavLink to="/home" />
<Navigate to="/login" replace />
```

### Imperative Navigation

Using hook:

```jsx
const navigate = useNavigate();
navigate("/profile");
navigate(-1);  // back
```

---

# 9. NavLink — Active Matching Logic

NavLink automatically checks:

* Exact match → active
* Partial match → active (optional)
* Custom class via function
* Custom aria attributes for accessibility

---

# 10. How `useNavigate` Works Internally

1. navigate() → updates browser history
2. triggers route loader
3. triggers re-render
4. updates navigation state
5. renders final UI

This is instantaneous, no reload.

---

# 11. Nested Routes — Deep System

Nested routes allow shared UI:

Layout Component:

```jsx
import { Outlet } from "react-router-dom";
```

Outlet = যেখানে child UI render হবে।

Why necessary?

* Dashboard UI
* Sidebar + Header common থাকবে
* Middle content route অনুযায়ী বদলাবে

---

# 12. Index Routes

Index route = Default child route
Equivalent to `/dashboard` without writing path.

```jsx
{ index: true, element: <DashboardHome /> }
```

---

# 13. Dynamic Routes

```jsx
<Route path="/product/:id" element={<Product />} />
```

### useParams

পাথের ভ্যালু পেতে:

```jsx
const { id } = useParams();
```

---

# 14. Data Routers (v6.4+) — Revolution

আগে data loading UI এর মধ্যে করতে হত:

```jsx
useEffect(() => fetch...);
```

এখন:

* Loader route render হওয়ার আগে run হয়
* UI loading phase দেখাতে পারে
* No waterfall loading
* faster UI
* caching
* error handled automatically

---

# 15. Loader — Data Fetch Lifecycle

Loader receives params:

```js
loader({ request, params })
```

Loader:

1. URL change detect করে
2. Correct loader run করে
3. Data return করে
4. Component useLoaderData দিয়ে ওই data পড়ে
5. Automatically caching করে
6. Errors হলে errorElement ব্যবহার করে

---

# 16. Actions — Form Submission Handling

Action works like a backend form endpoint:

Steps:

1. `<Form method="post">`
2. Action receives request, formData
3. Validate
4. Return success/error
5. UI automatically receives result via useActionData

This is insanely powerful.

---

# 17. Navigation Lifecycle

Navigation has 3 states:

### 1) idle

Nothing is loading/submitting.

### 2) loading

New page loading / loader running.

### 3) submitting

Form submitting / action running.

---

# 18. useNavigation Deep Explanation

```jsx
const nav = useNavigation();
nav.state; // idle, loading, submitting
nav.location; // next location
nav.formData; // submitting form data
```

Use cases:

* Global loading spinner
* Disable form while submitting
* Show progress indicator

---

# 19. Error Boundaries

React Router manages:

* Loader error
* Action error
* Render error

via:

```jsx
errorElement
useRouteError()
```

---

# 20. Search Params

Query string reading/writing:

```jsx
const [params, setParams] = useSearchParams();
params.get("q");
setParams({ q: "router" });
```

---

# 21. State Persistence

Navigation preserves:

* SearchParams
* Scroll restoration
* Form state
* Navigation state

---

# 22. Scroll Restoration

```jsx
import { ScrollRestoration } from "react-router-dom";
```

---

# 23. Lazy Loading

Code Splitting using React.lazy:

```jsx
const About = lazy(() => import("./About"));
```

This reduces bundle size.

---

# 24. Authentication Guards (Best Practice)

### Approach:

1. PrivateRoute component
2. Loader check
3. Redirect if not logged in

Example:

```jsx
loader: () => {
  if (!localStorage.getItem("token")) {
    throw redirect("/login");
  }
}
```

---

# 25. Role-Based Route Protection

```jsx
if (user.role !== "admin") {
  throw redirect("/no-access");
}
```

---

# 26. Catch-All Routes (404)

```jsx
{ path: "*", element: <NotFound /> }
```

---

# 27. Route-Based Animation

Using Framer Motion:

* Animate in/out on route change
* Use `useLocation`

---

# 28. Performance Tips

✔ Use lazy loading
✔ Avoid heavy components inside layout
✔ Use loader-based fetching (no waterfall)
✔ Cache results
✔ Avoid unnecessary `useEffect`

---

# 29. Example Full Folder Structure

```
src/
  routes/
    index.jsx
    dashboard.jsx
  layouts/
    RootLayout.jsx
    DashboardLayout.jsx
  pages/
    Home.jsx
    About.jsx
    Login.jsx
    Products/
      ProductList.jsx
      ProductDetail.jsx
  components/
  utils/
```

---

# 30. Real Life Advanced Examples

### Example: Admin Dashboard

Uses:

* Nested routes
* Layout
* Index routes
* Dynamic segments
* Loader actions
* Protected routes
* Search params

### Example: E-commerce

* `/products/:id`
* cart loader
* checkout action
* payment status loader

### Example: Blog

* Nested blog routes
* search with searchParams
* pagination
* errorElement

---

