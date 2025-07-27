# React.js Interview Questions & Answers

## Core Concepts

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

---

## Performance & Optimization

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

---

## Advanced Patterns

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
