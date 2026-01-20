# React - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts](#core-concepts)
2. [Performance Optimization](#performance-optimization)
3. [State Management](#state-management)
4. [Architecture & Design Patterns](#architecture--design-patterns)
5. [Advanced Hooks](#advanced-hooks)
6. [Testing & Quality](#testing--quality)

---

## Core Concepts

### Q1: What is React Reconciliation and how does the Fiber architecture improve it?

**Answer:**
Think of reconciliation as React's way of figuring out what changed in your UI and updating only those parts.

**Simple Explanation:**
- When your app's data changes, React doesn't rebuild everything from scratch
- It compares the new virtual DOM with the old one (like spotting differences between two photos)
- Only the actual differences are updated in the real DOM

**Fiber Architecture Benefits:**
- **Incremental Rendering**: Work can be split into chunks and paused if needed (like taking breaks while cleaning a house)
- **Priority-based Updates**: Urgent updates (like typing) happen before less urgent ones (like animations)
- **Better Error Handling**: Errors can be caught and handled gracefully

**Example:**
```javascript
// Without Fiber: Everything blocks until done
// With Fiber: High priority updates happen first
function SearchBox() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  
  // User typing = HIGH priority (Fiber handles this first)
  const handleChange = (e) => setQuery(e.target.value);
  
  // Search results = LOWER priority (can wait a bit)
  useEffect(() => {
    const results = expensiveSearch(query);
    setResults(results);
  }, [query]);
}
```

---

### Q2: Explain the difference between Controlled and Uncontrolled components. When would you use each?

**Answer:**
**Controlled Components:**
- React controls the form data through state
- Like a puppet master controlling every movement

```javascript
function ControlledForm() {
  const [name, setName] = useState('');
  
  return (
    <input 
      value={name} // React controls this
      onChange={(e) => setName(e.target.value)} 
    />
  );
}
```

**Uncontrolled Components:**
- DOM handles the form data, React just reads it when needed
- Like letting someone work independently and checking their work later

```javascript
function UncontrolledForm() {
  const inputRef = useRef();
  
  const handleSubmit = () => {
    console.log(inputRef.current.value); // Read when needed
  };
  
  return <input ref={inputRef} />;
}
```

**When to use:**
- **Controlled**: When you need validation, dynamic updates, or full control
- **Uncontrolled**: For simple forms, file uploads, or integrating with non-React code

---

## Performance Optimization

### Q3: What are the different ways to optimize React performance at scale?

**Answer:**

**1. Code Splitting (Lazy Loading):**
Load only what users need, when they need it.

```javascript
// Instead of importing everything at once
import HeavyComponent from './HeavyComponent';

// Load it only when needed
const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

**2. Memoization:**
Remember expensive calculations or components to avoid repeating work.

```javascript
// Memoize expensive calculations
const expensiveValue = useMemo(() => {
  return calculateSomethingExpensive(data);
}, [data]); // Only recalculate when data changes

// Memoize callbacks
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]); // Function stays the same unless id changes

// Memoize entire components
const MemoizedComponent = memo(MyComponent);
```

**3. Virtualization:**
For large lists, only render what's visible on screen.

```javascript
import { FixedSizeList } from 'react-window';

// Instead of rendering 10,000 items
// Only render what fits on screen
<FixedSizeList
  height={600}
  itemCount={10000}
  itemSize={50}
>
  {({ index, style }) => <Row index={index} style={style} />}
</FixedSizeList>
```

**4. Debouncing/Throttling:**
Limit how often expensive operations run.

```javascript
const debouncedSearch = useMemo(
  () => debounce((query) => searchAPI(query), 300),
  []
);
```

---

### Q4: How does React 18's Concurrent Rendering improve user experience?

**Answer:**
Concurrent rendering is like a smart chef who can pause making one dish to quickly handle an urgent order.

**Key Features:**

**1. Automatic Batching:**
```javascript
// React 18 batches ALL updates automatically
setTimeout(() => {
  setCount(c => c + 1);  // These all update together
  setFlag(f => !f);      // in one render
  setValue(v => v * 2);  // Not three separate renders!
}, 1000);
```

**2. Transitions (startTransition):**
Mark non-urgent updates so React can prioritize user input.

```javascript
function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  
  const handleChange = (e) => {
    // User typing = URGENT (instant feedback)
    setQuery(e.target.value);
    
    // Search results = NON-URGENT (can wait)
    startTransition(() => {
      setResults(searchHugeDatabase(e.target.value));
    });
  };
}
```

**3. Suspense for Data Fetching:**
```javascript
<Suspense fallback={<Spinner />}>
  <ProfilePage /> {/* Can suspend while loading data */}
</Suspense>
```

---

## State Management

### Q5: Compare different state management solutions (Context, Redux, Zustand, Jotai). When should you use each?

**Answer:**

**1. Context API:**
Best for: Simple, infrequent updates (theme, user auth)

```javascript
// Good: Theme doesn't change often
const ThemeContext = createContext();

// Bad: Frequent updates cause re-renders
// Every keystroke re-renders all consumers!
const SearchContext = createContext();
```

**2. Redux:**
Best for: Complex apps with predictable state updates, time-travel debugging

```javascript
// Pros: Single source of truth, DevTools, middleware
// Cons: More boilerplate

const userSlice = createSlice({
  name: 'user',
  initialState: { data: null },
  reducers: {
    setUser: (state, action) => {
      state.data = action.payload; // Immer makes this work
    }
  }
});
```

**3. Zustand (Lightweight):**
Best for: Medium complexity, less boilerplate than Redux

```javascript
// Simple, direct, performant
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 }))
}));
```

**4. Jotai (Atomic):**
Best for: Fine-grained updates, bottom-up state composition

```javascript
// Atoms are independent units
const countAtom = atom(0);
const doubleAtom = atom((get) => get(countAtom) * 2);

// Only components using this atom re-render
const count = useAtom(countAtom);
```

**Decision Matrix:**
- **Small app, simple state**: useState + Context
- **Medium app**: Zustand or Context + useReducer
- **Large app with complex logic**: Redux Toolkit
- **Need fine-grained control**: Jotai or Recoil

---

### Q6: What are the best practices for managing server state vs client state?

**Answer:**

**Server State** = Data from APIs (user info, products)
**Client State** = UI state (modal open, form inputs)

**Don't Mix Them!**

**Bad Approach:**
```javascript
// Mixing server and client state is messy
const [data, setData] = useState(null);
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);

