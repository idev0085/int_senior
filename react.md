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

### Q6: What are Design Patterns and why are they important?

**Answer:**

Design patterns are **proven solutions to common problems** in software design. They're like blueprints or templates that you can adapt to solve recurring design challenges in your code.

**Think of it like this:**
- You're building a house and need to solve common problems (doors, windows, plumbing)
- Instead of inventing solutions from scratch, you use proven architectural patterns
- Design patterns = Proven solutions that architects have refined over years

---

#### **Why Use Design Patterns?**

```javascript
// ❌ Without patterns: Messy, hard to maintain
function App() {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [settings, setSettings] = useState({});
  
  // Everything passed as props to every component
  return (
    <Header user={user} theme={theme} settings={settings} />
    <Sidebar user={user} theme={theme} />
    <Content user={user} theme={theme} settings={settings} />
  );
}

// ✅ With patterns: Clean, scalable, maintainable
function App() {
  // Context Pattern - shared state without prop drilling
  return (
    <ThemeProvider>
      <UserProvider>
        <SettingsProvider>
          <Layout />
        </SettingsProvider>
      </UserProvider>
    </ThemeProvider>
  );
}
```

**Benefits:**
1. **Reusability**: Same solution works in different contexts
2. **Communication**: Team speaks same language ("Let's use Observer pattern")
3. **Maintainability**: Easier to understand and modify
4. **Performance**: Proven patterns are optimized
5. **Best Practices**: Learn from collective experience

---

#### **Categories of Design Patterns**

**1. Creational Patterns** - How objects are created
**2. Structural Patterns** - How objects are composed
**3. Behavioral Patterns** - How objects communicate

---

### **React-Specific Design Patterns**

#### **1. Container/Presentational Pattern**

**Concept**: Separate logic from presentation

```javascript
// ❌ Without pattern: Mixed concerns
function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch('/api/user')
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <Spinner />;
  
  return (
    <div>
      <img src={user.avatar} />
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
}

// ✅ With pattern: Separated concerns

// Container (Logic)
function UserProfileContainer() {
  const { user, loading } = useUser();
  
  if (loading) return <Spinner />;
  return <UserProfile user={user} />;
}

// Presentational (UI)
function UserProfile({ user }) {
  return (
    <div>
      <img src={user.avatar} alt={user.name} />
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
}
```

**When to use:**
- Reusable UI components
- Testing UI separately from logic
- Different data sources for same UI

---

#### **2. Higher-Order Components (HOC) Pattern**

**Concept**: Function that takes a component and returns a new enhanced component

```javascript
// HOC that adds authentication check
function withAuth(Component) {
  return function AuthComponent(props) {
    const { isAuthenticated, user } = useAuth();
    
    if (!isAuthenticated) {
      return <Navigate to="/login" />;
    }
    
    return <Component {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const ProtectedSettings = withAuth(Settings);

// Multiple HOCs
function withLogging(Component) {
  return function LoggingComponent(props) {
    useEffect(() => {
      console.log('Component mounted:', Component.name);
    }, []);
    
    return <Component {...props} />;
  };
}

// Compose multiple HOCs
const EnhancedDashboard = withAuth(withLogging(Dashboard));
```

**When to use:**
- Cross-cutting concerns (auth, logging, analytics)
- Code reuse across multiple components
- Props manipulation

**Modern Alternative: Custom Hooks**
```javascript
// ✅ Modern approach with hooks
function Dashboard() {
  useAuth(); // Handles auth logic
  useLogging('Dashboard'); // Handles logging
  
  return <div>Dashboard Content</div>;
}
```

---

#### **3. Render Props Pattern**

**Concept**: Component receives a function as prop that returns React elements

```javascript
// Render props pattern
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  const handleMouseMove = (e) => {
    setPosition({ x: e.clientX, y: e.clientY });
  };
  
  return (
    <div onMouseMove={handleMouseMove} style={{ height: '100vh' }}>
      {render(position)}
    </div>
  );
}

// Usage
function App() {
  return (
    <MouseTracker 
      render={({ x, y }) => (
        <h1>Mouse position: {x}, {y}</h1>
      )}
    />
  );
}

// Alternative: Children as function
function MouseTracker({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  return (
    <div onMouseMove={handleMouseMove}>
      {children(position)}
    </div>
  );
}

// Usage
<MouseTracker>
  {({ x, y }) => <p>Position: {x}, {y}</p>}
</MouseTracker>
```

