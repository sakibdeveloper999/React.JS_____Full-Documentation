
# 🔥 **React Router & Routing System — সম্পূর্ণ বাংলা গাইড**

---

# ⭐ বিষয়সূচি

1. Routing পরিচিতি
2. SPA বনাম MPA – কেন Client-Side Routing দরকার
3. History API & React Router এর ভিতরের কাজ
4. React Router এর মূল কম্পোনেন্টস
5. BrowserRouter & HashRouter সেটআপ
6. Route Matching Algorithm
7. Route Ranking & Resolution
8. Navigation এর ধরন (Declarative vs Imperative)
9. Links — `<Link>`, `<NavLink>`, `<Navigate>`
10. useNavigate এর কাজ
11. Nested Routes & Layouts
12. Index Routes
13. Dynamic & Catch-All Routes
14. Data Routers (v6.4+)
15. Loader এর কাজ
16. Actions & Form Submission
17. Navigation Lifecycle
18. useNavigation এর বিস্তারিত
19. Error Boundaries
20. Search Params ব্যবস্থাপনা
21. Navigation এর সময় state সংরক্ষণ
22. Scroll Restoration
23. Lazy-loading & Code Splitting
24. Authentication Guards
25. Protected Routes
26. Role-Based Routing
27. 404 & 500 Handling
28. Route-Based Animations
29. পারফরম্যান্স টিপস
30. Example Full Project Structure
31. Real-Life Advanced Examples

---

# ১. Routing — আসলেই কি?

Routing মানে হলো **URL অনুযায়ী ভিন্ন UI বা কম্পোনেন্ট দেখানো।**

React Router করে:

* URL আপডেট করে
* ব্রাউজার reload করা বন্ধ করে
* কোন কম্পোনেন্ট কোন URL এ দেখানো হবে তা ঠিক করে
* Navigation state ম্যানেজ করে
* Data লোডিং, caching, submitting, error সব নিয়ন্ত্রণ করে

---

# ২. SPA বনাম MPA — কেন প্রয়োজন?

### MPA (Multi Page App)

* প্রতিটি পৃষ্ঠার জন্য reload হয়
* সার্ভার থেকে নতুন HTML আসে
* ধীর

### SPA (Single Page App)

* একবার HTML লোড হয়
* JS দিয়ে UI বদলায়
* দ্রুত
* তবে URL পরিবর্তনের জন্য **client-side routing** দরকার → React Router

---

# ৩. React Router এর ভিতরের কাজ — History API

React Router **History API** ব্যবহার করে:

* `pushState()` → নতুন রাউটে যাওয়া
* `replaceState()` → বর্তমান রাউট রিপ্লেস
* `popstate` → back/forward ডিটেকশন

React Router **কখনো পেজ reload করে না**, শুধু:

1. URL আপডেট করে
2. Matching route খুঁজে
3. সংশ্লিষ্ট কম্পোনেন্ট রেন্ডার করে

---

# ৪. মূল কম্পোনেন্টস

### ১) BrowserRouter

HTML5 History API ব্যবহার করে route নিয়ন্ত্রণ করে

### ২) HashRouter

URL-এ `#` ব্যবহার করে routing:

```
/#/about
```

যেখানে সার্ভার redirect সেটআপ নেই সেখানে HashRouter ব্যবহার হয়

### ৩) Routes

Route এর container যা সব route ধারণ করে

### ৪) Route

Route object define করে:

* path
* element (component)
* children
* loader
* action
* errorElement

### ৫) Outlet

Nested route এর child component render করে

---

# ৫. Basic Setup (Modern Version)

```jsx
import { RouterProvider, createBrowserRouter } from "react-router-dom";

const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      { index: true, element: <Home /> },
      { path: "about", element: <About /> },
    ]
  }
]);

export default function App() {
  return <RouterProvider router={router} />;
}
```

---

# ৬. Route Matching Algorithm

React Router:

1. URL কে segments এ ভাগ করে
2. Routes tree scan করে
3. Route rank করে
4. প্রথম best match route রেন্ডার করে

### Ranking Rules

* Static path → highest priority (`/about`)
* Dynamic path → medium (`/user/:id`)
* Wildcard/splat → lowest (`*`)

---

# ৭. Route Resolution

Nested routes parent + child মিলিয়ে final path তৈরি করে

উদাহরণ:

* Parent → `/dashboard`
* Child → `settings`

Final path:

```
/dashboard/settings
```

---

# ৮. Navigation Types

### Declarative Navigation (Component দিয়ে)

```jsx
<Link to="/home">Home</Link>
<NavLink to="/home">About</NavLink>
<Navigate to="/login" replace />
```

### Imperative Navigation (Hook দিয়ে)

```jsx
const navigate = useNavigate();
navigate("/profile");    // push
navigate("/login", { replace: true });  // replace
navigate(-1);           // back
```

---

# ৯. NavLink — Active Matching Logic

* Exact match → active
* Partial match → active (optional)
* Custom class function support
* Custom aria attributes for accessibility