// You have to handle caching, revalidation, etc. manually
```

**Good Approach - Use React Query/SWR:**
```javascript
// React Query handles server state
function UserProfile() {
  const { data, isLoading, error } = useQuery(
    ['user', userId], 
    fetchUser,
    {
      staleTime: 5 * 60 * 1000, // Cache for 5 minutes
      refetchOnWindowFocus: true // Refresh on tab focus
    }
  );
  
  // Separate client state
  const [isEditing, setIsEditing] = useState(false);
}
```

**Benefits:**
- Automatic caching and deduplication
- Background updates
- Optimistic updates
- Pagination and infinite scroll support

---

## Architecture & Design Patterns

### Q7: How would you architect a large-scale React application?

**Answer:**

**Folder Structure:**
```
src/
├── features/           # Feature-based organization
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── types/
│   └── products/
│       ├── components/
│       ├── hooks/
│       └── ...
├── shared/            # Shared across features
│   ├── components/   # Buttons, Inputs, etc.
│   ├── hooks/
│   ├── utils/
│   └── types/
├── layouts/          # Page layouts
└── routes/           # Route configuration
```

**Key Principles:**

**1. Feature-Based Organization:**
Keep related code together. If you delete a feature, delete one folder.

**2. Separation of Concerns:**
```javascript
// ❌ Bad: Everything in one component
function ProductList() {
  const [products, setProducts] = useState([]);
  useEffect(() => { /* fetch logic */ }, []);
  // rendering logic
  // business logic
  // all mixed together
}

// ✅ Good: Separated concerns
function ProductList() {
  const { products, isLoading } = useProducts(); // Custom hook
  return <ProductGrid products={products} />; // Presentational component
}
```

**3. Component Types:**
- **Presentational**: Just UI, receive props
- **Container**: Handle logic, pass data down
- **Custom Hooks**: Reusable logic

**4. API Layer Abstraction:**
```javascript
// api/client.ts - Single place for API config
export const apiClient = axios.create({
  baseURL: process.env.API_URL,
  timeout: 10000
});

// features/products/api.ts - Feature-specific API calls
export const productAPI = {
  getAll: () => apiClient.get('/products'),
  getById: (id) => apiClient.get(`/products/${id}`)
};
```

---

### Q8: Explain Compound Components pattern and when to use it.

**Answer:**
Compound components work together like a team, sharing implicit state.

**Example - Without Compound Pattern:**
```javascript
// ❌ Complex props, hard to customize
<Dropdown 
  items={items}
  renderItem={(item) => <div>{item.name}</div>}
  renderTrigger={() => <button>Open</button>}
  // Gets messy quickly
/>
```

**With Compound Pattern:**
```javascript
// ✅ Flexible, composable, clear structure
<Dropdown>
  <Dropdown.Trigger>
    <button>Open Menu</button>
  </Dropdown.Trigger>
  
  <Dropdown.Menu>
    <Dropdown.Item>Profile</Dropdown.Item>
    <Dropdown.Item>Settings</Dropdown.Item>
    <Dropdown.Item>Logout</Dropdown.Item>
  </Dropdown.Menu>
</Dropdown>
```

**Implementation:**
```javascript
const DropdownContext = createContext();

function Dropdown({ children }) {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <DropdownContext.Provider value={{ isOpen, setIsOpen }}>
      <div className="dropdown">{children}</div>
    </DropdownContext.Provider>
  );
}

Dropdown.Trigger = function Trigger({ children }) {
  const { setIsOpen } = useContext(DropdownContext);
  return <div onClick={() => setIsOpen(prev => !prev)}>{children}</div>;
};

Dropdown.Menu = function Menu({ children }) {
  const { isOpen } = useContext(DropdownContext);
  return isOpen ? <div>{children}</div> : null;
};
```

**When to Use:**
- Building flexible component libraries
- When components need to work together
- When you want to give users full control over structure

---

## Advanced Hooks

### Q9: How do you create custom hooks that are reusable and testable?

**Answer:**

**Best Practices:**

**1. Single Responsibility:**
```javascript
// ✅ Good: One clear purpose
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  
  return debouncedValue;
}

// ❌ Bad: Too many responsibilities
function useEverything() {
  // Don't do this - too complex to test and reuse
}
```

**2. Return Object for Multiple Values:**
```javascript
// ✅ Good: Named returns, easy to destructure selectively
function useAuth() {
  return {
    user,
    login,
    logout,
    isAuthenticated,
    isLoading
  };
}

const { user, login } = useAuth(); // Use only what you need
```

**3. Make Them Testable:**
```javascript
// hooks/useLocalStorage.js
export function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// hooks/useLocalStorage.test.js
import { renderHook, act } from '@testing-library/react-hooks';

test('should store value in localStorage', () => {
  const { result } = renderHook(() => useLocalStorage('key', 'initial'));
  
  act(() => {
    result.current[1]('new value');
  });
  
  expect(result.current[0]).toBe('new value');
});
```

---

### Q10: Explain useReducer vs useState. When should each be used?

**Answer:**

**useState: Simple state updates**
```javascript
// Good for: Independent, simple values
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**useReducer: Complex state logic**
```javascript
// Good for: Related values, complex updates
function ShoppingCart() {
  const [state, dispatch] = useReducer(cartReducer, { items: [], total: 0 });
  
  const addItem = (item) => dispatch({ type: 'ADD_ITEM', payload: item });
  const removeItem = (id) => dispatch({ type: 'REMOVE_ITEM', payload: id });
  
  return (/* UI */);
}

function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM':
      return {
        items: [...state.items, action.payload],
        total: state.total + action.payload.price
      };
    case 'REMOVE_ITEM':
      // Complex logic in one place
      const item = state.items.find(i => i.id === action.payload);
      return {
        items: state.items.filter(i => i.id !== action.payload),
        total: state.total - item.price
      };
    default:
      return state;
  }
}
```

**Use useState when:**
- State is simple (boolean, string, number)
- Updates are independent
- No complex dependencies

**Use useReducer when:**
- Multiple related state values
- Complex update logic
- Next state depends on previous state
- Testing is important (reducers are pure functions)

---

## Testing & Quality

### Q11: What is your testing strategy for React applications?

**Answer:**

**Testing Pyramid:**

**1. Unit Tests (70%)** - Test individual functions/components
```javascript
// Test pure functions and hooks
test('formatCurrency formats correctly', () => {
  expect(formatCurrency(1000)).toBe('$1,000.00');
});
```

**2. Integration Tests (20%)** - Test component interactions
```javascript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('form submission', async () => {
  render(<LoginForm />);
  
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password');
  await userEvent.click(screen.getByRole('button', { name: 'Login' }));
  
  await waitFor(() => {
    expect(screen.getByText('Welcome!')).toBeInTheDocument();
  });
});
```

**3. E2E Tests (10%)** - Test critical user flows
```javascript
// Cypress/Playwright
test('user can complete checkout', async () => {
  await page.goto('/products');
  await page.click('[data-testid="add-to-cart"]');
  await page.click('[data-testid="checkout"]');
  // ... complete flow
  await expect(page.locator('.success-message')).toBeVisible();
});
```

**Best Practices:**
- Test behavior, not implementation
- Use Testing Library's queries (getByRole, getByLabelText)
- Mock external dependencies (APIs)
- Test accessibility (screen reader support)

---

### Q12: How do you ensure code quality in a large React team?

