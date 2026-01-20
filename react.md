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