**When to use:**
- Sharing behavior between components
- Flexible rendering logic
- Component composition

**Modern Alternative: Custom Hooks**
```javascript
// ✅ Cleaner with hooks
function useMouse() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handleMove = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);
  
  return position;
}

function App() {
  const { x, y } = useMouse();
  return <h1>Position: {x}, {y}</h1>;
}
```

---

#### **4. Compound Components Pattern**

**Concept**: Components that work together, sharing implicit state

```javascript
// Example: Accordion component
const AccordionContext = createContext();

function Accordion({ children }) {
  const [openItem, setOpenItem] = useState(null);
  
  return (
    <AccordionContext.Provider value={{ openItem, setOpenItem }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

Accordion.Item = function Item({ id, children }) {
  const { openItem, setOpenItem } = useContext(AccordionContext);
  const isOpen = openItem === id;
  
  return (
    <div className="accordion-item">
      <button onClick={() => setOpenItem(isOpen ? null : id)}>
        {isOpen ? '−' : '+'}
      </button>
      {isOpen && <div>{children}</div>}
    </div>
  );
};

// Usage - Very flexible!
function App() {
  return (
    <Accordion>
      <Accordion.Item id="1">
        <h3>Section 1</h3>
        <p>Content 1</p>
      </Accordion.Item>
      <Accordion.Item id="2">
        <h3>Section 2</h3>
        <p>Content 2</p>
      </Accordion.Item>
    </Accordion>
  );
}
```

**Real-world examples:**
- React Router: `<Router>`, `<Route>`, `<Link>`
- Reach UI: `<Tabs>`, `<TabList>`, `<Tab>`, `<TabPanels>`
- Headless UI components

---

#### **5. Provider Pattern**

**Concept**: Make data available to multiple components via Context API

```javascript
// Create theme context
const ThemeContext = createContext();

// Provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom hook for consuming
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// Usage
function App() {
  return (
    <ThemeProvider>
      <Navbar />
      <Main />
      <Footer />
    </ThemeProvider>
  );
}

function Navbar() {
  const { theme, toggleTheme } = useTheme();
  return (
    <nav className={theme}>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </nav>
  );
}
```

**When to use:**
- Global state (theme, auth, language)
- Avoiding prop drilling
- Dependency injection

---

#### **6. Custom Hooks Pattern**

**Concept**: Extract component logic into reusable functions

```javascript
// Example: Form handling hook
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = (callback) => (e) => {
    e.preventDefault();
    callback(values);
  };
  
  const reset = () => setValues(initialValues);
  
  return { values, errors, handleChange, handleSubmit, reset };
}

// Usage
function LoginForm() {
  const { values, handleChange, handleSubmit } = useForm({
    email: '',
    password: ''
  });
  
  const onSubmit = (data) => {
    console.log('Submitting:', data);
  };
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input name="email" value={values.email} onChange={handleChange} />
      <input name="password" value={values.password} onChange={handleChange} />
      <button type="submit">Login</button>
    </form>
  );
}
```

**Common Custom Hooks:**
```javascript
// Fetch data
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);
  
  return { data, loading, error };
}

// Local storage
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const saved = localStorage.getItem(key);
    return saved ? JSON.parse(saved) : initialValue;
  });
  
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);
  
  return [value, setValue];
}

// Debounce
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  
  return debouncedValue;
}
```

---

#### **7. Observer Pattern (Pub/Sub)**

**Concept**: Objects subscribe to events and get notified when events occur

```javascript
// Event emitter
class EventEmitter {
  constructor() {
    this.events = {};
  }
  
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    this.events[event].push(callback);
  }
  
  off(event, callback) {
    if (this.events[event]) {
      this.events[event] = this.events[event].filter(cb => cb !== callback);
    }
  }
  
  emit(event, data) {
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
}

// Usage in React
const eventBus = new EventEmitter();

function ComponentA() {
  const handleClick = () => {
    eventBus.emit('userAction', { action: 'clicked', timestamp: Date.now() });
  };
  
  return <button onClick={handleClick}>Trigger Event</button>;
}

function ComponentB() {
  const [events, setEvents] = useState([]);
  
  useEffect(() => {
    const handler = (data) => {
      setEvents(prev => [...prev, data]);
    };
    
    eventBus.on('userAction', handler);
    return () => eventBus.off('userAction', handler);
  }, []);
  
  return <div>Events: {events.length}</div>;
}
```