**Answer:**

**1. TypeScript:**
Catch errors before runtime.
```typescript
// Props are documented and type-safe
interface ButtonProps {
  variant: 'primary' | 'secondary';
  onClick: () => void;
  children: React.ReactNode;
}

function Button({ variant, onClick, children }: ButtonProps) {
  return <button className={variant} onClick={onClick}>{children}</button>;
}
```

**2. ESLint + Prettier:**
Enforce consistent code style.
```json
{
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended"
  ],
  "rules": {
    "react-hooks/exhaustive-deps": "error"
  }
}
```

**3. Pre-commit Hooks (Husky + lint-staged):**
```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

**4. Code Review Checklist:**
- Are components small and focused?
- Is state lifted properly?
- Are effects necessary and properly cleaned up?
- Is error handling in place?
- Are there tests?

**5. Component Documentation:**
```javascript
/**
 * UserCard displays user information with avatar
 * 
 * @param {Object} user - User object with name, email, avatar
 * @param {Function} onEdit - Callback when edit is clicked
 * @example
 * <UserCard user={userData} onEdit={handleEdit} />
 */
export function UserCard({ user, onEdit }) {
  // ...
}
```

**6. Performance Monitoring:**
- Use React DevTools Profiler
- Monitor bundle size (webpack-bundle-analyzer)
- Set up error tracking (Sentry)
- Track Core Web Vitals

---

## Summary

**Key Takeaways for Senior/Architect Level:**

1. **Performance**: Lazy loading, memoization, virtualization
2. **State Management**: Choose the right tool for the job
3. **Architecture**: Feature-based, separation of concerns
4. **Patterns**: Compound components, custom hooks, render props
5. **Quality**: Testing pyramid, TypeScript, code reviews
6. **Scalability**: Code splitting, micro-frontends, proper folder structure

**Remember:** The best code is not the cleverest code, it's the code that your team can understand and maintain!

---

## Fundamentals

### Q13: What is JSX and why do we use it?

**Answer:**
JSX (JavaScript XML) is a syntax extension for JavaScript that looks similar to HTML. It makes writing React components more intuitive.

```javascript
// JSX
const element = <h1>Hello, {name}</h1>;

// Compiles to:
const element = React.createElement('h1', null, 'Hello, ', name);
```

**Why JSX?**
- More readable and expressive
- Allows embedding JavaScript expressions
- Provides compile-time errors
- Prevents injection attacks by escaping values

### Q14: What is the difference between Element and Component?

**Answer:**
**Element**: Plain object describing what you want to see on screen (immutable)
```javascript
const element = <div>Hello</div>; // React element
```

**Component**: Function or class that returns elements (reusable)
```javascript
function Welcome() {
  return <div>Hello</div>; // Component
}
```

Elements are what components return. Components are the factories that produce elements.

### Q15: What are Pure Components?

**Answer:**
Pure Components automatically implement `shouldComponentUpdate` with a shallow prop and state comparison.

```javascript
// Class component
class PureUserCard extends React.PureComponent {
  render() {
    return <div>{this.props.name}</div>;
  }
}

// Functional equivalent
const UserCard = React.memo(function UserCard({ name }) {
  return <div>{name}</div>;
});
```

**When to use:**
- Component renders often with same props
- Props/state are primitives or shallow objects
- Avoid if props are complex nested objects

### Q16: Why should we not update state directly?

**Answer:**
Directly mutating state bypasses React's update mechanism.

```javascript
// ❌ Wrong - Direct mutation
this.state.count = 5;
this.state.items.push(newItem);

// ✅ Correct - Use setState
this.setState({ count: 5 });
this.setState({ items: [...this.state.items, newItem] });
```

**Reasons:**
- React won't trigger re-render
- State batching won't work
- Can be overwritten by async updates
- Breaks React DevTools time-travel

### Q17: What is the purpose of callback function in setState()?

**Answer:**
The callback executes after state is actually updated and component re-rendered.

```javascript
this.setState({ count: this.state.count + 1 }, () => {
  console.log('State updated:', this.state.count);
  // Safe to perform actions that depend on new state
});

// With functional updates
this.setState(
  prevState => ({ count: prevState.count + 1 }),
  () => console.log('Updated')
);
```

**Use cases:**
- Trigger side effects after state update
- Make API calls with new state
- Access DOM after re-render

### Q18: What are synthetic events in React?

**Answer:**
Synthetic events are React's cross-browser wrapper around native browser events.

```javascript
function handleClick(e) {
  e.preventDefault(); // Synthetic event method
  console.log(e.nativeEvent); // Access native event if needed
}

<button onClick={handleClick}>Click</button>
```

**Benefits:**
- Consistent API across browsers
- Better performance through event delegation
- Auto-cleanup (event pooling in React <17)

### Q19: What is the "key" prop and why is it important?

**Answer:**
Keys help React identify which items have changed, been added, or removed.

```javascript
// ❌ Bad - Using index as key
{items.map((item, index) => (
  <Item key={index} data={item} />
))}

