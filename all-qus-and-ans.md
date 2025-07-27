# Senior Full-Stack Team Lead Interview Questions & Answers

## JavaScript & TypeScript

### Advanced JavaScript

**1. Explain the event loop in JavaScript. How does it handle asynchronous operations?**

The JavaScript event loop is the mechanism that handles asynchronous operations in a single-threaded environment. Here's how it works:

**Components:**
- **Call Stack**: Executes synchronous code
- **Web APIs**: Handle async operations (setTimeout, DOM events, HTTP requests)
- **Callback Queue**: Stores completed async callbacks
- **Microtask Queue**: Stores promises and queueMicrotask callbacks (higher priority)

**Process:**
1. Synchronous code executes on the call stack
2. Async operations are sent to Web APIs
3. When complete, callbacks go to the appropriate queue
4. Event loop checks if call stack is empty
5. Microtasks are processed first, then macrotasks

```javascript
console.log('1'); // Synchronous

setTimeout(() => console.log('2'), 0); // Macrotask

Promise.resolve().then(() => console.log('3')); // Microtask

console.log('4'); // Synchronous

// Output: 1, 4, 3, 2
```

**2. What are closures? Provide a practical use case where they are beneficial.**

A closure is a function that has access to variables in its outer (enclosing) scope even after the outer function has returned.

**Practical Use Case - Module Pattern:**
```javascript
const createCounter = () => {
  let count = 0; // Private variable
  
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
};

const counter = createCounter();
console.log(counter.getCount()); // 0
counter.increment();
console.log(counter.getCount()); // 1
// count is not directly accessible from outside
```

**Benefits:**
- Data privacy and encapsulation
- Function factories
- Callback functions with persistent state
- Module patterns

**3. How does prototypal inheritance differ from classical inheritance?**

**Prototypal Inheritance:**
- Objects inherit directly from other objects
- No classes, only objects and prototypes
- Dynamic and flexible
- Uses prototype chain for property lookup

```javascript
const animal = {
  speak() { console.log('Animal sound'); }
};

const dog = Object.create(animal);
dog.bark = function() { console.log('Woof!'); };
```

**Classical Inheritance:**
- Classes inherit from other classes
- Static structure defined at compile time
- Uses class hierarchies
- More rigid but predictable

**Key Differences:**
- Prototypal is more flexible and dynamic
- Classical provides better tooling and IDE support
- Prototypal allows runtime modification of inheritance chain
- Classical is more familiar to developers from OOP languages

**4. Explain `this` keyword behavior in different contexts.**

```javascript
// Global context
console.log(this); // Window (browser) or global (Node.js)

// Function context
function regularFunction() {
  console.log(this); // Window (non-strict) or undefined (strict)
}

// Object method
const obj = {
  name: 'Object',
  method() {
    console.log(this.name); // 'Object'
  }
};

// Arrow function (lexical this)
const arrowObj = {
  name: 'Arrow',
  method: () => {
    console.log(this.name); // undefined (inherits from enclosing scope)
  }
};

// Class context
class MyClass {
  constructor(name) {
    this.name = name;
  }
  
  method() {
    console.log(this.name); // Instance name
  }
}

// Explicit binding
const boundFunction = regularFunction.bind(obj);
regularFunction.call(obj);
regularFunction.apply(obj, []);
```

**5. How would you optimize performance for a heavy computational task in JavaScript?**

**Strategies:**

1. **Web Workers for CPU-intensive tasks:**
```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: largeDataSet });
worker.onmessage = (e) => {
  console.log('Result:', e.data);
};

// worker.js
self.onmessage = (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
};
```

2. **Time slicing with requestIdleCallback:**
```javascript
function processLargeArray(array, callback) {
  const chunk = array.splice(0, 100);
  
  // Process chunk
  chunk.forEach(processItem);
  
  if (array.length > 0) {
    requestIdleCallback(() => processLargeArray(array, callback));
  } else {
    callback();
  }
}
```

3. **Memoization:**
```javascript
const memoize = (fn) => {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
};
```

### TypeScript

**1. What are the key advantages of TypeScript over JavaScript for large-scale applications?**

**Key Advantages:**

1. **Static Type Checking:**
   - Catches errors at compile time
   - Reduces runtime errors
   - Better IDE support with autocomplete

2. **Enhanced Developer Experience:**
   - IntelliSense and code completion
   - Refactoring tools
   - Better navigation

3. **Code Documentation:**
   - Types serve as documentation
   - Self-documenting APIs
   - Easier onboarding for new developers

4. **Scalability:**
   - Better code organization
   - Interface contracts
   - Easier maintenance of large codebases

5. **Modern JavaScript Features:**
   - Latest ECMAScript features
   - Transpilation to older JavaScript versions
   - Decorators and advanced features

**2. Explain generics in TypeScript. Provide an example where they are useful.**

Generics allow you to create reusable components that work with multiple types while maintaining type safety.

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg;
}