**Modern approach with Context:**
```javascript
// Better approach using Context + useReducer
const AppContext = createContext();

function AppProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <AppContext.Provider value={{ state, dispatch }}>
      {children}
    </AppContext.Provider>
  );
}
```

---

#### **8. Singleton Pattern**

**Concept**: Ensure a class has only one instance

```javascript
// API Client singleton
class APIClient {
  static instance = null;
  
  constructor() {
    if (APIClient.instance) {
      return APIClient.instance;
    }
    
    this.baseURL = process.env.API_URL;
    this.token = null;
    APIClient.instance = this;
  }
  
  setToken(token) {
    this.token = token;
  }
  
  async get(endpoint) {
    const response = await fetch(`${this.baseURL}${endpoint}`, {
      headers: { Authorization: `Bearer ${this.token}` }
    });
    return response.json();
  }
}

// Usage - always returns same instance
const api1 = new APIClient();
const api2 = new APIClient();
console.log(api1 === api2); // true

// In React
function useAPI() {
  const apiClient = useMemo(() => new APIClient(), []);
  return apiClient;
}
```

---

#### **9. Factory Pattern**

**Concept**: Create objects without specifying exact class

```javascript
// Component factory
function createButton(type) {
  const buttons = {
    primary: ({ children, onClick }) => (
      <button className="btn-primary" onClick={onClick}>
        {children}
      </button>
    ),
    secondary: ({ children, onClick }) => (
      <button className="btn-secondary" onClick={onClick}>
        {children}
      </button>
    ),
    danger: ({ children, onClick }) => (
      <button className="btn-danger" onClick={onClick}>
        {children}
      </button>
    )
  };
  
  return buttons[type] || buttons.primary;
}

// Usage
function App() {
  const PrimaryButton = createButton('primary');
  const DangerButton = createButton('danger');
  
  return (
    <div>
      <PrimaryButton onClick={() => console.log('Primary')}>
        Save
      </PrimaryButton>
      <DangerButton onClick={() => console.log('Danger')}>
        Delete
      </DangerButton>
    </div>
  );
}
```

---

#### **10. Strategy Pattern**

**Concept**: Define family of algorithms, make them interchangeable

```javascript
// Sorting strategies
const sortStrategies = {
  byName: (items) => [...items].sort((a, b) => a.name.localeCompare(b.name)),
  byPrice: (items) => [...items].sort((a, b) => a.price - b.price),
  byDate: (items) => [...items].sort((a, b) => new Date(b.date) - new Date(a.date))
};

function ProductList() {
  const [products, setProducts] = useState([]);
  const [sortBy, setSortBy] = useState('byName');
  
  const sortedProducts = sortStrategies[sortBy](products);
  
  return (
    <div>
      <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
        <option value="byName">Name</option>
        <option value="byPrice">Price</option>
        <option value="byDate">Date</option>
      </select>
      
      {sortedProducts.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

---

### **Pattern Comparison**

| Pattern | Use Case | Modern Alternative |
|---------|----------|-------------------|
| HOC | Cross-cutting concerns | Custom Hooks |
| Render Props | Sharing behavior | Custom Hooks |
| Container/Presentational | Separate logic/UI | Still relevant |
| Compound Components | Flexible component APIs | Still relevant |
| Provider | Global state | Context + useReducer |
| Observer | Event handling | Context or State Management |

---

### **When to Use Which Pattern?**

```javascript
// ✅ Custom Hooks - Most common, use for logic reuse
function useAuth() { /* ... */ }

// ✅ Compound Components - For flexible component libraries
<Tabs>
  <TabList>...</TabList>
  <TabPanels>...</TabPanels>
</Tabs>

// ✅ Provider Pattern - For global state
<ThemeProvider>
  <App />
</ThemeProvider>

// ✅ Container/Presentational - For complex UI with separate logic
function UserDashboardContainer() {
  const data = useUserData();
  return <UserDashboard data={data} />;
}

// ⚠️ HOC - Legacy pattern, use hooks instead
const Enhanced = withAuth(Component); // Old way
function Component() {
  useAuth(); // New way
  return <div>...</div>;
}
```

---

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

---

**Q55.1: Do Error Boundaries work with lazy loading (React.lazy)?**

**Answer:** Yes! Error boundaries work perfectly with lazy loading and are actually **essential** for catching chunk loading failures.

**Correct Pattern:**
```javascript
// ✅ Correct: Error Boundary wraps Suspense
<ErrorBoundary fallback={<ErrorPage />}>
  <Suspense fallback={<Loading />}>
    <LazyComponent />
  </Suspense>