// ✅ Good - Using stable unique ID
{items.map(item => (
  <Item key={item.id} data={item} />
))}
```

**Why it matters:**
- Helps React optimize re-renders
- Preserves component state correctly
- Prevents bugs with reordering lists

**Never use index as key when:**
- List items can be reordered
- Items can be added/removed
- List is filtered

### Q20: What is the use of refs?

**Answer:**
Refs provide direct access to DOM elements or component instances.

```javascript
function TextInput() {
  const inputRef = useRef(null);
  
  const focusInput = () => {
    inputRef.current.focus(); // Direct DOM access
  };
  
  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

**Use cases:**
- Managing focus, text selection, media playback
- Triggering animations
- Integrating with third-party DOM libraries
- Storing mutable values that don't cause re-renders

### Q21: What are forward refs?

**Answer:**
Ref forwarding passes a ref through a component to one of its children.

```javascript
const FancyInput = React.forwardRef((props, ref) => (
  <input ref={ref} className="fancy-input" {...props} />
));

function Parent() {
  const inputRef = useRef();
  
  return <FancyInput ref={inputRef} />;
}
```

**Common in:**
- Reusable component libraries
- HOC components
- When parent needs direct DOM access

### Q22: What is React Fiber?

**Answer:**
Fiber is React's reconciliation engine rewrite (React 16+) that enables:

**Key capabilities:**
- **Incremental rendering**: Split work into chunks
- **Pause, abort, or reuse work**: Based on priority
- **Concurrent features**: Multiple priority levels

```javascript
// Fiber allows React to:
// 1. Pause expensive renders
const hugeList = items.map(item => <ExpensiveComponent item={item} />);

// 2. Handle urgent updates first
startTransition(() => {
  setSearchResults(massiveDataset); // Lower priority
});
```

### Q23: What is the main goal of React Fiber?

**Answer:**
The main goal is to enable **incremental rendering** - the ability to split rendering work into chunks and spread it across multiple frames.

**Benefits:**
- Smooth animations during heavy renders
- Responsive UI during expensive operations
- Better perceived performance
- Enables features like Suspense and Concurrent Mode

### Q24: What is the difference between createElement and cloneElement?

**Answer:**
**createElement**: Creates a new React element
```javascript
const element = React.createElement(
  'div',
  { className: 'container' },
  'Hello'
);
```

**cloneElement**: Clones an existing element and can override props
```javascript
function Parent({ children }) {
  // Add props to existing child element
  return React.cloneElement(children, { 
    className: 'enhanced',
    onClick: handleClick 
  });
}
```

**Use cloneElement for:**
- Modifying children in compound components
- Adding props to unknown children
- Enhancing component wrappers

### Q25: What is Lifting State Up?

**Answer:**
Moving state to the closest common ancestor when multiple components need to share it.

```javascript
// Before: State in sibling components (can't share)
function ComponentA() {
  const [value, setValue] = useState('');
  return <input value={value} onChange={e => setValue(e.target.value)} />;
}

function ComponentB() {
  // Can't access ComponentA's value
}

// After: Lift state up to parent
function Parent() {
  const [value, setValue] = useState('');
  
  return (
    <>
      <ComponentA value={value} onChange={setValue} />
      <ComponentB value={value} />
    </>
  );
}
```

### Q26: What are the lifecycle methods of React (Class Components)?

**Answer:**
**Mounting:**
- `constructor()` - Initialize state
- `static getDerivedStateFromProps()` - Sync state with props
- `render()` - Return JSX
- `componentDidMount()` - Side effects, API calls

**Updating:**
- `static getDerivedStateFromProps()`
- `shouldComponentUpdate()` - Performance optimization
- `render()`
- `getSnapshotBeforeUpdate()` - Capture DOM info
- `componentDidUpdate()` - Post-update side effects

**Unmounting:**
- `componentWillUnmount()` - Cleanup

**Error Handling:**
- `static getDerivedStateFromError()` - Render fallback
- `componentDidCatch()` - Log errors

```javascript
class MyComponent extends React.Component {
  componentDidMount() {
    // Fetch data, add listeners
  }
  
  componentWillUnmount() {
    // Cleanup listeners, cancel requests
  }
  
  render() {
    return <div>{this.props.data}</div>;
  }
}
```

### Q27: What are Higher-Order Components (HOC)?

**Answer:**
A HOC is a function that takes a component and returns a new enhanced component.

```javascript
// HOC that adds loading state
function withLoading(Component) {
  return function WithLoadingComponent({ isLoading, ...props }) {
    if (isLoading) return <Spinner />;
    return <Component {...props} />;
  };
}

// Usage
const UserListWithLoading = withLoading(UserList);

// Use it
<UserListWithLoading isLoading={loading} users={users} />
```

**Common HOC patterns:**
- Authentication: `withAuth(Component)`
- Data fetching: `withData(Component)`
- Error boundaries: `withErrorBoundary(Component)`

**Modern alternative:** Custom hooks (more flexible)

### Q28: What is Context and when to use it?

**Answer:**
Context provides a way to pass data through the component tree without prop drilling.

```javascript
// Create context
const ThemeContext = createContext('light');

// Provider
function App() {
  const [theme, setTheme] = useState('dark');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Header />
      <Main />
    </ThemeContext.Provider>
  );
}

// Consumer (any level deep)
function ThemedButton() {
  const { theme, setTheme } = useContext(ThemeContext);
  return <button className={theme}>Button</button>;
}
```

**Use Context for:**
- Theme, language, authentication
- Data needed by many components
- Avoid passing props through many levels

**Don't use for:**
- Frequently changing values (causes re-renders)
- State management (use Redux, Zustand instead)

### Q29: What is the children prop?

**Answer:**
`children` is a special prop that contains the content between component opening and closing tags.

```javascript
function Card({ children }) {
  return (
    <div className="card">
      {children} {/* Render whatever is passed */}
    </div>
  );
}

// Usage
<Card>
  <h1>Title</h1>
  <p>Content</p>
</Card>
```

**Advanced patterns:**
```javascript
// Function as children (render prop)
<DataProvider>
  {data => <div>{data.name}</div>}
</DataProvider>

// Multiple children
function Tabs({ children }) {
  return React.Children.map(children, (child, index) => {
    return React.cloneElement(child, { index });
  });
}
```

### Q30: What is reconciliation?

**Answer:**
Reconciliation is React's algorithm for diffing the virtual DOM to determine minimal DOM updates.

**How it works:**
1. Compare new virtual DOM with previous
2. Identify differences
3. Update only what changed in real DOM

**Optimization rules:**
- Elements of different types produce different trees
- Keys help identify stable elements across renders
- Siblings are processed in order

```javascript
// Efficient reconciliation
<ul>
  {items.map(item => (
    <li key={item.id}>{item.name}</li> // Key helps React track items
  ))}
</ul>
```

### Q31: What is the difference between Shadow DOM and Virtual DOM?

**Answer:**
**Virtual DOM (React):**
- JavaScript representation of real DOM
- Lightweight copy for diffing
- React-specific optimization technique

**Shadow DOM (Web Standard):**
- Browser feature for encapsulation
- Isolates component styles and markup
- Used in Web Components

```javascript
// Virtual DOM - React concept
const vdom = { type: 'div', props: { children: 'Hello' } };

// Shadow DOM - Browser API
const shadow = element.attachShadow({ mode: 'open' });
shadow.innerHTML = '<style>p { color: red; }</style><p>Isolated</p>';
```

They're unrelated technologies that solve different problems.

---

## React Hooks Deep Dive

### Q32: What are the rules of Hooks?

**Answer:**
**Rule 1: Only call Hooks at the top level**
Don't call Hooks inside loops, conditions, or nested functions.

```javascript
// ❌ Wrong
function Component() {
  if (condition) {
    useState(0); // Conditional hook call
  }
}

// ✅ Correct
function Component() {
  const [count, setCount] = useState(0);
  if (condition) {
    // Use the state here
  }
}
```

**Rule 2: Only call Hooks from React functions**
- Call from functional components
- Call from custom Hooks
- Don't call from regular JavaScript functions

**Why?** React relies on the order of Hook calls to maintain state correctly.

### Q33: What is the difference between useEffect and useLayoutEffect?

**Answer:**
**useEffect:**
- Runs **after** paint (asynchronous)
- Non-blocking
- Use for most side effects

```javascript
useEffect(() => {
  fetchData(); // After render and paint
}, []);
```

**useLayoutEffect:**
- Runs **before** paint (synchronous)
- Blocks visual updates
- Use when you need to read/mutate DOM before paint

```javascript
useLayoutEffect(() => {
  const height = ref.current.offsetHeight; // Measure before paint
  setHeight(height);
}, []);
```

**Use useLayoutEffect for:**
- Measuring DOM elements
- Preventing visual flickering
- Synchronizing animations

**Visual timeline:**
1. React renders
2. useLayoutEffect runs (blocks paint)
3. Browser paints
4. useEffect runs

### Q34: How to implement shouldComponentUpdate with Hooks?

**Answer:**
Use `React.memo()` for component-level optimization and comparison logic.

```javascript
// Basic memoization
const MyComponent = React.memo(function MyComponent({ name }) {
  return <div>{name}</div>;
});

// Custom comparison
const MyComponent = React.memo(
  function MyComponent({ user }) {
    return <div>{user.name}</div>;
  },
  (prevProps, nextProps) => {
    // Return true if props are equal (skip render)
    return prevProps.user.id === nextProps.user.id;
  }
);
```

**Inside the component:**
```javascript
// Use useMemo for expensive calculations
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(props.data);
}, [props.data]);

// Use useCallback for stable function references
const memoizedCallback = useCallback(() => {
  doSomething(props.id);
}, [props.id]);
```

### Q35: How to force re-render with Hooks?

**Answer:**
Use a dummy state update to trigger re-render.

```javascript
function Component() {
  const [, forceUpdate] = useReducer(x => x + 1, 0);
  
  return (
    <button onClick={forceUpdate}>
      Force Re-render
    </button>
  );
}

// Or with useState
function Component() {
  const [, forceUpdate] = useState({});
  
  const handleClick = () => {
    forceUpdate({}); // New object reference triggers render
  };
  
  return <button onClick={handleClick}>Force</button>;
}
```

**Note:** Usually there's a better pattern. Force re-rendering is often a code smell.

### Q36: What is useCallback and when to use it?

**Answer:**
`useCallback` memoizes function references to prevent unnecessary re-creations.

```javascript
// Without useCallback - new function every render
function Parent() {
  const handleClick = () => console.log('clicked');
  return <ExpensiveChild onClick={handleClick} />; // Re-renders every time
}

// With useCallback - stable function reference
function Parent() {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []); // Function only created once
  
  return <ExpensiveChild onClick={handleClick} />; // No unnecessary re-renders
}
```

**Use when:**
- Passing callbacks to optimized child components
- Callbacks are dependencies in useEffect
- Performance profiling shows issues

**Don't use everywhere** - it has overhead too!

### Q37: What is useMemo and when to use it?

**Answer:**
`useMemo` memoizes computed values to avoid expensive recalculations.

```javascript
function Component({ items }) {
  // Recalculates every render
  const sorted = items.sort((a, b) => b.value - a.value);
  
  // Only recalculates when items change
  const sorted = useMemo(() => {
    return items.sort((a, b) => b.value - a.value);
  }, [items]);
  
  return <List items={sorted} />;
}
```

**Use for:**
- Expensive calculations
- Referential equality (objects/arrays passed as props)
- Preventing child re-renders

```javascript
// Prevent re-render due to new object
const config = useMemo(() => ({
  color: 'red',
  size: 'large'
}), []); // Object created once

<ChildComponent config={config} />
```

### Q38: What is the difference between useCallback and useMemo?

**Answer:**
**useCallback:** Returns a memoized **function**
```javascript
const memoizedFn = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

**useMemo:** Returns a memoized **value**
```javascript
const memoizedValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);
```

**Relationship:**
```javascript
useCallback(fn, deps) === useMemo(() => fn, deps)
```

### Q39: What is useRef and its use cases?

**Answer:**
`useRef` creates a mutable object that persists across renders without causing re-renders.

```javascript
// 1. DOM references
function TextInput() {
  const inputRef = useRef(null);
  const focus = () => inputRef.current.focus();
  return <input ref={inputRef} />;
}