// Generic interface
interface Repository<T> {
  findById(id: string): Promise<T>;
  save(entity: T): Promise<T>;
  delete(id: string): Promise<void>;
}

// Generic class implementation
class UserRepository implements Repository<User> {
  async findById(id: string): Promise<User> {
    // Implementation
  }
  
  async save(user: User): Promise<User> {
    // Implementation
  }
  
  async delete(id: string): Promise<void> {
    // Implementation
  }
}

// Generic constraints
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

**3. How do you handle third-party libraries that lack TypeScript definitions?**

**Solutions:**

1. **Install @types packages:**
```bash
npm install @types/library-name
```

2. **Create custom declaration files:**
```typescript
// types/library-name.d.ts
declare module 'library-name' {
  export interface LibraryOptions {
    option1: string;
    option2?: number;
  }
  
  export default function libraryFunction(options: LibraryOptions): void;
}
```

3. **Use module augmentation:**
```typescript
declare module 'existing-library' {
  interface ExistingInterface {
    newProperty: string;
  }
}
```

4. **Quick any type (temporary solution):**
```typescript
declare const libraryName: any;
```

**4. What are utility types? Provide use cases.**

Utility types are built-in TypeScript types that help transform existing types.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
}

// Partial - makes all properties optional
type UserUpdate = Partial<User>;
// { id?: string; name?: string; email?: string; password?: string; }

// Pick - selects specific properties
type UserPublic = Pick<User, 'id' | 'name' | 'email'>;
// { id: string; name: string; email: string; }

// Omit - excludes specific properties
type UserWithoutPassword = Omit<User, 'password'>;
// { id: string; name: string; email: string; }

// Record - creates object type with specific keys and values
type UserRoles = Record<string, 'admin' | 'user' | 'guest'>;
// { [key: string]: 'admin' | 'user' | 'guest'; }

// Required - makes all properties required
type RequiredUser = Required<Partial<User>>;

// Readonly - makes all properties readonly
type ImmutableUser = Readonly<User>;
```

**5. How would you enforce strict type safety in a large codebase?**

**Strategies:**

1. **Strict TypeScript configuration:**
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUncheckedIndexedAccess": true
  }
}
```

2. **ESLint rules:**
```json
{
  "rules": {
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unsafe-assignment": "error",
    "@typescript-eslint/prefer-nullish-coalescing": "error"
  }
}
```

3. **Type-first development:**
```typescript
// Define types first
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// Then implement
async function fetchUser(id: string): Promise<ApiResponse<User>> {
  // Implementation
}
```

4. **Use branded types for domain modeling:**
```typescript
type UserId = string & { readonly brand: unique symbol };
type Email = string & { readonly brand: unique symbol };

function createUserId(id: string): UserId {
  return id as UserId;
}
```

## React.js

### Core Concepts

**1. Explain React's reconciliation algorithm and the virtual DOM.**

**Virtual DOM:**
- Lightweight JavaScript representation of the real DOM
- Kept in memory and synced with real DOM through reconciliation
- Enables efficient updates by comparing virtual DOM trees

**Reconciliation Algorithm:**

1. **Diffing Process:**
   - Compares new virtual DOM tree with previous tree
   - Identifies minimum changes needed
   - Updates only changed elements

2. **Key Strategies:**
   - **Element Type Comparison**: Different types trigger complete rebuild
   - **Key Prop**: Helps identify which items changed, added, or removed
   - **Component State**: Preserved when element type and key match

```jsx
// Without keys - inefficient
{items.map(item => <Item data={item} />)}

// With keys - efficient
{items.map(item => <Item key={item.id} data={item} />)}
```

3. **Fiber Architecture (React 16+):**
   - Incremental rendering
   - Ability to pause and resume work
   - Priority-based updates

**2. How does React handle state management in large applications?**

**State Management Solutions:**

1. **Context API:**
```jsx
const AppContext = createContext();

function AppProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);
  
  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
}

// Usage
const { state, dispatch } = useContext(AppContext);
```

**Pros:** Built-in, no extra dependencies
**Cons:** Can cause unnecessary re-renders, not optimized for frequent updates

2. **Redux:**
```jsx
// Store
const store = configureStore({
  reducer: {
    user: userReducer,
    posts: postsReducer
  }
});

// Component
const user = useSelector(state => state.user);
const dispatch = useDispatch();
```

**Pros:** Predictable state updates, excellent DevTools, middleware support
**Cons:** Boilerplate, learning curve

3. **Zustand:**
```jsx
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 }))
}));

// Usage
const { count, increment } = useStore();
```

**Pros:** Minimal boilerplate, TypeScript-friendly, small bundle size
**Cons:** Less ecosystem support than Redux

**3. What are React hooks? Explain useEffect, useMemo, and useCallback with optimization examples.**