</ErrorBoundary>
```

**Why this order matters:**
- **Suspense** handles the loading state (while chunk downloads)
- **ErrorBoundary** catches errors during:
  - Chunk download failures (network errors)
  - Component initialization errors
  - Rendering errors in the lazy component

**Complete Example:**
```javascript
import { lazy, Suspense } from 'react';

// Lazy load components
const AdminDashboard = lazy(() => import('./AdminDashboard'));
const UserProfile = lazy(() => import('./UserProfile'));

// Error Boundary with retry
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Lazy load error:', error);
    // Log to error service (Sentry, LogRocket, etc.)
  }
  
  resetError = () => {
    this.setState({ hasError: false, error: null });
  };
  
  render() {
    if (this.state.hasError) {
      return (
        <div className="error-container">
          <h2>Failed to load component</h2>
          <p>{this.state.error?.message}</p>
          <button onClick={this.resetError}>Retry</button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// App with lazy loading + error handling
function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/admin" element={<AdminDashboard />} />
          <Route path="/profile" element={<UserProfile />} />
        </Routes>
      </Suspense>
    </ErrorBoundary>
  );
}
```

**Handling Chunk Load Errors (Network Failures):**
```javascript
// Automatic retry on chunk load failure
const LazyComponent = lazy(() => 
  import('./Component')
    .catch((error) => {
      console.error('Chunk load error:', error);
      
      // Retry once after delay
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve(import('./Component'));
        }, 1000);
      });
    })
);

// Advanced: Retry with exponential backoff
function lazyWithRetry(importFn, retries = 3, delay = 1000) {
  return lazy(() => 
    new Promise((resolve, reject) => {
      const attemptLoad = (retriesLeft) => {
        importFn()
          .then(resolve)
          .catch((error) => {
            if (retriesLeft === 0) {
              reject(error);
            } else {
              setTimeout(() => {
                console.log(`Retrying... (${retriesLeft} attempts left)`);
                attemptLoad(retriesLeft - 1);
              }, delay);
            }
          });
      };
      
      attemptLoad(retries);
    })
  );
}