// 2. Store mutable values
function Timer() {
  const intervalRef = useRef(null);
  
  useEffect(() => {
    intervalRef.current = setInterval(() => {
      console.log('tick');
    }, 1000);
    
    return () => clearInterval(intervalRef.current);
  }, []);
}

// 3. Store previous values
function Counter() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();
  
  useEffect(() => {
    prevCountRef.current = count;
  });
  
  const prevCount = prevCountRef.current;
  return <div>Now: {count}, Before: {prevCount}</div>;
}
```

### Q40: What is useImperativeHandle?

**Answer:**
Customizes the ref value exposed to parent components.

```javascript
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    // Only expose these methods
    focus: () => inputRef.current.focus(),
    clear: () => inputRef.current.value = ''
  }));
  
  return <input ref={inputRef} />;
});

// Parent can only access exposed methods
function Parent() {
  const ref = useRef();
  
  return (
    <>
      <FancyInput ref={ref} />
      <button onClick={() => ref.current.focus()}>Focus</button>
      <button onClick={() => ref.current.clear()}>Clear</button>
    </>
  );
}
```

### Q41: How to fetch data with Hooks?

**Answer:**
```javascript
function useDataFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    
    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url);
        const json = await response.json();
        
        if (!cancelled) {
          setData(json);
          setError(null);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }
    
    fetchData();
    
    return () => {
      cancelled = true; // Cleanup flag
    };
  }, [url]);
  
  return { data, loading, error };
}

// Usage
function Users() {
  const { data, loading, error } = useDataFetch('/api/users');
  
  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;
  return <UserList users={data} />;
}
```

### Q42: What is useDebugValue?

**Answer:**
Displays a label for custom hooks in React DevTools.

```javascript
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  
  // Shows in DevTools: "OnlineStatus: Online" or "OnlineStatus: Offline"
  useDebugValue(isOnline ? 'Online' : 'Offline');
  
  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);
    
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  
  return isOnline;
}

// Expensive debug value formatting
function useCustomHook(value) {
  useDebugValue(value, value => {
    // Only formats when DevTools is open
    return expensiveFormat(value);
  });
}
```

### Q43: What is the useTransition Hook?

**Answer:**
Marks state updates as non-urgent, keeping UI responsive during expensive renders.

```javascript
import { useTransition, useState } from 'react';

function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  const handleChange = (e) => {
    const value = e.target.value;
    
    // Urgent: Update input immediately
    setQuery(value);
    
    // Non-urgent: Can be interrupted
    startTransition(() => {
      const searchResults = expensiveSearch(value);
      setResults(searchResults);
    });
  };
  
  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <Results data={results} />
    </>
  );
}
```

**When to use:**
- Heavy rendering operations
- Large list filtering
- Complex state updates

### Q44: What is the useDeferredValue Hook?

**Answer:**
Defers updating a value to keep UI responsive.

```javascript
function SearchResults({ query }) {
  // Deferred value lags behind query during fast typing
  const deferredQuery = useDeferredValue(query);
  
  // Expensive operation uses deferred value
  const results = useMemo(() => {
    return expensiveSearch(deferredQuery);
  }, [deferredQuery]);
  
  return (
    <div style={{ opacity: query !== deferredQuery ? 0.5 : 1 }}>
      {results.map(result => <Result key={result.id} {...result} />)}
    </div>
  );
}
```

**Difference from useTransition:**
- `useTransition`: You control when to start transition
- `useDeferredValue`: React defers the value automatically

### Q45: What is the useId Hook?

**Answer:**
Generates unique IDs for accessibility attributes, stable across server and client.

```javascript
function TextField({ label }) {
  const id = useId(); // Generates unique ID
  
  return (
    <>
      <label htmlFor={id}>{label}</label>
      <input id={id} />
    </>
  );
}