**useEffect:**
```jsx
// Basic usage
useEffect(() => {
  // Side effect
  fetchData();
}, [dependency]); // Dependency array

// Cleanup
useEffect(() => {
  const subscription = subscribe();
  
  return () => {
    subscription.unsubscribe();
  };
}, []);

// Optimization - avoid unnecessary effects
useEffect(() => {
  if (userId) {
    fetchUserData(userId);
  }
}, [userId]); // Only run when userId changes
```

**useMemo:**
```jsx
// Expensive calculation
const expensiveValue = useMemo(() => {
  return heavyCalculation(data);
}, [data]); // Only recalculate when data changes

// Object/array memoization
const filteredItems = useMemo(() => {
  return items.filter(item => item.category === selectedCategory);
}, [items, selectedCategory]);
```

**useCallback:**
```jsx
// Memoize function to prevent child re-renders
const handleClick = useCallback((id) => {
  onItemClick(id);
}, [onItemClick]);

// Optimization example
const MemoizedChild = memo(({ onClick }) => {
  return <button onClick={onClick}>Click me</button>;
});

function Parent() {
  const [count, setCount] = useState(0);
  
  // Without useCallback - child re-renders on every parent render
  const handleClick = () => console.log('clicked');
  
  // With useCallback - child only re-renders when dependencies change
  const memoizedHandleClick = useCallback(() => {
    console.log('clicked');
  }, []);
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      <MemoizedChild onClick={memoizedHandleClick} />
    </div>
  );
}
```

**4. How would you handle SSR (Server-Side Rendering) in React without Next.js?**

**Implementation Steps:**