// Usage
const AdminPanel = lazyWithRetry(
  () => import('./AdminPanel'),
  3,  // retry 3 times
  1500 // 1.5s delay between retries
);
```

**Granular Error Boundaries for Different Routes:**
```javascript
function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* Separate error boundary for admin section */}
        <Route path="/admin/*" element={
          <ErrorBoundary fallback={<AdminErrorPage />}>
            <Suspense fallback={<AdminLoader />}>
              <AdminRoutes />
            </Suspense>
          </ErrorBoundary>
        } />
        
        {/* Separate error boundary for user section */}
        <Route path="/user/*" element={
          <ErrorBoundary fallback={<UserErrorPage />}>
            <Suspense fallback={<UserLoader />}>
              <UserRoutes />
            </Suspense>
          </ErrorBoundary>
        } />
      </Routes>
    </BrowserRouter>
  );
}
```

**Nested Error Boundaries with Lazy Loading:**
```javascript
function Dashboard() {
  return (
    <div>
      <Header />
      
      {/* Main content error boundary */}
      <ErrorBoundary fallback={<MainContentError />}>
        <Suspense fallback={<ContentLoader />}>
          <LazyMainContent />
        </Suspense>
      </ErrorBoundary>
      
      {/* Sidebar error boundary */}
      <ErrorBoundary fallback={<SidebarError />}>
        <Suspense fallback={<SidebarLoader />}>
          <LazySidebar />
        </Suspense>
      </ErrorBoundary>
      
      {/* Widget error boundary */}
      <ErrorBoundary fallback={<WidgetError />}>
        <Suspense fallback={<WidgetLoader />}>
          <LazyWidget />
        </Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

**Track Chunk Load Failures:**
```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    // Detect chunk load errors
    const isChunkLoadError = error.name === 'ChunkLoadError' || 
                             error.message.includes('Loading chunk');
    
    if (isChunkLoadError) {
      console.error('Chunk failed to load:', error);
      
      // Analytics tracking
      analytics.track('chunk_load_error', {
        error: error.message,
        component: errorInfo.componentStack
      });
      
      // Optionally reload the page
      if (window.confirm('App needs to reload. Reload now?')) {
        window.location.reload();
      }
    } else {
      console.error('Component error:', error, errorInfo);
    }
  }
  
  render() {
    if (this.state.hasError) {
      const isChunkError = this.state.error?.message?.includes('chunk');
      
      return (
        <div>
          <h2>{isChunkError ? 'Failed to load' : 'Something went wrong'}</h2>
          <p>{this.state.error?.message}</p>
          <button onClick={() => window.location.reload()}>
            Reload Page
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}
```

**Best Practices:**
1. ✅ Always wrap `Suspense` with `ErrorBoundary`
2. ✅ Use granular boundaries for different sections
3. ✅ Implement retry logic for network failures
4. ✅ Provide clear error messages and recovery options
5. ✅ Log chunk load errors for monitoring
6. ✅ Consider automatic page reload for chunk errors

**Common Errors Caught:**
- **ChunkLoadError**: Failed to load JavaScript chunk (network issue)
- **Syntax errors**: In the lazy-loaded component
- **Runtime errors**: During component initialization or render
- **Module not found**: Import path issues

**What Still Won't Be Caught:**
- Errors in event handlers (use try-catch)
- Async errors outside React lifecycle (use try-catch)
- Errors in the error boundary itself

---

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

---

### Q71: How do you implement Error Boundaries in functional components?

**Answer:**
Error Boundaries **can only be implemented as class components** (React doesn't provide hooks for `getDerivedStateFromError` or `componentDidCatch`). However, you can create reusable class-based boundaries and use them with functional components.

---

## **Approach 1: Reusable Class-Based Error Boundary**

**Create once, use everywhere:**

```javascript
// ErrorBoundary.jsx - Create this once
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { 
      hasError: false, 
      error: null,
      errorInfo: null 
    };
  }

  static getDerivedStateFromError(error) {
    // Update state so next render shows fallback UI
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Log error to error reporting service
    this.setState({ errorInfo });
    
    console.error('Error caught:', error);
    console.error('Component stack:', errorInfo.componentStack);
    
    // Send to error tracking service
    if (this.props.onError) {
      this.props.onError(error, errorInfo);
    }
  }

  resetError = () => {
    this.setState({ 
      hasError: false, 
      error: null, 
      errorInfo: null 
    });
  };

  render() {
    if (this.state.hasError) {
      // Custom fallback UI from props
      if (this.props.fallback) {
        return this.props.fallback({
          error: this.state.error,
          errorInfo: this.state.errorInfo,
          resetError: this.resetError
        });
      }

      // Default fallback UI
      return (
        <div style={{ padding: '20px', border: '1px solid red' }}>
          <h2>⚠️ Something went wrong</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            <summary>Error details</summary>
            <p><strong>Error:</strong> {this.state.error?.message}</p>
            <p><strong>Stack:</strong></p>
            <pre>{this.state.errorInfo?.componentStack}</pre>
          </details>
          <button onClick={this.resetError}>
            🔄 Try Again
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

## **Approach 2: Use with Functional Components**

```javascript
// App.jsx - All functional!
import ErrorBoundary from './ErrorBoundary';

function ProblematicComponent() {
  const [count, setCount] = useState(0);
  
  // This error will be caught by ErrorBoundary
  if (count > 5) {
    throw new Error('Count exceeded limit!');
  }
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary>
      <ProblematicComponent />
    </ErrorBoundary>
  );
}
```

---

## **Approach 3: Custom Fallback UI**

```javascript
function App() {
  return (
    <ErrorBoundary
      fallback={({ error, resetError }) => (
        <div className="custom-error">
          <h1>😢 Oh no!</h1>
          <p>Something went wrong: {error.message}</p>
          <button onClick={resetError}>Reset</button>
          <button onClick={() => window.location.href = '/'}>
            Go Home
          </button>
        </div>
      )}
      onError={(error, errorInfo) => {
        // Send to Sentry, LogRocket, etc.
        console.error('Logged to service:', error);
      }}
    >
      <MyApp />
    </ErrorBoundary>
  );
}
```

---

## **Approach 4: Granular Error Boundaries**

```javascript
function Dashboard() {
  return (
    <div>
      {/* Header error won't crash sidebar or main content */}
      <ErrorBoundary fallback={<HeaderError />}>
        <Header />
      </ErrorBoundary>
      
      <div style={{ display: 'flex' }}>
        {/* Sidebar error won't crash main content */}
        <ErrorBoundary fallback={<SidebarError />}>
          <Sidebar />
        </ErrorBoundary>
        
        {/* Main content error won't crash sidebar */}
        <ErrorBoundary fallback={<MainError />}>
          <MainContent />
        </ErrorBoundary>
      </div>
      
      {/* Footer always renders even if others fail */}
      <Footer />
    </div>
  );
}
```

---

## **Approach 5: Custom Hook for Error Handling (Async/Events)**

**Note:** Error boundaries DON'T catch:
- ❌ Async errors (fetch, promises)
- ❌ Event handler errors
- ❌ Errors in useEffect

**Solution: Custom error hook**

```javascript
// useErrorHandler.js
import { useState } from 'react';

function useErrorHandler() {
  const [error, setError] = useState(null);

  const handleError = (error) => {
    setError(error);
    console.error('Error caught by hook:', error);
  };

  const resetError = () => setError(null);

  // Throw error to trigger error boundary
  if (error) {
    throw error;
  }

  return { handleError, resetError };
}

export default useErrorHandler;
```

**Usage:**

```javascript
function UserProfile() {
  const { handleError } = useErrorHandler();
  const [user, setUser] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        const response = await fetch('/api/user');
        if (!response.ok) throw new Error('Failed to fetch user');
        const data = await response.json();
        setUser(data);
      } catch (err) {
        handleError(err); // This will trigger error boundary!
      }
    }
    fetchUser();
  }, []);

  return <div>{user?.name}</div>;
}