// Multiple fields, no ID conflicts
function Form() {
  return (
    <>
      <TextField label="Name" />    {/* id: :r1: */}
      <TextField label="Email" />   {/* id: :r2: */}
      <TextField label="Phone" />   {/* id: :r3: */}
    </>
  );
}
```

**Critical for SSR:** IDs match between server and client.

### Q46: How to handle cleanup in useEffect?

**Answer:**
Return a cleanup function from useEffect.

```javascript
useEffect(() => {
  // Setup
  const subscription = api.subscribe();
  const timer = setTimeout(() => {}, 1000);
  const listener = () => {};
  window.addEventListener('resize', listener);
  
  // Cleanup function
  return () => {
    subscription.unsubscribe();
    clearTimeout(timer);
    window.removeEventListener('resize', listener);
  };
}, []);
```

**When cleanup runs:**
- Before effect runs again (if dependencies changed)
- When component unmounts

**Common cleanups:**
- Cancel subscriptions
- Clear timers/intervals
- Remove event listeners
- Abort fetch requests

### Q47: What happens if you omit the dependency array in useEffect?

**Answer:**
Effect runs after **every render**.

```javascript
useEffect(() => {
  console.log('Runs after every render');
}); // No dependency array

// Equivalent to:
componentDidUpdate() {
  console.log('After every update');
}
```

**Usually wrong!** Can cause infinite loops:
```javascript
useEffect(() => {
  setState(value); // Triggers re-render → runs effect → infinite loop!
});
```

### Q48: What happens with an empty dependency array?

**Answer:**
Effect runs **only once** after initial render.

```javascript
useEffect(() => {
  fetchData();
  console.log('Runs once on mount');
  
  return () => {
    console.log('Cleanup on unmount');
  };
}, []); // Empty array

// Equivalent to:
componentDidMount() {
  fetchData();
}

componentWillUnmount() {
  // cleanup
}
```

### Q49: Can useEffect be async?

**Answer:**
No, but you can call async functions inside it.

```javascript
// ❌ Wrong - useEffect cannot be async
useEffect(async () => {
  const data = await fetchData();
}, []);

// ✅ Correct - Define async function inside
useEffect(() => {
  async function loadData() {
    const data = await fetchData();
    setData(data);
  }
  
  loadData();
}, []);

// ✅ Or use IIFE
useEffect(() => {
  (async () => {
    const data = await fetchData();
    setData(data);
  })();
}, []);

// ✅ Best - with cleanup
useEffect(() => {
  let cancelled = false;
  
  async function loadData() {
    const data = await fetchData();
    if (!cancelled) {
      setData(data);
    }
  }
  
  loadData();
  
  return () => {
    cancelled = true;
  };
}, []);
```

---

## Advanced React Patterns

### Q50: What is Redux and when should you use it?

**Answer:**
Redux is a predictable state container with a single source of truth.

**Core concepts:**
```javascript
// 1. Store - Single state tree
const store = createStore(reducer);

// 2. Actions - What happened
const increment = { type: 'INCREMENT' };
const addTodo = { type: 'ADD_TODO', payload: 'Learn Redux' };

// 3. Reducers - How state changes
function counterReducer(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    default:
      return state;
  }
}

// 4. Dispatch - Send actions
store.dispatch(increment);
```

**Use Redux when:**
- Large app with complex state
- State needed by many components
- Frequent state updates
- Need for time-travel debugging
- Team prefers predictable patterns

**Don't use Redux for:**
- Small apps
- Simple state management
- Form state (use local state)
- Server cache (use React Query)

### Q51: What is React Router and how to implement it?

**Answer:**
React Router enables client-side routing in single-page applications.

```javascript
import { BrowserRouter, Routes, Route, Link, useParams, Navigate } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="/admin" element={
          <ProtectedRoute>
            <Admin />
          </ProtectedRoute>
        } />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

// Route with params
function UserDetail() {
  const { id } = useParams();
  return <div>User {id}</div>;
}

// Protected route
function ProtectedRoute({ children }) {
  const isAuthenticated = useAuth();
  return isAuthenticated ? children : <Navigate to="/login" />;
}
```

### Q52: What is the difference between NavLink and Link?

**Answer:**
**Link:** Basic navigation
```javascript
<Link to="/about">About</Link>
```

**NavLink:** Link with active state styling
```javascript
<NavLink 
  to="/about"
  className={({ isActive }) => isActive ? 'active' : ''}
  style={({ isActive }) => ({
    color: isActive ? 'red' : 'black'
  })}
>
  About
</NavLink>

// Automatic active class
<NavLink to="/about" className="nav-link">
  About {/* Gets 'active' class when on /about */}
</NavLink>
```

**Use NavLink for:** Navigation menus, breadcrumbs

### Q53: What is lazy loading and code splitting?

**Answer:**
Split your app into smaller chunks that load on demand.

```javascript
// Without lazy loading - everything loads upfront
import AdminPanel from './AdminPanel';
import UserDashboard from './UserDashboard';

// With lazy loading - loads when needed
const AdminPanel = lazy(() => import('./AdminPanel'));
const UserDashboard = lazy(() => import('./UserDashboard'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/admin" element={<AdminPanel />} />
        <Route path="/dashboard" element={<UserDashboard />} />
      </Routes>
    </Suspense>
  );
}

// Named exports
const UserProfile = lazy(() => 
  import('./UserProfile').then(module => ({ default: module.UserProfile }))
);
```

**Benefits:**
- Faster initial load
- Smaller bundle size
- Better performance
- Pay-for-what-you-use

### Q54: What is Suspense?

**Answer:**
Suspense lets components "wait" for something before rendering.

```javascript
// Basic usage
<Suspense fallback={<Spinner />}>
  <LazyComponent />
</Suspense>

// Nested Suspense boundaries
<Suspense fallback={<PageSkeleton />}>
  <Header />
  <Suspense fallback={<ContentSkeleton />}>
    <Content />
    <Suspense fallback={<CommentsSkeleton />}>
      <Comments />
    </Suspense>
  </Suspense>
</Suspense>

// Data fetching (React 18+)
<Suspense fallback={<Loading />}>
  <UserProfile userId={id} /> {/* Suspends while fetching */}
</Suspense>
```

**Use cases:**
- Code splitting with React.lazy()
- Data fetching (with frameworks like Relay)
- Image/asset loading

### Q55: What are Error Boundaries?

**Answer:**
Error boundaries catch JavaScript errors in component tree and display fallback UI.

```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error:', error);
    console.error('Error Info:', errorInfo);
    // Log to error service (Sentry, etc.)
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h1>Something went wrong</h1>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <App />
</ErrorBoundary>