1. **Server Setup:**
```jsx
// server.js
import express from 'express';
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router-dom';
import App from './App';

const server = express();

server.get('*', (req, res) => {
  const context = {};
  
  const html = renderToString(
    <StaticRouter location={req.url} context={context}>
      <App />
    </StaticRouter>
  );
  
  if (context.url) {
    res.redirect(301, context.url);
    return;
  }
  
  res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>SSR App</title>
      </head>
      <body>
        <div id="root">${html}</div>
        <script src="/bundle.js"></script>
      </body>
    </html>
  `);
});
```

2. **Client Hydration:**
```jsx
// client.js
import React from 'react';
import { hydrateRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

hydrateRoot(
  document.getElementById('root'),
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

3. **Data Fetching:**
```jsx
// Component with data fetching
function UserProfile({ user }) {
  return <div>{user.name}</div>;
}

// Server-side data fetching
UserProfile.getInitialProps = async ({ params }) => {
  const user = await fetchUser(params.id);
  return { user };
};
```

### Performance & Optimization

**1. How do you identify and fix performance bottlenecks in a React application?**

**Identification Tools:**

1. **React DevTools Profiler:**
   - Identifies slow components
   - Shows render times and causes
   - Flame graphs for performance analysis

2. **Browser DevTools:**
   - Performance tab for runtime analysis
   - Memory tab for memory leaks
   - Network tab for bundle analysis

3. **Bundle Analysis:**
```bash
# Webpack Bundle Analyzer
npm install --save-dev webpack-bundle-analyzer
npx webpack-bundle-analyzer build/static/js/*.js
```

**Common Fixes:**

1. **Unnecessary Re-renders:**
```jsx
// Problem
function Parent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  return (
    <div>
      <input value={name} onChange={e => setName(e.target.value)} />
      <ExpensiveChild count={count} />
    </div>
  );
}

// Solution
const MemoizedExpensiveChild = memo(ExpensiveChild);
```

2. **Large Lists:**
```jsx
import { FixedSizeList as List } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );
  
  return (
    <List
      height={600}
      itemCount={items.length}
      itemSize={35}
    >
      {Row}
    </List>
  );
}
```

**2. Explain code-splitting in React. How would you implement it?**

Code-splitting divides your bundle into smaller chunks that can be loaded on demand.

**Implementation Methods:**

1. **Dynamic Imports:**
```jsx
// Route-based splitting
const Home = lazy(() => import('./Home'));
const About = lazy(() => import('./About'));

function App() {
  return (
    <Router>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </Suspense>
    </Router>
  );
}
```

2. **Component-based splitting:**
```jsx
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  const [showHeavy, setShowHeavy] = useState(false);
  
  return (
    <div>
      <button onClick={() => setShowHeavy(true)}>
        Load Heavy Component
      </button>
      {showHeavy && (
        <Suspense fallback={<div>Loading...</div>}>
          <HeavyComponent />
        </Suspense>
      )}
    </div>
  );
}
```

3. **Webpack Magic Comments:**
```jsx
const AdminPanel = lazy(() => 
  import(
    /* webpackChunkName: "admin-panel" */
    /* webpackPreload: true */
    './AdminPanel'
  )
);
```

**3. What are React Server Components? How do they differ from traditional components?**

React Server Components run on the server and send their output to the client.

**Key Differences:**

| Server Components | Client Components |
|-------------------|-------------------|
| Run on server | Run on client |
| No state/effects | Can use state/effects |
| Direct database access | API calls only |
| Zero bundle size | Adds to bundle |
| No interactivity | Full interactivity |

**Example:**
```jsx
// Server Component
async function UserProfile({ userId }) {
  // Direct database access
  const user = await db.user.findUnique({ where: { id: userId } });
  
  return (
    <div>
      <h1>{user.name}</h1>
      <ClientButton userId={userId} />
    </div>
  );
}

// Client Component
'use client';
function ClientButton({ userId }) {
  const [liked, setLiked] = useState(false);
  
  return (
    <button onClick={() => setLiked(!liked)}>
      {liked ? 'Unlike' : 'Like'}
    </button>
  );
}
```

### Advanced Patterns

**1. How would you design a high-order component (HOC) for authentication?**

```jsx
function withAuth(WrappedComponent, requiredRole = null) {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();
    const router = useRouter();
    
    useEffect(() => {
      if (!loading && !user) {
        router.push('/login');
      }
      
      if (user && requiredRole && !user.roles.includes(requiredRole)) {
        router.push('/unauthorized');
      }
    }, [user, loading, router]);
    
    if (loading) {
      return <LoadingSpinner />;
    }
    
    if (!user) {
      return null;
    }
    
    if (requiredRole && !user.roles.includes(requiredRole)) {
      return <UnauthorizedMessage />;
    }
    
    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const AdminPanel = withAuth(AdminComponent, 'admin');
```

**2. Explain the Compound Component pattern in React. Provide an example.**

Compound components work together to form a complete UI, sharing implicit state.

```jsx
// Accordion compound component
const AccordionContext = createContext();

function Accordion({ children, ...props }) {
  const [openItems, setOpenItems] = useState(new Set());
  
  const toggleItem = (id) => {
    setOpenItems(prev => {
      const newSet = new Set(prev);
      if (newSet.has(id)) {
        newSet.delete(id);
      } else {
        newSet.add(id);
      }
      return newSet;
    });
  };
  
  return (
    <AccordionContext.Provider value={{ openItems, toggleItem }}>
      <div className="accordion" {...props}>
        {children}
      </div>
    </AccordionContext.Provider>
  );
}

function AccordionItem({ id, children }) {
  return (
    <div className="accordion-item">
      {children}
    </div>
  );
}

function AccordionHeader({ id, children }) {
  const { openItems, toggleItem } = useContext(AccordionContext);
  const isOpen = openItems.has(id);
  
  return (
    <button
      className="accordion-header"
      onClick={() => toggleItem(id)}
    >
      {children}
      <span>{isOpen ? '−' : '+'}</span>
    </button>
  );
}

function AccordionPanel({ id, children }) {
  const { openItems } = useContext(AccordionContext);
  const isOpen = openItems.has(id);
  
  return isOpen ? (
    <div className="accordion-panel">
      {children}
    </div>
  ) : null;
}

// Attach sub-components
Accordion.Item = AccordionItem;
Accordion.Header = AccordionHeader;
Accordion.Panel = AccordionPanel;

// Usage
function App() {
  return (
    <Accordion>
      <Accordion.Item id="item1">
        <Accordion.Header id="item1">Section 1</Accordion.Header>
        <Accordion.Panel id="item1">Content 1</Accordion.Panel>
      </Accordion.Item>
      <Accordion.Item id="item2">
        <Accordion.Header id="item2">Section 2</Accordion.Header>
        <Accordion.Panel id="item2">Content 2</Accordion.Panel>
      </Accordion.Item>
    </Accordion>
  );
}
```

**3. How do you handle error boundaries in React?**

Error boundaries catch JavaScript errors in component trees and display fallback UI.

```jsx
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    this.setState({
      error,
      errorInfo
    });
    
    // Log error to service
    logErrorToService(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          {process.env.NODE_ENV === 'development' && (
            <details>
              <summary>Error details</summary>
              <pre>{this.state.error && this.state.error.toString()}</pre>
              <pre>{this.state.errorInfo.componentStack}</pre>
            </details>
          )}
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// Hook-based error boundary (using react-error-boundary)
import { ErrorBoundary, useErrorHandler } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <h2>Something went wrong:</h2>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onError={(error, errorInfo) => {
        logErrorToService(error, errorInfo);
      }}
    >
      <MyComponent />
    </ErrorBoundary>
  );
}

// Using error handler in components
function MyComponent() {
  const handleError = useErrorHandler();
  
  const handleAsyncError = async () => {
    try {
      await riskyAsyncOperation();
    } catch (error) {
      handleError(error);
    }
  };
  
  return <button onClick={handleAsyncError}>Do risky thing</button>;
}
```

## Next.js

### Core Features

**1. Explain the difference between SSG, SSR, and ISR in Next.js.**

**Static Site Generation (SSG):**
- Pages generated at build time
- Served as static files
- Best performance
- Good for content that doesn't change often

```jsx
// getStaticProps - runs at build time
export async function getStaticProps() {
  const posts = await fetchPosts();
  
  return {
    props: { posts },
    // Regenerate page at most once per day
    revalidate: 86400
  };
}

// For dynamic routes
export async function getStaticPaths() {
  const posts = await fetchPosts();
  const paths = posts.map(post => ({
    params: { id: post.id }
  }));
  
  return {
    paths,
    fallback: 'blocking' // or true, false
  };
}
```

**Server-Side Rendering (SSR):**
- Pages generated on each request
- Fresh data on every request
- Slower than SSG but always up-to-date

```jsx
export async function getServerSideProps(context) {
  const { req, res, params, query } = context;
  const user = await fetchUser(params.id);
  
  if (!user) {
    return {
      notFound: true
    };
  }
  
  return {
    props: { user }
  };
}
```

**Incremental Static Regeneration (ISR):**
- Combines benefits of SSG and SSR
- Static pages that can be updated after build
- Background regeneration

```jsx
export async function getStaticProps() {
  const data = await fetchData();
  
  return {
    props: { data },
    revalidate: 60 // Revalidate at most once per minute
  };
}
```

**2. How does Next.js handle API routes? What are the best practices?**

API routes provide serverless functions for backend logic.

**Basic API Route:**
```jsx
// pages/api/users/[id].js
export default async function handler(req, res) {
  const { method, query: { id } } = req;
  
  switch (method) {
    case 'GET':
      try {
        const user = await getUserById(id);
        res.status(200).json(user);
      } catch (error) {
        res.status(404).json({ error: 'User not found' });
      }
      break;
      
    case 'PUT':
      try {
        const updatedUser = await updateUser(id, req.body);
        res.status(200).json(updatedUser);
      } catch (error) {
        res.status(400).json({ error: error.message });
      }
      break;
      
    default:
      res.setHeader('Allow', ['GET', 'PUT']);
      res.status(405).end(`Method ${method} Not Allowed`);
  }
}
```

**Best Practices:**

1. **Middleware for common functionality:**
```jsx
// middleware/auth.js
export function withAuth(handler) {
  return async (req, res) => {
    const token = req.headers.authorization?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ error: 'No token provided' });
    }
    
    try {
      const user = await verifyToken(token);
      req.user = user;
      return handler(req, res);
    } catch (error) {
      return res.status(401).json({ error: 'Invalid token' });
    }
  };
}

// Usage
export default withAuth(async function handler(req, res) {
  // Handler logic with authenticated user
});
```

2. **Input validation:**
```jsx
import Joi from 'joi';

const userSchema = Joi.object({
  name: Joi.string().required(),
  email: Joi.string().email().required()
});

export default async function handler(req, res) {
  if (req.method === 'POST') {
    const { error, value } = userSchema.validate(req.body);
    
    if (error) {
      return res.status(400).json({ error: error.details[0].message });
    }
    
    // Process valid data
  }
}
```

3. **Error handling:**
```jsx
// utils/apiHandler.js
export function apiHandler(handler) {
  return async (req, res) => {
    try {
      await handler(req, res);
    } catch (error) {
      console.error('API Error:', error);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  };
}
```

**3. How would you implement dynamic routing in Next.js?**

- Use file-based routing with brackets for dynamic segments:
  ```jsx
  // pages/posts/[id].js
  import { useRouter } from 'next/router';
  export default function Post() {
    const router = useRouter();
    const { id } = router.query;
    return <div>Post ID: {id}</div>;
  }
  ```
- For catch-all routes: `[...slug].js`
- For optional catch-all: `[[...slug]].js`

---

## Deployment & DevOps

### Senior Engineer Deployment Questions & Answers

**1. How do you deploy a Next.js project to AWS (EC2, S3+CloudFront, Amplify, etc.)?**

- **EC2:** Provision an EC2 instance, install Node.js, clone your repo, install dependencies, build the app, and run with PM2 or a process manager. Use NGINX as a reverse proxy. Set up a CI/CD pipeline (e.g., GitHub Actions) to automate deployments.
- **S3 + CloudFront (Static Export):** Use `next export` for static sites, upload the `out` directory to S3, and serve via CloudFront for global CDN. Configure invalidations for cache updates.
- **Amplify:** Connect your repo to AWS Amplify, configure build settings, and Amplify handles build, deploy, and hosting (supports SSR/ISR with Lambda@Edge).
- **Elastic Beanstalk:** Deploy as a Node.js app, Elastic Beanstalk manages scaling and load balancing.
- **Best Practices:** Use environment variables via AWS Parameter Store or Secrets Manager, automate with CI/CD, monitor with CloudWatch.

**2. How do you deploy a Next.js project to DigitalOcean?**

- **App Platform:** Connect your repo to DigitalOcean App Platform, set build and run commands, and configure environment variables. App Platform supports SSR and static sites.
- **Droplet:** Provision a Droplet (VM), install Node.js, clone repo, install dependencies, build, and run with PM2. Use NGINX as a reverse proxy.
- **Container Registry + Kubernetes:** Build a Docker image, push to DigitalOcean Container Registry, and deploy with DigitalOcean Kubernetes (DOKS) for scalable production.
- **Best Practices:** Use managed databases, enable automatic deploys, and monitor with DigitalOcean Monitoring.

**3. What are best practices for CI/CD with Next.js?**

- Use GitHub Actions, GitLab CI, or CircleCI to automate build, test, and deploy.
- Run linting, type checks, and tests in the pipeline.
- Use environment-specific variables and secrets.
- Deploy to staging before production.
- Use blue/green or canary deployments for zero-downtime.
- Roll back automatically on failure.

**4. How do you handle environment variables and secrets in production?**

- Never commit secrets to version control.
- Use platform-specific secret managers (AWS SSM, Secrets Manager, Vercel/Netlify/DigitalOcean env vars).
- For Docker, use `docker secrets` or pass env vars at runtime.
- Use `.env.production` for build-time variables, and runtime env vars for serverless/SSR.

**5. How do you ensure zero-downtime deployments?**

- Use blue/green or canary deployment strategies.
- Deploy new version alongside old, switch traffic after health checks.
- Use load balancers to manage traffic.
- Automate rollbacks on failure.
- Use process managers (PM2) for graceful restarts.

**6. How do you monitor and roll back deployments?**

- Integrate monitoring (Datadog, New Relic, Sentry, CloudWatch) for errors and performance.
- Use health checks in CI/CD.
- Automate rollbacks on failed health checks or error spikes.
- Keep previous build artifacts for quick rollback.

**7. What are the differences between static and serverless deployments for Next.js?**

- **Static:** Uses `next export` or SSG, deploys static files to CDN (S3, Netlify, Vercel). Fast, cheap, but no SSR.
- **Serverless:** Deploys SSR/ISR pages as serverless functions (AWS Lambda, Vercel, Netlify). Supports dynamic content, scales automatically, but cold starts and execution limits apply.
- **Hybrid:** Next.js supports hybrid apps with both static and serverless pages.

**8. How do you optimize Next.js for production performance and cost?**

- Use SSG/ISR for as many pages as possible.
- Optimize images with `next/image`.
- Use CDN for static assets.
- Minimize bundle size (analyze with `next build` and bundle analyzer).
- Use serverless for dynamic routes, but monitor cold starts and costs.
- Cache API responses and use edge caching.
- Monitor and tune serverless function memory/timeouts.

---

### Additional Basic & Advanced Deployment Questions

**1. What is the difference between a rolling deployment and a blue/green deployment?**

- **Rolling:** Gradually replaces old instances with new ones, some users may hit old/new during deploy.
- **Blue/Green:** Deploys new version (green) alongside old (blue), switch all traffic at once after validation, enables instant rollback.

**2. How do you use Docker to containerize a Next.js app for deployment?**

- Write a `Dockerfile` that installs dependencies, builds the app, and runs it.
- Use multi-stage builds to keep images small.
- Example:
  ```dockerfile
  FROM node:18-alpine AS builder
  WORKDIR /app
  COPY . .
  RUN yarn install --frozen-lockfile
  RUN yarn build

  FROM node:18-alpine
  WORKDIR /app
  COPY --from=builder /app ./
  EXPOSE 3000
  CMD ["yarn", "start"]
  ```
- Push to a registry and deploy to any container platform.

**3. How do you handle SSL/HTTPS in production deployments?**

- Use managed certificates (AWS ACM, Let's Encrypt).
- Terminate SSL at the load balancer or CDN (CloudFront, NGINX).
- Redirect HTTP to HTTPS.
- Renew certificates automatically.

**4. How do you automate infrastructure provisioning for deployments?**

- Use Infrastructure as Code (IaC) tools: Terraform, AWS CloudFormation, Pulumi.
- Version control your infrastructure.
- Automate with CI/CD pipelines.

**5. How do you scale a Next.js app to handle high traffic?**

- Use CDN for static assets and SSG pages.
- Use auto-scaling groups or serverless for SSR.
- Optimize database connections (pooling, managed DBs).
- Cache API responses.
- Monitor and tune for bottlenecks.

---

### Performance & SEO

**1. How do you optimize a Next.js application for SEO?**

- Use SSR/SSG for content pages.
- Set meta tags, titles, canonical URLs, and Open Graph tags with `<Head>`.
- Generate sitemaps and robots.txt.
- Use semantic HTML and ARIA attributes.
- Optimize page speed (image optimization, code splitting, minimal JS).

**2. Explain how Next.js handles image optimization. What are the benefits of using the `next/image` component?**

- The `next/image` component optimizes images on-demand: lazy loading, resizing, WebP/AVIF support, responsive images, CDN integration.
- Benefits: smaller payloads, better LCP, improved SEO and UX.

**Example:**
```jsx
import Image from 'next/image';
<Image src="/images/photo.jpg" alt="Description" width={600} height={400} quality={80} priority />
```

**3. How would you implement internationalization (i18n) in Next.js?**

- Use built-in i18n routing in `next.config.js`:
  ```js
  module.exports = {
    i18n: {
      locales: ['en', 'fr', 'es'],
      defaultLocale: 'en',
    },
  };
  ```
- Use libraries like `next-i18next` or `react-intl` for translations.
- Structure content and routes to support locale subpaths or domains.

---

### Advanced Topics

**1. How does Next.js handle middleware? Provide a use case for it.**

- Middleware runs before a request is completed, allowing you to modify the request/response.
- In Next.js 12+, middleware is placed in `middleware.ts` at the root or in specific folders.
- Use cases: authentication, redirects, logging, A/B testing.

**Example:**
```ts
// middleware.ts
import { NextResponse } from 'next/server';
export function middleware(request) {
  const { pathname } = request.nextUrl;
  if (pathname.startsWith('/admin') && !request.cookies.get('token')) {
    return NextResponse.redirect('/login');
  }
  return NextResponse.next();
}
```

**2. What are the benefits of using Next.js over a custom React setup?**

- Built-in SSR/SSG/ISR for SEO and performance.
- File-based routing and API routes.
- Image optimization, static asset handling.
- Middleware, internationalization, and analytics.
- Zero-config production builds and Vercel integration.

**3. How would you migrate a legacy React app to Next.js?**

- Audit the current app for SSR/SSG needs.
- Move components/pages into the `pages` or `app` directory.
- Refactor routing to use Next.js file-based routing.
- Move API logic to `/api` routes if needed.
- Update data fetching to use `getStaticProps`, `getServerSideProps`, or React Server Components.
- Test SSR/SSG output and optimize for SEO.

---

## Full-Stack & Architecture

### Backend Integration

**1. How would you design a RESTful API for a Next.js application? What are the best practices?**

- Use `/api` routes for backend logic or a separate backend service.
- Follow REST conventions: resource-based URLs, proper HTTP verbs, status codes.
- Validate input, handle errors, and secure endpoints (auth, rate limiting).
- Use middleware for shared logic (auth, logging).
- Document the API (OpenAPI/Swagger).

**2. Compare REST vs. GraphQL for a Next.js application. When would you use each?**

- **REST:** Simple, cacheable, well-understood, good for standard CRUD.
- **GraphQL:** Flexible queries, reduces over/under-fetching, single endpoint, introspection.
- Use REST for simple APIs, legacy systems, or when caching is critical.
- Use GraphQL for complex data needs, multiple clients, or when you want to minimize requests.

**3. How do you handle authentication in a Next.js app (JWT, OAuth, sessions)?**

- Use libraries like `next-auth` for OAuth, JWT, and session management.
- Store JWTs in HTTP-only cookies for security.
- For SSR, validate tokens in `getServerSideProps` or middleware.
- For API routes, check tokens in headers or cookies.

---

### Database & State Management

**1. How would you structure a full-stack application using Next.js with a database like PostgreSQL or MongoDB?**

- Use `/api` routes or a separate backend for database access.
- Use an ORM (Prisma, TypeORM, Mongoose) for data modeling.
- Keep database credentials secure (env vars).
- Use server components or SSR for data fetching when needed.
- Example structure:
  - `/pages` or `/app`: UI
  - `/api`: API endpoints (CRUD)
  - `/prisma` or `/models`: ORM schemas

**2. Explain caching strategies for a Next.js app (client-side, server-side, CDN).**

- **Client-side:** SWR/React Query for data caching, browser cache.
- **Server-side:** In-memory cache (Redis), HTTP cache headers.
- **CDN:** Cache static assets, images, and SSG pages at the edge.
- Use `revalidate` in ISR for background regeneration.

**3. How do you handle real-time updates in Next.js (WebSockets, Server-Sent Events)?**

- Use WebSockets (Socket.IO, ws) for bidirectional real-time communication.
- Use Server-Sent Events (SSE) for one-way server-to-client updates.
- Integrate with `/api` routes or a separate Node.js server.
- Use client libraries to subscribe to updates and update UI state.

---

### Microservices & Monoliths

**1. When would you choose a microservices architecture over a monolith for a Next.js app?**

- Choose microservices for large teams, independent deployments, scalability, or when different parts of the system have different requirements.
- Use a monolith for small teams, simple apps, or when rapid iteration is needed.
- Consider micro-frontends if splitting the UI is required.

**2. How do you ensure consistency between frontend and backend types in a full-stack TypeScript app?**

- Share type definitions in a common package (e.g., `@myorg/types`).
- Use code generation tools (GraphQL Codegen, tRPC).
- Use OpenAPI/Swagger with codegen for REST APIs.
- Enforce type checks in CI/CD.

---

## Leadership & Team Skills

### Technical Leadership

**1. How do you mentor junior developers in a team setting?**

- Pair programming, code reviews, and regular feedback.
- Encourage questions and foster a safe learning environment.
- Assign progressively challenging tasks.
- Share resources and best practices.
- Lead by example in code quality and communication.

**2. Describe your approach to code reviews. What do you focus on?**

- Focus on correctness, readability, maintainability, and security.
- Check for adherence to coding standards and architectural guidelines.
- Provide constructive, actionable feedback.
- Encourage discussion and knowledge sharing.
- Balance nitpicks with bigger-picture issues.

**3. How do you handle technical debt in a fast-paced environment?**

- Track debt in the backlog and prioritize based on impact.
- Allocate regular time for refactoring and cleanup.
- Communicate trade-offs with stakeholders.
- Prevent new debt by enforcing standards and reviews.

---

### Project Management

**1. How do you prioritize tasks when leading a full-stack team?**

- Align priorities with business goals and deadlines.
- Use frameworks like MoSCoW or RICE for prioritization.
- Balance feature work, bug fixes, and technical debt.
- Involve the team in estimation and planning.

**2. Explain your strategy for breaking down a large feature into manageable tasks.**

- Start with high-level requirements and user stories.
- Break down into epics, then smaller tasks.
- Define clear acceptance criteria for each task.
- Identify dependencies and parallelizable work.
- Review progress regularly and adjust as needed.

**3. How do you ensure alignment between frontend and backend teams?**

- Hold regular cross-team standups and planning meetings.
- Use shared documentation and API contracts.
- Encourage early collaboration on API design.
- Use tools like Swagger, Postman, or GraphQL playgrounds for testing.

---

### Conflict Resolution

**1. How would you handle a disagreement between team members over technical decisions?**

- Facilitate open discussion and ensure all voices are heard.
- Focus on data, pros/cons, and alignment with goals.
- Encourage experimentation or proof-of-concept if feasible.
- Escalate to architecture review or leadership if unresolved.

**2. What’s your approach to balancing business deadlines with code quality?**

- Communicate risks and trade-offs transparently.
- Negotiate scope or timelines when possible.
- Prioritize critical quality (security, stability) over perfection.
- Plan for post-release improvements if needed.

---

## System Design & Problem-Solving

### Design a Scalable Next.js App

**1. How would you architect a Next.js app with millions of users?**

- Use SSR/ISR for dynamic and static content.
- Deploy on scalable infrastructure (Vercel, AWS, GCP).
- Use CDN for static assets and edge caching.
- Implement horizontal scaling for API routes.
- Use managed databases (e.g., AWS RDS, MongoDB Atlas).
- Monitor performance and errors (Datadog, Sentry).

**2. How do you handle rate limiting, caching, and load balancing in a Next.js API?**

- Use middleware or API gateways (NGINX, API Gateway) for rate limiting.
- Cache responses at the CDN or server level.
- Use Redis or similar for server-side caching.
- Deploy behind a load balancer (AWS ELB, GCP Load Balancer).

---

### Debugging & Troubleshooting

**1. A Next.js page is loading slowly. How would you diagnose and fix it?**

- Profile server and client performance (DevTools, Lighthouse).
- Check for large bundle sizes and optimize code splitting.
- Optimize images and static assets.
- Analyze API response times and database queries.
- Use caching and CDN for static content.

**2. A React component is re-rendering unnecessarily. How would you debug and optimize it?**

- Use React DevTools to inspect re-renders.
- Check props and state changes.
- Use `React.memo`, `useMemo`, and `useCallback` to prevent unnecessary renders.
- Refactor large components into smaller, focused ones.

---

### Case Study Questions

**1. Walk us through how you would build a real-time dashboard with Next.js and WebSockets.**

- Set up a WebSocket server (Socket.IO, ws) for real-time data.
- Create a Next.js page/component that connects to the WebSocket.
- Use state management (Context, Redux, Zustand) to store live data.
- Render dashboard widgets that update on new events.
- Handle reconnection and error states gracefully.

**2. How would you implement a feature like "dark mode" in a Next.js app with SSR support?**

- Store theme preference in a cookie or localStorage.
- On SSR, read the cookie and set the initial theme.
- Use a context/provider to manage theme state.
- Apply CSS variables or Tailwind dark mode classes.
- Provide a toggle button for users.

---

## Final Questions for the Candidate

**1. What’s your approach to staying updated with the latest in JavaScript/React/Next.js?**

- Follow official blogs, changelogs, and release notes.
- Participate in developer communities (Twitter, Reddit, Dev.to).
- Attend conferences, webinars, and local meetups.
- Experiment with new features in side projects.
- Read books and take online courses.

**2. Describe a challenging technical problem you solved as a team lead.**

- Example: Led a migration from a monolithic app to microservices, coordinating multiple teams, ensuring zero downtime, and improving deployment speed by 50%. Overcame challenges in data consistency, service discovery, and team communication.

**3. How do you foster collaboration between frontend and backend developers?**

- Encourage shared planning and retrospectives.
- Use API contracts and shared documentation.
- Organize cross-functional pairing and code reviews.
- Celebrate joint successes and learn from failures together.

---