// Wrap with error boundary
<ErrorBoundary>
  <UserProfile />
</ErrorBoundary>
```

---

## **Approach 6: Using react-error-boundary Library**

**Install:** `npm install react-error-boundary`

```javascript
import { ErrorBoundary } from 'react-error-boundary';

// Simple usage
function App() {
  return (
    <ErrorBoundary 
      fallback={<div>Something went wrong</div>}
    >
      <MyApp />
    </ErrorBoundary>
  );
}

// With custom fallback component
function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
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
        // Log to error service
        console.error('Error:', error);
      }}
      onReset={() => {
        // Reset app state
        console.log('Resetting...');
      }}
    >
      <MyApp />
    </ErrorBoundary>
  );
}

// With useErrorHandler hook from library
import { useErrorHandler } from 'react-error-boundary';

function UserList() {
  const handleError = useErrorHandler();
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers()
      .then(setUsers)
      .catch(handleError); // Triggers error boundary
  }, []);

  return <ul>{users.map(user => <li>{user.name}</li>)}</ul>;
}
```

---

## **Approach 7: TypeScript Version**

```typescript
// ErrorBoundary.tsx
import React, { Component, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: (props: FallbackProps) => ReactNode;
  onError?: (error: Error, errorInfo: React.ErrorInfo) => void;
}

interface State {
  hasError: boolean;
  error: Error | null;
  errorInfo: React.ErrorInfo | null;
}

interface FallbackProps {
  error: Error | null;
  errorInfo: React.ErrorInfo | null;
  resetError: () => void;
}