// Multiple boundaries
<ErrorBoundary fallback={<PageError />}>
  <Layout>
    <ErrorBoundary fallback={<WidgetError />}>
      <Widget />
    </ErrorBoundary>
  </Layout>
</ErrorBoundary>
```

**Note:** Error boundaries don't catch:
- Event handlers (use try-catch)
- Async code
- Server-side rendering errors
- Errors in error boundary itself

### Q56: What are Portals?

**Answer:**
Portals render children into a DOM node outside the parent hierarchy.

```javascript
import { createPortal } from 'react-dom';

function Modal({ children, onClose }) {
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>,
    document.getElementById('modal-root') // Render here
  );
}

// HTML
<div id="root"></div>
<div id="modal-root"></div> <!-- Modal renders here -->

// Usage
function App() {
  const [showModal, setShowModal] = useState(false);
  
  return (
    <div style={{ overflow: 'hidden' }}>
      <button onClick={() => setShowModal(true)}>Open Modal</button>
      {showModal && (
        <Modal onClose={() => setShowModal(false)}>
          <h1>Modal Content</h1>
        </Modal>
      )}
    </div>
  );
}
```

**Use cases:**
- Modals
- Tooltips
- Dropdowns
- Notifications

### Q57: What are Fragments and why use them?

**Answer:**
Fragments group children without adding extra DOM nodes.

```javascript
// Without Fragment - extra div wrapper
function List() {
  return (
    <div> {/* Unnecessary wrapper */}
      <li>Item 1</li>
      <li>Item 2</li>
    </div>
  );
}

// With Fragment - no wrapper
function List() {
  return (
    <>
      <li>Item 1</li>
      <li>Item 2</li>
    </>
  );
}

// Fragment with key (for lists)
function Glossary({ items }) {
  return items.map(item => (
    <Fragment key={item.id}>
      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </Fragment>
  ));
}
```

**Benefits:**
- Cleaner DOM
- Better CSS layout (flexbox/grid)
- Faster rendering
- Less memory usage

### Q58: What is StrictMode?

**Answer:**
StrictMode highlights potential problems in development.

```javascript
<StrictMode>
  <App />
</StrictMode>
```

**What it does:**
- Identifies unsafe lifecycles
- Warns about legacy string ref API
- Warns about deprecated findDOMNode
- Detects unexpected side effects (double-invokes effects)
- Warns about legacy context API

**Development only** - no impact on production.

### Q59: What is prop drilling and how to avoid it?

**Answer:**
Prop drilling is passing props through intermediate components that don't need them.

```javascript
// ❌ Prop drilling
function App() {
  const [user, setUser] = useState(null);
  return <Layout user={user} setUser={setUser} />;
}

function Layout({ user, setUser }) {
  return <Header user={user} setUser={setUser} />; // Just passing through
}

function Header({ user, setUser }) {
  return <UserMenu user={user} setUser={setUser} />; // Just passing through
}

function UserMenu({ user, setUser }) {
  return <div>{user.name}</div>; // Finally used here!
}

// ✅ Solution 1: Context
const UserContext = createContext();

function App() {
  const [user, setUser] = useState(null);
  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Layout />
    </UserContext.Provider>
  );
}

function UserMenu() {
  const { user } = useContext(UserContext); // Direct access
  return <div>{user.name}</div>;
}

// ✅ Solution 2: State management (Redux, Zustand)
function UserMenu() {
  const user = useSelector(state => state.user);
  return <div>{user.name}</div>;
}

// ✅ Solution 3: Composition
function App() {
  const [user, setUser] = useState(null);
  return (
    <Layout>
      <UserMenu user={user} setUser={setUser} />
    </Layout>
  );
}
```

### Q60: What is React.memo()?

**Answer:**
`React.memo()` is a higher-order component that memoizes functional components.

```javascript
// Component re-renders when parent renders
function ExpensiveComponent({ data }) {
  return <div>{/* Complex rendering */}</div>;
}

// Memoized - only re-renders if props change
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  return <div>{/* Complex rendering */}</div>;
});

// Custom comparison
const UserCard = React.memo(
  ({ user }) => <div>{user.name}</div>,
  (prevProps, nextProps) => {
    // Return true if props are equal (skip render)
    return prevProps.user.id === nextProps.user.id;
  }
);
```

**When to use:**
- Pure functional components
- Expensive rendering
- Component receives same props often

**Don't use:**
- Component always renders with different props
- Premature optimization

---

## Testing React Applications

### Q61: What are the main testing libraries for React?

**Answer:**
**1. Jest**: Test runner and assertion library
**2. React Testing Library**: Component testing focused on user behavior
**3. Enzyme** (legacy): Component testing with shallow/mount rendering
**4. Cypress/Playwright**: End-to-end testing

```javascript
// Jest + React Testing Library
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('login form submits correctly', async () => {
  const handleSubmit = jest.fn();
  render(<LoginForm onSubmit={handleSubmit} />);
  
  // Find elements by role (accessibility-focused)
  const emailInput = screen.getByRole('textbox', { name: /email/i });
  const passwordInput = screen.getByLabelText(/password/i);
  const submitButton = screen.getByRole('button', { name: /login/i });
  
  // Simulate user interactions
  await userEvent.type(emailInput, 'user@example.com');
  await userEvent.type(passwordInput, 'password123');
  await userEvent.click(submitButton);
  
  // Assert
  await waitFor(() => {
    expect(handleSubmit).toHaveBeenCalledWith({
      email: 'user@example.com',
      password: 'password123'
    });
  });
});
```

### Q62: How to test async operations?

**Answer:**
Use `waitFor`, `findBy` queries, or `act` for async updates.

```javascript
// Test data fetching
test('loads and displays users', async () => {
  // Mock API
  global.fetch = jest.fn(() =>
    Promise.resolve({
      json: () => Promise.resolve([
        { id: 1, name: 'John' },
        { id: 2, name: 'Jane' }
      ])
    })
  );
  
  render(<UserList />);
  
  // Loading state
  expect(screen.getByText(/loading/i)).toBeInTheDocument();
  
  // Wait for data to load
  const users = await screen.findAllByRole('listitem');
  expect(users).toHaveLength(2);
  expect(screen.getByText('John')).toBeInTheDocument();
});

// Test with waitFor
test('shows error message on failure', async () => {
  global.fetch = jest.fn(() => Promise.reject('API Error'));
  
  render(<UserList />);
  
  await waitFor(() => {
    expect(screen.getByText(/error/i)).toBeInTheDocument();
  });
});
```

### Q63: How to test custom Hooks?

**Answer:**
Use `@testing-library/react-hooks` (or `renderHook` from React Testing Library).

```javascript
import { renderHook, act } from '@testing-library/react';