---

# ১০. useNavigate এর কাজ

1. navigate() → browser history আপডেট করে
2. Loader trigger করে
3. Re-render trigger করে
4. Navigation state update করে
5. Final UI রেন্ডার করে

কোনো reload হয় না

---

# ১১. Nested Routes — বিস্তারিত

Layout component:

```jsx
import { Outlet } from "react-router-dom";

export default function DashboardLayout() {
  return (
    <div>
      <Sidebar />
      <Outlet />
    </div>
  );
}
```

Outlet = child UI render করার জায়গা

কেন দরকার?

* Common UI (Header, Sidebar)
* Route অনুযায়ী main content update

---

# ১২. Index Routes

* Default child route
* `/dashboard` এ `DashboardHome` রেন্ডার হবে

```jsx
{ index: true, element: <DashboardHome /> }
```

---

# ১৩. Dynamic Routes

```jsx
<Route path="/product/:id" element={<Product />} />
```

### useParams

```jsx
const { id } = useParams();
```

---

# ১৪. Data Routers (v6.4+)

আগে data fetching করতে হত useEffect দিয়ে, এখন:

* Loader route render হওয়ার আগে run হয়
* UI loading phase দেখাতে পারে
* No waterfall loading
* Faster UI
* Automatic caching
* Error auto handled

---

# ১৫. Loader — Data Fetch Lifecycle

```js
loader({ request, params })
```

Loader:

1. URL change detect
2. Correct loader run
3. Data return
4. Component useLoaderData দিয়ে data পড়ে
5. Auto caching
6. Error হলে errorElement রেন্ডার

---

# ১৬. Actions — Form Submission

Steps:

1. `<Form method="post">`
2. Action receives request, formData
3. Validate
4. Return success/error
5. UI automatically useActionData ব্যবহার করে result দেখায়

```jsx
import { Form, useActionData } from "react-router-dom";

export default function Contact() {
  const data = useActionData();

  return (
    <>
      <Form method="post">
        <input name="name" />
        <button>Submit</button>
      </Form>

      {data?.error && <p>{data.error}</p>}
    </>
  );
}
```

---

# ১৭. Navigation Lifecycle

States:

1. idle → কিছু লোড বা submit হচ্ছে না
2. loading → loader চলছে
3. submitting → action চলছে

---

# ১৮. useNavigation

```jsx
const nav = useNavigation();
nav.state;       // idle, loading, submitting
nav.location;    // next location
nav.formData;    // submitting form data
```

Use cases: spinner, progress bar, disable form

---

# ১৯. Error Boundaries

React Router manages loader/action/render errors:

```jsx
errorElement
useRouteError()
```

---

# ২০. Search Params

```jsx
const [params, setParams] = useSearchParams();
params.get("q");
setParams({ q: "react" });
```

---

# ২১. State Persistence

Navigation preserves:

* SearchParams
* Scroll restoration
* Form state
* Navigation state

---

# ২২. Scroll Restoration

```jsx
import { ScrollRestoration } from "react-router-dom";
```

---

# ২৩. Lazy-loading & Code Splitting

```jsx
const About = React.lazy(() => import("./About"));
```

React.Suspense ব্যবহার করে fallback দেখানো যায়

---

# ২৪. Authentication Guards

Loader check:

```jsx
loader: () => {
  if (!localStorage.getItem("token")) {
    throw redirect("/login");
  }
}
```

---

# ২৫. Role-Based Routing

```jsx
if (user.role !== "admin") {
  throw redirect("/no-access");
}
```

---

# ২৬. Catch-All Routes (404)

```jsx
{ path: "*", element: <NotFound /> }
```

---

# ২৭. Route-Based Animation

* Framer Motion ব্যবহার করে route change animation
* useLocation ব্যবহার করে route track

---

# ২৮. Performance Tips

* Lazy load route components
* Avoid heavy components inside layouts
* Loader-based fetching (no waterfall)
* Cache results
* Avoid unnecessary useEffect

---

# ২৯. Full Folder Structure

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

# ৩০. Real Life Advanced Examples

### Admin Dashboard

* Nested routes
* Layout
* Index routes
* Dynamic segments
* Loader & Actions
* Protected routes
* Search params

### E-commerce

* `/products/:id`
* Cart loader
* Checkout action
* Payment status loader

### Blog

* Nested blog routes
* search & pagination
* errorElement

---

# ৩১. Full Modern React Router Example

```jsx
import { RouterProvider, createBrowserRouter } from "react-router-dom";
import RootLayout from "./layouts/RootLayout";
import Home from "./pages/Home";
import About from "./pages/About";
import Post, { postLoader } from "./pages/Post";
import ErrorPage from "./pages/ErrorPage";

const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <Home /> },
      { path: "about", element: <About /> },
      { path: "post/:id", element: <Post />, loader: postLoader }
    ]
  }
]);

export default function App() {
  return <RouterProvider router={router} />;
}
```

---