class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = {
      hasError: false,
      error: null,
      errorInfo: null
    };
  }

  static getDerivedStateFromError(error: Error): Partial<State> {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo): void {
    this.setState({ errorInfo });

    if (this.props.onError) {
      this.props.onError(error, errorInfo);
    }
  }

  resetError = (): void => {
    this.setState({
      hasError: false,
      error: null,
      errorInfo: null
    });
  };

  render(): ReactNode {
    if (this.state.hasError) {
      if (this.props.fallback) {
        return this.props.fallback({
          error: this.state.error,
          errorInfo: this.state.errorInfo,
          resetError: this.resetError
        });
      }

      return (
        <div>
          <h2>Something went wrong</h2>
          <button onClick={this.resetError}>Try Again</button>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

## **Approach 8: Error Boundary with React Query**

```javascript
import { QueryErrorResetBoundary } from '@tanstack/react-query';
import { ErrorBoundary } from 'react-error-boundary';

function App() {
  return (
    <QueryErrorResetBoundary>
      {({ reset }) => (
        <ErrorBoundary
          onReset={reset}
          fallbackRender={({ error, resetErrorBoundary }) => (
            <div>
              <h2>Query Error</h2>
              <p>{error.message}</p>
              <button onClick={resetErrorBoundary}>Retry</button>
            </div>
          )}
        >
          <UserList />
        </ErrorBoundary>
      )}
    </QueryErrorResetBoundary>
  );
}
```

---

## **What Error Boundaries Catch:**
✅ Rendering errors
✅ Lifecycle method errors
✅ Constructor errors in child components
✅ Errors in useEffect (if you re-throw them)

## **What Error Boundaries DON'T Catch:**
❌ Event handler errors (use try-catch)
❌ Async code errors (use try-catch or custom hook)
❌ Server-side rendering errors
❌ Errors in the error boundary itself
❌ Errors in setTimeout/setInterval

---

## **Best Practices:**

1. **Use multiple error boundaries** for different parts of your app
2. **Always log errors** to monitoring service (Sentry, LogRocket)
3. **Provide recovery options** (retry button, navigate home)
4. **Show user-friendly messages** (not technical stack traces)
5. **Test error scenarios** in development
6. **Use custom hooks** for async/event errors
7. **Consider using `react-error-boundary`** library for convenience

---

## **Complete Production Example:**

```javascript
// ErrorBoundary.jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Send to error tracking
    if (process.env.NODE_ENV === 'production') {
      Sentry.captureException(error, { extra: errorInfo });
    } else {
      console.error(error, errorInfo);
    }
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-container">
          <h1>Something went wrong</h1>
          <button onClick={() => window.location.reload()}>
            Reload Page
          </button>
          <button onClick={() => window.location.href = '/'}>
            Go Home
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// App.jsx (all functional!)
function App() {
  return (
    <ErrorBoundary>
      <Router>
        <ErrorBoundary fallback={<NavError />}>
          <Navigation />
        </ErrorBoundary>

        <Routes>
          <Route path="/" element={
            <ErrorBoundary fallback={<HomeError />}>
              <Home />
            </ErrorBoundary>
          } />
          
          <Route path="/dashboard" element={
            <ErrorBoundary fallback={<DashboardError />}>
              <Suspense fallback={<Loading />}>
                <Dashboard />
              </Suspense>
            </ErrorBoundary>
          } />
        </Routes>
      </Router>
    </ErrorBoundary>
  );
}
```

**Summary:** While error boundaries must be class components, you create them **once** and use them everywhere with your functional components!

---

**Multiple Error Boundaries (granular error handling):**
```javascript
function App() {
  return (
    <ErrorBoundary fallback={<AppError />}>
      <Header />
      
      <ErrorBoundary fallback={<SidebarError />}>
        <Sidebar />
      </ErrorBoundary>
      
      <ErrorBoundary fallback={<MainContentError />}>
        <MainContent />
      </ErrorBoundary>
      
      <Footer />
    </ErrorBoundary>
  );
}
```

**React 19 Proposed Hook (Future):**
```javascript
// Note: Not yet available, but proposed for future React versions
function MyComponent() {
  const [error, resetError] = useErrorBoundary();
  
  if (error) {
    return <div>Error: {error.message}</div>;
  }
  
  return <div>Normal content</div>;
}
```

**What Error Boundaries catch:**
- ✅ Render errors
- ✅ Lifecycle method errors
- ✅ Constructor errors

**What Error Boundaries DON'T catch:**
- ❌ Event handler errors (use try-catch)
- ❌ Async code (use try-catch or .catch())
- ❌ Server-side rendering errors
- ❌ Errors in the error boundary itself

---

### Q72: How do you replicate lifecycle methods in functional components with hooks?

**Answer:**
Functional components use hooks to replicate class component lifecycle methods.

**Lifecycle equivalents:**

**1. componentDidMount (run once on mount):**
```javascript
// Class component
class MyComponent extends React.Component {
  componentDidMount() {
    console.log('Component mounted');
    fetchData();
  }
}

// Functional component equivalent
function MyComponent() {
  useEffect(() => {
    console.log('Component mounted');
    fetchData();
  }, []); // Empty dependency array = run once on mount
}
```

**2. componentDidUpdate (run on updates):**
```javascript
// Class component
class MyComponent extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser(this.props.userId);
    }
  }
}

// Functional component equivalent
function MyComponent({ userId }) {
  useEffect(() => {
    fetchUser(userId);
  }, [userId]); // Run when userId changes
}
```

**3. componentWillUnmount (cleanup):**
```javascript
// Class component
class MyComponent extends React.Component {
  componentDidMount() {
    this.timer = setInterval(() => console.log('tick'), 1000);
  }
  
  componentWillUnmount() {
    clearInterval(this.timer);
  }
}

// Functional component equivalent
function MyComponent() {
  useEffect(() => {
    const timer = setInterval(() => console.log('tick'), 1000);
    
    // Cleanup function = componentWillUnmount
    return () => {
      clearInterval(timer);
    };
  }, []);
}
```

**4. componentDidMount + componentDidUpdate + componentWillUnmount:**
```javascript
// Class component
class UserProfile extends React.Component {
  componentDidMount() {
    this.subscribe(this.props.userId);
  }
  
  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      this.unsubscribe(prevProps.userId);
      this.subscribe(this.props.userId);
    }
  }
  
  componentWillUnmount() {
    this.unsubscribe(this.props.userId);
  }
}

// Functional component equivalent (cleaner!)
function UserProfile({ userId }) {
  useEffect(() => {
    subscribe(userId); // Mount + Update
    
    return () => {
      unsubscribe(userId); // Unmount + before next effect
    };
  }, [userId]);
}
```

**5. shouldComponentUpdate (prevent re-renders):**
```javascript
// Class component
class MyComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    return nextProps.value !== this.props.value;
  }
}

// Functional component equivalent
const MyComponent = React.memo(({ value }) => {
  return <div>{value}</div>;
}, (prevProps, nextProps) => {
  // Return true if props are equal (skip render)
  return prevProps.value === nextProps.value;
});
```

**6. getDerivedStateFromProps:**
```javascript
// Class component
class MyComponent extends React.Component {
  static getDerivedStateFromProps(props, state) {
    if (props.value !== state.prevValue) {
      return {
        prevValue: props.value,
        derivedValue: props.value * 2
      };
    }
    return null;
  }
}

// Functional component equivalent (usually not needed!)
function MyComponent({ value }) {
  // Just derive during render
  const derivedValue = value * 2;
  
  // Or use state if needed
  const [prevValue, setPrevValue] = useState(value);
  const [derivedValue, setDerivedValue] = useState(value * 2);
  
  if (value !== prevValue) {
    setPrevValue(value);
    setDerivedValue(value * 2);
  }
}
```

**Complete lifecycle comparison:**
```javascript
// Class component with all lifecycles
class CompleteExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  componentDidMount() {
    console.log('Mounted');
    this.timer = setInterval(this.tick, 1000);
  }
  
  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      console.log('Count updated:', this.state.count);
    }
  }
  
  componentWillUnmount() {
    console.log('Unmounting');
    clearInterval(this.timer);
  }
  
  tick = () => {
    this.setState(state => ({ count: state.count + 1 }));
  }
  
  render() {
    return <div>Count: {this.state.count}</div>;
  }
}

// Functional component equivalent
function CompleteExample() {
  const [count, setCount] = useState(0);
  
  // componentDidMount + componentWillUnmount
  useEffect(() => {
    console.log('Mounted');
    
    const timer = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    
    return () => {
      console.log('Unmounting');
      clearInterval(timer);
    };
  }, []);
  
  // componentDidUpdate (when count changes)
  useEffect(() => {
    console.log('Count updated:', count);
  }, [count]);
  
  return <div>Count: {count}</div>;
}
```

**Advanced patterns:**

**Run effect only after first render (skip mount):**
```javascript
function MyComponent({ value }) {
  const isFirstRender = useRef(true);
  
  useEffect(() => {
    if (isFirstRender.current) {
      isFirstRender.current = false;
      return; // Skip on mount
    }
    
    console.log('Value changed:', value);
  }, [value]);
}
```

**Custom hook for lifecycle logging:**
```javascript
function useLifecycleLogger(componentName) {
  useEffect(() => {
    console.log(`${componentName} mounted`);
    
    return () => {
      console.log(`${componentName} unmounted`);
    };
  }, [componentName]);
  
  useEffect(() => {
    console.log(`${componentName} updated`);
  });
}

// Usage
function MyComponent() {
  useLifecycleLogger('MyComponent');
  return <div>Content</div>;
}
```

**useLayoutEffect (componentDidMount/Update synchronously):**
```javascript
// Similar to useEffect but runs synchronously after DOM mutations
// Use for measuring DOM elements or preventing visual flicker

function MyComponent() {
  const ref = useRef();
  const [height, setHeight] = useState(0);
  
  useLayoutEffect(() => {
    // Runs before browser paint (synchronous)
    setHeight(ref.current.offsetHeight);
  }, []);
  
  return <div ref={ref}>Content</div>;
}
```

**Key differences:**
- **useEffect**: Asynchronous, runs after paint
- **useLayoutEffect**: Synchronous, runs before paint (like componentDidMount)