// Custom hook
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  return { count, increment, decrement };
}

// Test
test('useCounter increments and decrements', () => {
  const { result } = renderHook(() => useCounter(0));
  
  expect(result.current.count).toBe(0);
  
  act(() => {
    result.current.increment();
  });
  
  expect(result.current.count).toBe(1);
  
  act(() => {
    result.current.decrement();
  });
  
  expect(result.current.count).toBe(0);
});

// Test with props
test('useCounter with initial value', () => {
  const { result, rerender } = renderHook(
    ({ initial }) => useCounter(initial),
    { initialProps: { initial: 10 } }
  );
  
  expect(result.current.count).toBe(10);
  
  rerender({ initial: 20 });
  expect(result.current.count).toBe(10); // Doesn't change on rerender
});
```

### Q64: What are snapshot tests?

**Answer:**
Snapshot tests capture component output and detect unexpected changes.

```javascript
import renderer from 'react-test-renderer';

test('Button matches snapshot', () => {
  const tree = renderer
    .create(<Button variant="primary">Click me</Button>)
    .toJSON();
  
  expect(tree).toMatchSnapshot();
});

// First run: Creates __snapshots__/Button.test.js.snap
// Future runs: Compares against snapshot
// If changed: Test fails (review and update if intended)
```

**Inline snapshots:**
```javascript
test('renders correctly', () => {
  const { container } = render(<Button>Click</Button>);
  expect(container.firstChild).toMatchInlineSnapshot(`
    <button class="btn">
      Click
    </button>
  `);
});
```

**Pros:** Quick, catches unintended changes
**Cons:** Can be brittle, over-used

---

## TypeScript with React

### Q65: How to type React components?

**Answer:**
```typescript
// Functional component with explicit type
interface ButtonProps {
  variant: 'primary' | 'secondary';
  onClick: () => void;
  children: React.ReactNode;
  disabled?: boolean;
}

function Button({ variant, onClick, children, disabled = false }: ButtonProps) {
  return (
    <button className={variant} onClick={onClick} disabled={disabled}>
      {children}
    </button>
  );
}

// With React.FC (includes children automatically)
const Button: React.FC<ButtonProps> = ({ variant, onClick, children }) => {
  return <button className={variant} onClick={onClick}>{children}</button>;
};

// Generic component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map(renderItem)}</ul>;
}

// Usage
<List<User> 
  items={users} 
  renderItem={user => <li key={user.id}>{user.name}</li>}
/>
```

### Q66: How to type events in React?

**Answer:**
```typescript
// Form events
function Form() {
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
  };
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log(e.target.value);
  };
  
  const handleSelect = (e: React.ChangeEvent<HTMLSelectElement>) => {
    console.log(e.target.value);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} />
      <select onChange={handleSelect} />
    </form>
  );
}

// Mouse events
function Button() {
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    console.log(e.clientX, e.clientY);
  };
  
  const handleHover = (e: React.MouseEvent<HTMLDivElement>) => {
    console.log('Hovering');
  };
  
  return (
    <div onMouseEnter={handleHover}>
      <button onClick={handleClick}>Click</button>
    </div>
  );
}

// Keyboard events
function Input() {
  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key === 'Enter') {
      console.log('Enter pressed');
    }
  };
  
  return <input onKeyDown={handleKeyDown} />;
}
```

### Q67: How to type refs in TypeScript?

**Answer:**
```typescript
// DOM element ref
function Component() {
  const inputRef = useRef<HTMLInputElement>(null);
  const divRef = useRef<HTMLDivElement>(null);
  
  useEffect(() => {
    inputRef.current?.focus();
    const width = divRef.current?.offsetWidth;
  }, []);
  
  return (
    <>
      <input ref={inputRef} />
      <div ref={divRef} />
    </>
  );
}

// Mutable value ref
function Timer() {
  const intervalRef = useRef<number | null>(null);
  
  useEffect(() => {
    intervalRef.current = window.setInterval(() => {
      console.log('tick');
    }, 1000);
    
    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
      }
    };
  }, []);
}

// Component ref with forwardRef
interface InputHandle {
  focus: () => void;
  clear: () => void;
}

const CustomInput = forwardRef<InputHandle, {}>((props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);
  
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    clear: () => {
      if (inputRef.current) inputRef.current.value = '';
    }
  }));
  
  return <input ref={inputRef} />;
});

// Usage
function Parent() {
  const ref = useRef<InputHandle>(null);
  
  return (
    <>
      <CustomInput ref={ref} />
      <button onClick={() => ref.current?.focus()}>Focus</button>
    </>
  );
}
```

### Q68: How to type custom Hooks?

**Answer:**
```typescript
// Return tuple
function useToggle(initialValue: boolean): [boolean, () => void] {
  const [value, setValue] = useState(initialValue);
  const toggle = () => setValue(v => !v);
  return [value, toggle];
}

// Return object
interface UseApiResult<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
  refetch: () => void;
}

function useApi<T>(url: string): UseApiResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  
  const fetchData = async () => {
    try {
      const response = await fetch(url);
      const json = await response.json();
      setData(json);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Unknown error');
    } finally {
      setLoading(false);
    }
  };
  
  useEffect(() => {
    fetchData();
  }, [url]);
  
  return { data, loading, error, refetch: fetchData };
}

// Usage
function Users() {
  const { data, loading, error } = useApi<User[]>('/api/users');
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {data?.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### Q69: What is React.FC and should you use it?

**Answer:**
`React.FC` (FunctionComponent) is a TypeScript type for functional components.

```typescript
// With React.FC
const Component: React.FC<Props> = ({ prop1, prop2 }) => {
  return <div>{prop1}</div>;
};

// Without React.FC (recommended)
interface Props {
  prop1: string;
  prop2: number;
}

function Component({ prop1, prop2 }: Props) {
  return <div>{prop1}</div>;
}
```

**Pros of React.FC:**
- Implicit children prop
- Explicit return type

**Cons (why it's less recommended now):**
- Makes children always optional
- Doesn't work well with generics
- Extra verbosity

**Current recommendation:** Use regular function syntax.

### Q70: How to type context in TypeScript?

**Answer:**
```typescript
// Define context type
interface AuthContextType {
  user: User | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  isAuthenticated: boolean;
}

// Create context with type
const AuthContext = createContext<AuthContextType | null>(null);

// Provider component
export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  
  const login = async (email: string, password: string) => {
    const user = await api.login(email, password);
    setUser(user);
  };
  
  const logout = () => setUser(null);
  
  const value: AuthContextType = {
    user,
    login,
    logout,
    isAuthenticated: !!user
  };
  
  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

// Custom hook with type guard
export function useAuth(): AuthContextType {
  const context = useContext(AuthContext);
  
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  
  return context;
}

// Usage
function Profile() {
  const { user, logout } = useAuth(); // Fully typed
  
  return (
    <div>
      <p>{user?.name}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```
