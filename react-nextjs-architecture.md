# React/Next.js Architecture - Senior Developer Guide

## Table of Contents
1. [React Architecture Mastery](#react-architecture-mastery)
2. [Next.js Architectural Decisions](#nextjs-architectural-decisions)
3. [State Management Architecture](#state-management-architecture)
4. [Performance Optimization](#performance-optimization)
5. [Monorepo & Tooling](#monorepo--tooling)
6. [TypeScript Architecture](#typescript-architecture)
7. [Testing Strategy](#testing-strategy)
8. [Real-World Scenarios](#real-world-scenarios)
9. [Leadership & Communication](#leadership--communication)

---

## React Architecture Mastery

### Q1: Explain React's reconciliation algorithm and virtual DOM diffing process in detail.

**Answer:**

React's reconciliation is the process by which React updates the DOM. The algorithm works in several phases:

**Key Concepts:**
- **Virtual DOM**: A lightweight JavaScript representation of the actual DOM
- **Reconciliation**: The diffing algorithm that compares old and new virtual DOM trees
- **Commit Phase**: When changes are applied to the actual DOM

**Diffing Algorithm Strategy:**

1. **Different Element Types**: When root elements have different types, React tears down the old tree and builds a new one from scratch.
```jsx
// Old tree: <div><Counter /></div>
// New tree: <span><Counter /></span>
// Result: Completely unmounts old Counter and mounts new one
```

2. **Same Element Type, Different Attributes**: React updates only the changed attributes.
```jsx
// Old: <div className="before" />
// New: <div className="after" />
// Result: Only updates className attribute
```

3. **Recursing on Children**: React iterates through children simultaneously and generates mutations when there's a difference.

**The Key Optimization - Keys:**
```jsx
// Without keys (inefficient - all items re-rendered):
<ul>
  {items.map(item => <li>{item}</li>)}
</ul>

// With keys (efficient - only changed items updated):
<ul>
  {items.map(item => <li key={item.id}>{item}</li>)}
</ul>
```

**React 18 Concurrent Rendering:**
- **Interruptible Rendering**: React can pause, resume, or abandon rendering work
- **Priority-based Updates**: Urgent updates (typing) vs non-urgent (data fetching)
- **Time Slicing**: Breaks rendering work into chunks
- **Automatic Batching**: Multiple state updates batched automatically, even in async operations

---

### Q2: When and how should you use useTransition, useDeferredValue, and Suspense boundaries?

**Answer:**

**useTransition:**
Used to mark state updates as non-urgent, preventing them from blocking the UI.

```jsx
function SearchResults() {
  const [isPending, startTransition] = useTransition();
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  const handleChange = (e) => {
    const value = e.target.value;
    setQuery(value); // Urgent - update input immediately
    
    startTransition(() => {
      // Non-urgent - can be interrupted
      setResults(searchData(value));
    });
  };

  return (
    <div>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <Results data={results} />
    </div>
  );
}
```

**When to use useTransition:**
- Expensive state updates (filtering large lists, complex calculations)
- Navigation between tabs or views
- Search/filter operations
- Any update that shouldn't block user input

**useDeferredValue:**
Creates a deferred version of a value that lags behind urgent updates.

```jsx
function SearchPage({ query }) {
  const deferredQuery = useDeferredValue(query);
  const results = useMemo(() => 
    searchDatabase(deferredQuery), 
    [deferredQuery]
  );

  return (
    <div>
      <SearchResults 
        query={deferredQuery} 
        isStale={query !== deferredQuery}
      />
    </div>
  );
}
```

**useTransition vs useDeferredValue:**
- `useTransition`: Control when state updates happen (you own the setState)
- `useDeferredValue`: You receive a value as prop, want to defer renders based on it

**Suspense Boundaries:**
Handles loading states for async operations declaratively.

```jsx
// Component-level suspense
function ProfilePage({ userId }) {
  return (
    <Suspense fallback={<ProfileSkeleton />}>
      <ProfileDetails userId={userId} />
      <Suspense fallback={<PostsSkeleton />}>
        <ProfilePosts userId={userId} />
      </Suspense>
    </Suspense>
  );
}

// Data fetching with Suspense (React 18+)
function ProfileDetails({ userId }) {
  const user = use(fetchUser(userId)); // Suspends on first render
  return <div>{user.name}</div>;
}
```

**Suspense Best Practices:**
- Place boundaries strategically - not around every component
- Provide meaningful fallbacks that match content shape
- Use nested boundaries for granular loading states
- Combine with Error Boundaries for complete error handling

```jsx
function App() {
  return (
    <ErrorBoundary fallback={<ErrorPage />}>
      <Suspense fallback={<LayoutSkeleton />}>
        <Layout>
          <ErrorBoundary fallback={<SectionError />}>
            <Suspense fallback={<ContentSkeleton />}>
              <Content />
            </Suspense>
          </ErrorBoundary>
        </Layout>
      </Suspense>
    </ErrorBoundary>
  );
}
```

---

### Q3: Explain controlled vs uncontrolled components at scale and when to use each.

**Answer:**

**Controlled Components:**
React state is the "single source of truth" - form data handled by React.

```jsx
function ControlledForm() {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    newsletter: false
  });

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  return (
    <form>
      <input 
        name="email" 
        value={formData.email} 
        onChange={handleChange} 
      />
      <input 
        name="password" 
        value={formData.password} 
        onChange={handleChange} 
      />
      <input 
        name="newsletter" 
        type="checkbox"
        checked={formData.newsletter} 
        onChange={handleChange} 
      />
    </form>
  );
}
```

**Uncontrolled Components:**
Form data handled by the DOM itself, accessed via refs.

```jsx
function UncontrolledForm() {
  const emailRef = useRef();
  const passwordRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    const email = emailRef.current.value;
    const password = passwordRef.current.value;
    // Process form
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={emailRef} defaultValue="" />
      <input ref={passwordRef} type="password" />
    </form>
  );
}
```

**At Scale Comparison:**

| Aspect | Controlled | Uncontrolled |
|--------|-----------|-------------|
| **Performance** | Can cause re-renders on every keystroke | Better for large forms - no re-renders |
| **Validation** | Real-time validation easy | Validation on blur/submit only |
| **Conditional Logic** | Easy to show/hide fields based on values | More difficult, requires refs |
| **Integration** | Works well with React state management | Better for third-party libraries |
| **Testing** | Easier to test (state-based) | Requires DOM queries |
| **File Inputs** | Not possible (file inputs are always uncontrolled) | Natural fit |

**Hybrid Approach for Large Forms:**
```jsx
function LargeForm() {
  // Controlled for critical fields that need real-time validation
  const [email, setEmail] = useState('');
  const [emailError, setEmailError] = useState('');

  // Uncontrolled for simple fields
  const nameRef = useRef();
  const addressRef = useRef();
  const cityRef = useRef();
  const zipRef = useRef();

  const validateEmail = (value) => {
    if (!value.includes('@')) {
      setEmailError('Invalid email');
    } else {
      setEmailError('');
    }
  };

  return (
    <form>
      {/* Controlled - needs real-time validation */}
      <input 
        value={email}
        onChange={(e) => {
          setEmail(e.target.value);
          validateEmail(e.target.value);
        }}
      />
      {emailError && <span>{emailError}</span>}

      {/* Uncontrolled - simple data capture */}
      <input ref={nameRef} defaultValue="" />
      <input ref={addressRef} defaultValue="" />
      <input ref={cityRef} defaultValue="" />
      <input ref={zipRef} defaultValue="" />
    </form>
  );
}
```

**React Hook Form Approach (Best of Both Worlds):**
```jsx
import { useForm } from 'react-hook-form';

function OptimizedForm() {
  const { register, handleSubmit, watch, formState: { errors } } = useForm({
    mode: 'onBlur' // Only validate on blur, not every keystroke
  });

  // Watch specific fields when needed for conditional logic
  const country = watch('country');

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email', { required: true, pattern: /^\S+@\S+$/i })} />
      {errors.email && <span>Invalid email</span>}
      
      <input {...register('name', { required: true })} />
      
      {country === 'US' && (
        <input {...register('ssn', { required: true })} />
      )}
    </form>
  );
}
```

**When to Use Each:**
- **Controlled**: Form validation, conditional fields, multi-step forms, real-time formatting
- **Uncontrolled**: Simple forms, performance-critical forms, integrating with non-React libraries
- **Hybrid/React Hook Form**: Large enterprise forms with mixed requirements

---

### Q4: Explain advanced composition patterns: render props, HOCs, and compound components.

**Answer:**

**1. Render Props Pattern:**
A component with a function prop that returns React elements.

```jsx
// Basic Render Props
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = (e) => {
    this.setState({ x: e.clientX, y: e.clientY });
  };

  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

// Usage
<MouseTracker render={({ x, y }) => (
  <div>Mouse at ({x}, {y})</div>
)} />

// Modern Hooks Alternative
function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return position;
}

// Usage with hooks
function Component() {
  const { x, y } = useMousePosition();
  return <div>Mouse at ({x}, {y})</div>;
}
```

**2. Higher-Order Components (HOCs):**
A function that takes a component and returns a new component.

```jsx
// HOC for authentication
function withAuth(Component) {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();

    if (loading) return <Loading />;
    if (!user) return <Navigate to="/login" />;

    return <Component {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);

// HOC for data fetching
function withDataFetching(Component, fetchFn) {
  return function DataFetchingComponent(props) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
      fetchFn(props)
        .then(setData)
        .catch(setError)
        .finally(() => setLoading(false));
    }, [props.id]);

    return (
      <Component 
        {...props} 
        data={data} 
        loading={loading} 
        error={error} 
      />
    );
  };
}

// Composing multiple HOCs
const EnhancedComponent = compose(
  withAuth,
  withRouter,
  withDataFetching(fetchUserData)
)(UserProfile);
```

**HOC Best Practices:**
```jsx
// ❌ Don't modify the original component
function badHOC(WrappedComponent) {
  WrappedComponent.prototype.componentDidMount = function() {
    // Don't do this!
  };
  return WrappedComponent;
}

// ✅ Use composition
function goodHOC(WrappedComponent) {
  return class extends React.Component {
    componentDidMount() {
      // Add behavior
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}

// ✅ Forward refs
function withForwardRef(Component) {
  const WithRef = React.forwardRef((props, ref) => (
    <Component {...props} forwardedRef={ref} />
  ));
  
  WithRef.displayName = `withForwardRef(${Component.displayName || Component.name})`;
  return WithRef;
}

// ✅ Hoist static methods
import hoistNonReactStatics from 'hoist-non-react-statics';

function withHOC(WrappedComponent) {
  class HOC extends React.Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
  }
  return hoistNonReactStatics(HOC, WrappedComponent);
}
```

**3. Compound Components Pattern:**
Components that work together to form a complete UI.

```jsx
// Compound Component with Context
const TabsContext = React.createContext();

function Tabs({ children, defaultValue }) {
  const [activeTab, setActiveTab] = useState(defaultValue);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ value, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      className={activeTab === value ? 'active' : ''}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
}

function TabPanels({ children }) {
  return <div className="tab-panels">{children}</div>;
}

function TabPanel({ value, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === value ? <div>{children}</div> : null;
}

// Compound component exports
Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panels = TabPanels;
Tabs.Panel = TabPanel;

// Usage
function App() {
  return (
    <Tabs defaultValue="profile">
      <Tabs.List>
        <Tabs.Tab value="profile">Profile</Tabs.Tab>
        <Tabs.Tab value="settings">Settings</Tabs.Tab>
        <Tabs.Tab value="billing">Billing</Tabs.Tab>
      </Tabs.List>
      <Tabs.Panels>
        <Tabs.Panel value="profile">
          <ProfileContent />
        </Tabs.Panel>
        <Tabs.Panel value="settings">
          <SettingsContent />
        </Tabs.Panel>
        <Tabs.Panel value="billing">
          <BillingContent />
        </Tabs.Panel>
      </Tabs.Panels>
    </Tabs>
  );
}
```

**Advanced Compound Component with Flexible Order:**
```jsx
// Allow any order of components
function Select({ children, value, onChange }) {
  const [isOpen, setIsOpen] = useState(false);

  const context = {
    value,
    onChange,
    isOpen,
    setIsOpen
  };

  return (
    <SelectContext.Provider value={context}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
}

Select.Trigger = function Trigger({ children }) {
  const { isOpen, setIsOpen } = useContext(SelectContext);
  return (
    <button onClick={() => setIsOpen(!isOpen)}>
      {children}
    </button>
  );
};

Select.Options = function Options({ children }) {
  const { isOpen } = useContext(SelectContext);
  if (!isOpen) return null;
  return <div className="options">{children}</div>;
};

Select.Option = function Option({ value, children }) {
  const { onChange, setIsOpen } = useContext(SelectContext);
  return (
    <div onClick={() => {
      onChange(value);
      setIsOpen(false);
    }}>
      {children}
    </div>
  );
};
```

**When to Use Each Pattern:**

| Pattern | Use When | Avoid When |
|---------|----------|------------|
| **Render Props** | Need to share logic with custom rendering | Logic can be extracted to hooks (modern) |
| **HOCs** | Need to enhance multiple components similarly | Using hooks is simpler (modern approach) |
| **Compound Components** | Building component libraries, complex UI with shared state | Simple components, no shared state needed |
| **Custom Hooks** | Reusing stateful logic across components (preferred modern) | Need to modify rendering directly |

**Modern Recommendation:**
Most use cases for render props and HOCs are now better solved with **custom hooks**:

```jsx
// ✅ Modern approach with hooks
function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    checkAuth().then(setUser).finally(() => setLoading(false));
  }, []);
  
  return { user, loading };
}

// Clean, composable, no wrapper components
function Dashboard() {
  const { user, loading } = useAuth();
  const { data } = useData();
  
  if (loading) return <Loading />;
  return <div>Welcome {user.name}</div>;
}
```

---

### Q5: How do you implement inversion of control and dependency injection in React?

**Answer:**

**Inversion of Control (IoC) in React:**
Instead of components creating their dependencies, they receive them from outside.

**1. Dependency Injection via Props:**
```jsx
// ❌ Tight coupling - component creates its own dependencies
function UserProfile() {
  const api = new ApiService(); // Tightly coupled
  const [user, setUser] = useState(null);

  useEffect(() => {
    api.getUser().then(setUser);
  }, []);

  return <div>{user?.name}</div>;
}

// ✅ Dependency injection via props
function UserProfile({ apiService }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    apiService.getUser().then(setUser);
  }, [apiService]);

  return <div>{user?.name}</div>;
}

// Usage - inject dependency
<UserProfile apiService={new ApiService()} />
```

**2. Dependency Injection via Context:**
```jsx
// Create context for dependencies
const DependencyContext = React.createContext(null);

// Provider component
function DependencyProvider({ children, dependencies }) {
  return (
    <DependencyContext.Provider value={dependencies}>
      {children}
    </DependencyContext.Provider>
  );
}

// Hook to access dependencies
function useDependency(key) {
  const dependencies = useContext(DependencyContext);
  if (!dependencies || !dependencies[key]) {
    throw new Error(`Dependency ${key} not found`);
  }
  return dependencies[key];
}

// Component using dependency
function UserProfile() {
  const apiService = useDependency('apiService');
  const logger = useDependency('logger');

  const [user, setUser] = useState(null);

  useEffect(() => {
    logger.log('Fetching user');
    apiService.getUser().then(setUser);
  }, [apiService, logger]);

  return <div>{user?.name}</div>;
}

// App setup with dependency injection
function App() {
  const dependencies = {
    apiService: new ApiService(),
    logger: new Logger(),
    analytics: new Analytics()
  };

  return (
    <DependencyProvider dependencies={dependencies}>
      <UserProfile />
    </DependencyProvider>
  );
}
```

**3. Advanced DI Container Pattern:**
```jsx
// Dependency container
class Container {
  constructor() {
    this.dependencies = new Map();
    this.singletons = new Map();
  }

  register(name, factory, options = {}) {
    this.dependencies.set(name, { factory, options });
  }

  resolve(name) {
    const dependency = this.dependencies.get(name);
    if (!dependency) {
      throw new Error(`Dependency ${name} not registered`);
    }

    // Singleton pattern
    if (dependency.options.singleton) {
      if (!this.singletons.has(name)) {
        this.singletons.set(name, dependency.factory(this));
      }
      return this.singletons.get(name);
    }

    // Transient - new instance every time
    return dependency.factory(this);
  }
}

// Service definitions
class ApiService {
  constructor(httpClient, config) {
    this.httpClient = httpClient;
    this.config = config;
  }

  async getUser() {
    return this.httpClient.get(`${this.config.apiUrl}/user`);
  }
}

class HttpClient {
  async get(url) {
    return fetch(url).then(r => r.json());
  }
}

// Container setup
const container = new Container();

container.register('config', () => ({
  apiUrl: process.env.REACT_APP_API_URL
}), { singleton: true });

container.register('httpClient', () => new HttpClient(), { singleton: true });

container.register('apiService', (c) => new ApiService(
  c.resolve('httpClient'),
  c.resolve('config')
), { singleton: true });

// Context provider
const ContainerContext = React.createContext(null);

function ContainerProvider({ container, children }) {
  return (
    <ContainerContext.Provider value={container}>
      {children}
    </ContainerContext.Provider>
  );
}

// Hook to resolve dependencies
function useService(serviceName) {
  const container = useContext(ContainerContext);
  return useMemo(() => container.resolve(serviceName), [container, serviceName]);
}

// Component using DI
function UserProfile() {
  const apiService = useService('apiService');
  const [user, setUser] = useState(null);

  useEffect(() => {
    apiService.getUser().then(setUser);
  }, [apiService]);

  return <div>{user?.name}</div>;
}

// App
function App() {
  return (
    <ContainerProvider container={container}>
      <UserProfile />
    </ContainerProvider>
  );
}
```

**4. Plugin Architecture with IoC:**
```jsx
// Plugin system
class PluginManager {
  constructor() {
    this.plugins = new Map();
  }

  register(name, plugin) {
    this.plugins.set(name, plugin);
  }

  get(name) {
    return this.plugins.get(name);
  }

  execute(name, ...args) {
    const plugin = this.plugins.get(name);
    return plugin?.execute(...args);
  }
}

// Plugin definition
class AuthPlugin {
  execute(config) {
    return {
      login: (credentials) => { /* auth logic */ },
      logout: () => { /* logout logic */ },
      getUser: () => { /* get user logic */ }
    };
  }
}

class AnalyticsPlugin {
  execute(config) {
    return {
      track: (event) => { /* tracking logic */ },
      page: (name) => { /* page view logic */ }
    };
  }
}

// Setup
const pluginManager = new PluginManager();
pluginManager.register('auth', new AuthPlugin());
pluginManager.register('analytics', new AnalyticsPlugin());

// Context
const PluginContext = React.createContext(null);

function usePlugin(pluginName) {
  const manager = useContext(PluginContext);
  return useMemo(() => manager.execute(pluginName), [manager, pluginName]);
}

// Usage
function LoginForm() {
  const auth = usePlugin('auth');
  const analytics = usePlugin('analytics');

  const handleSubmit = async (credentials) => {
    await auth.login(credentials);
    analytics.track('user_logged_in');
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

**5. Testing Benefits of DI:**
```jsx
// Easy to mock dependencies in tests
describe('UserProfile', () => {
  it('displays user name', async () => {
    const mockApiService = {
      getUser: jest.fn().mockResolvedValue({ name: 'John' })
    };

    render(
      <DependencyProvider dependencies={{ apiService: mockApiService }}>
        <UserProfile />
      </DependencyProvider>
    );

    await waitFor(() => {
      expect(screen.getByText('John')).toBeInTheDocument();
    });
  });
});
```

**When to Use DI in React:**
- Large applications with many services
- Need to swap implementations (mock in tests, different providers in dev/prod)
- Building plugin systems or extensible architectures
- Managing complex dependencies between services
- Feature flags and A/B testing implementations

**When to Keep It Simple:**
- Small applications
- Few external dependencies
- Components that don't need testing flexibility
- Simple CRUD apps

---

### Q6: Explain Context performance pitfalls and solutions.

**Answer:**

**The Context Performance Problem:**

Every time a context value changes, ALL components consuming that context re-render, even if they only use a small part of the value.

```jsx
// ❌ Performance Problem
const AppContext = React.createContext();

function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  const [settings, setSettings] = useState({});
  const [notifications, setNotifications] = useState([]);

  // All consumers re-render when ANY value changes!
  const value = { user, setUser, theme, setTheme, settings, setSettings, notifications, setNotifications };

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}

function ThemeToggle() {
  const { theme, setTheme } = useContext(AppContext);
  // Re-renders when user, settings, or notifications change! 🔴
  return <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>Toggle</button>;
}
```

**Solution 1: Context Splitting**
Separate contexts for different domains.

```jsx
// ✅ Split contexts by concern
const UserContext = React.createContext();
const ThemeContext = React.createContext();
const SettingsContext = React.createContext();
const NotificationsContext = React.createContext();

function Providers({ children }) {
  return (
    <UserProvider>
      <ThemeProvider>
        <SettingsProvider>
          <NotificationsProvider>
            {children}
          </NotificationsProvider>
        </SettingsProvider>
      </ThemeProvider>
    </UserProvider>
  );
}

function ThemeToggle() {
  const { theme, setTheme } = useContext(ThemeContext);
  // Only re-renders when theme changes! ✅
  return <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>Toggle</button>;
}
```

**Solution 2: Separate State and Updaters**
```jsx
// Split state and setters into separate contexts
const StateContext = React.createContext();
const UpdatersContext = React.createContext();

function AppProvider({ children }) {
  const [state, setState] = useState({
    user: null,
    theme: 'light',
    settings: {}
  });

  // Updaters never change - wrapped in useMemo
  const updaters = useMemo(() => ({
    setUser: (user) => setState(s => ({ ...s, user })),
    setTheme: (theme) => setState(s => ({ ...s, theme })),
    setSettings: (settings) => setState(s => ({ ...s, settings }))
  }), []);

  return (
    <UpdatersContext.Provider value={updaters}>
      <StateContext.Provider value={state}>
        {children}
      </StateContext.Provider>
    </UpdatersContext.Provider>
  );
}

// Components that only update don't re-render on state changes
function LogoutButton() {
  const { setUser } = useContext(UpdatersContext); // Never re-renders! ✅
  return <button onClick={() => setUser(null)}>Logout</button>;
}
```

**Solution 3: Context Selectors (Manual)**
```jsx
// Create a selector-based context
function createSelectableContext() {
  const Context = React.createContext(null);

  function Provider({ value, children }) {
    const valueRef = useRef(value);
    const subscribersRef = useRef(new Set());

    useEffect(() => {
      valueRef.current = value;
      subscribersRef.current.forEach(callback => callback());
    }, [value]);

    const contextValue = useMemo(() => ({
      subscribe: (callback) => {
        subscribersRef.current.add(callback);
        return () => subscribersRef.current.delete(callback);
      },
      getSnapshot: () => valueRef.current
    }), []);

    return <Context.Provider value={contextValue}>{children}</Context.Provider>;
  }

  function useSelector(selector) {
    const context = useContext(Context);
    const [, forceRender] = useReducer(s => s + 1, 0);

    const selectedValueRef = useRef();
    selectedValueRef.current = selector(context.getSnapshot());

    useEffect(() => {
      return context.subscribe(() => {
        const newValue = selector(context.getSnapshot());
        if (newValue !== selectedValueRef.current) {
          selectedValueRef.current = newValue;
          forceRender();
        }
      });
    }, [context, selector]);

    return selectedValueRef.current;
  }

  return { Provider, useSelector };
}

// Usage
const { Provider: StoreProvider, useSelector } = createSelectableContext();

function App() {
  const [state] = useState({ user: null, theme: 'light', count: 0 });
  return (
    <StoreProvider value={state}>
      <ThemeDisplay />
      <Counter />
    </StoreProvider>
  );
}

function ThemeDisplay() {
  const theme = useSelector(state => state.theme); // Only re-renders when theme changes ✅
  return <div>Theme: {theme}</div>;
}

function Counter() {
  const count = useSelector(state => state.count); // Only re-renders when count changes ✅
  return <div>Count: {count}</div>;
}
```

**Solution 4: Use External State Manager**
```jsx
// Zustand - built-in selectors
import create from 'zustand';

const useStore = create((set) => ({
  user: null,
  theme: 'light',
  count: 0,
  setUser: (user) => set({ user }),
  setTheme: (theme) => set({ theme }),
  incrementCount: () => set(state => ({ count: state.count + 1 }))
}));

function ThemeDisplay() {
  const theme = useStore(state => state.theme); // Only re-renders when theme changes ✅
  return <div>Theme: {theme}</div>;
}

function Counter() {
  const count = useStore(state => state.count); // Only re-renders when count changes ✅
  return <div>Count: {count}</div>;
}
```

**Solution 5: Memoization of Context Value**
```jsx
// Always memoize context values!
function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');

  // ❌ Creates new object every render - causes all consumers to re-render
  // const value = { user, setUser, theme, setTheme };

  // ✅ Only creates new object when dependencies change
  const value = useMemo(() => ({
    user,
    setUser,
    theme,
    setTheme
  }), [user, theme]);

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}
```

**Solution 6: Composition Over Context**
```jsx
// Sometimes you don't need context at all!
// ❌ Using context unnecessarily
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Layout>
        <Header />
        <Content />
      </Layout>
    </ThemeContext.Provider>
  );
}

// ✅ Pass props directly when component tree is shallow
function App() {
  const [theme, setTheme] = useState('light');
  return (
    <Layout theme={theme}>
      <Header theme={theme} onThemeChange={setTheme} />
      <Content theme={theme} />
    </Layout>
  );
}
```

**Performance Comparison:**

| Approach | Re-render Behavior | Complexity | When to Use |
|----------|-------------------|------------|-------------|
| Single context | All consumers re-render | Low | Small apps, few consumers |
| Split contexts | Only relevant consumers | Medium | Medium apps, logical separation |
| Separate updaters | Updater consumers don't re-render | Medium | Many write-only components |
| Context selectors | Only on selected value change | High | Large apps, fine-grained control |
| External state manager | Built-in optimization | Low-Medium | Any app size, prefer modern approach |

**Best Practices:**
1. Always memoize context values with `useMemo`
2. Split contexts by concern (user, theme, settings)
3. Separate state and updaters when many components only update
4. Consider Zustand/Jotai for complex state with built-in selectors
5. Use composition and props for shallow component trees
6. Profile with React DevTools to identify real bottlenecks
7. Don't over-optimize - context is fine for most use cases!

---

## Next.js Architectural Decisions

### Q7: Explain App Router vs Pages Router - when to use each and migration strategy.

**Answer:**

**Pages Router (Traditional):**

**Architecture:**
```
pages/
  index.js          → /
  about.js          → /about
  blog/
    [slug].js       → /blog/:slug
  api/
    users.js        → /api/users
```

**Characteristics:**
- File-based routing in `pages/` directory
- Each page is a React component
- `getServerSideProps`, `getStaticProps` for data fetching
- All components are client components by default
- API routes in `pages/api/`

**App Router (Modern - Next.js 13+):**

**Architecture:**
```
app/
  layout.js         → Root layout
  page.js           → /
  about/
    page.js         → /about
  blog/
    [slug]/
      page.js       → /blog/:slug
  api/
    users/
      route.js      → /api/users
```

**Key Features:**
```jsx
// app/layout.js - Root Layout (required)
export default function RootLayout({ children }) {
  return (
    <html>
      <body>{children}</body>
    </html>
  );
}

// app/page.js - Server Component by default
export default async function Home() {
  const data = await fetch('https://api.example.com/data');
  return <div>{data}</div>;
}

// app/dashboard/layout.js - Nested layouts
export default function DashboardLayout({ children }) {
  return (
    <div>
      <Sidebar />
      {children}
    </div>
  );
}

// app/client-component.js - Opt-in to client components
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Comparison:**

| Feature | Pages Router | App Router |
|---------|-------------|-----------|
| **Default Component** | Client | Server |
| **Data Fetching** | getServerSideProps, getStaticProps | async/await in components |
| **Layouts** | Custom _app.js, _document.js | Native layouts with nesting |
| **Loading States** | Manual | loading.js, Suspense |
| **Error Handling** | Manual | error.js boundaries |
| **Streaming** | Not supported | Native with Suspense |
| **API Routes** | pages/api/ | app/api/route.js |
| **Middleware** | Supported | Enhanced support |
| **Bundle Size** | Larger (all client-side) | Smaller (server components) |
| **TypeScript** | Good support | Enhanced with typed routes |

**When to Use Pages Router:**
- Existing app with heavy investment in Pages Router
- Need stable, battle-tested features
- Team not ready for paradigm shift
- Using libraries not compatible with Server Components
- Need SSR with simple data fetching patterns
- Deadline-driven projects (less learning curve)

**When to Use App Router:**
- New projects (recommended)
- Need advanced streaming and partial rendering
- Want optimal bundle sizes
- Building complex data-fetching patterns
- Need nested layouts
- Want better TypeScript experience
- Long-term maintainability is priority

**Migration Strategy:**

**Phase 1: Incremental Adoption (Recommended)**
Next.js supports both routers simultaneously!

```
my-app/
  pages/              ← Keep existing pages
    index.js
    about.js
  app/                ← Add new routes here
    dashboard/
      page.js
    api/
      new-endpoint/
        route.js
```

**Step-by-Step Migration:**

1. **Setup (Next.js 13.4+):**
```bash
npm install next@latest react@latest react-dom@latest
```

```js
// next.config.js
module.exports = {
  experimental: {
    appDir: true // Enable App Router
  }
};
```

2. **Create Root Layout:**
```jsx
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

3. **Migrate One Route at a Time:**

**Before (Pages Router):**
```jsx
// pages/products/[id].js
export default function Product({ product }) {
  return <div>{product.name}</div>;
}

export async function getServerSideProps({ params }) {
  const product = await fetch(`/api/products/${params.id}`).then(r => r.json());
  return { props: { product } };
}
```

**After (App Router):**
```jsx
// app/products/[id]/page.js
export default async function Product({ params }) {
  const product = await fetch(`https://api.example.com/products/${params.id}`)
    .then(r => r.json());
  
  return <div>{product.name}</div>;
}

// Generate static params
export async function generateStaticParams() {
  const products = await fetch('https://api.example.com/products')
    .then(r => r.json());
  
  return products.map(product => ({
    id: product.id
  }));
}
```

4. **Migrate Data Fetching Patterns:**

**Pages Router:**
```jsx
// getServerSideProps
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}

// getStaticProps
export async function getStaticProps() {
  const data = await fetchData();
  return { props: { data }, revalidate: 60 };
}

// getStaticPaths
export async function getStaticPaths() {
  const paths = await fetchPaths();
  return { paths, fallback: false };
}
```

**App Router:**
```jsx
// Server Component - just await
export default async function Page() {
  const data = await fetch('...', {
    next: { revalidate: 60 } // ISR
  });
  return <div>{data}</div>;
}

// Dynamic routes
export async function generateStaticParams() {
  return await fetchPaths();
}

// Dynamic rendering
export const dynamic = 'force-dynamic'; // SSR
export const revalidate = 60; // ISR
```

5. **Handle Client-Side Interactivity:**

```jsx
// app/components/InteractiveButton.js
'use client'; // Must add this directive!

import { useState } from 'react';

export function InteractiveButton() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// app/page.js - Server Component
import { InteractiveButton } from './components/InteractiveButton';

export default async function Page() {
  const data = await fetchData(); // Server-side
  return (
    <div>
      <h1>{data.title}</h1>
      <InteractiveButton /> {/* Client-side */}
    </div>
  );
}
```

6. **Migrate API Routes:**

**Before:**
```jsx
// pages/api/users.js
export default async function handler(req, res) {
  if (req.method === 'GET') {
    const users = await getUsers();
    res.status(200).json(users);
  }
}
```

**After:**
```jsx
// app/api/users/route.js
export async function GET(request) {
  const users = await getUsers();
  return Response.json(users);
}

export async function POST(request) {
  const body = await request.json();
  const user = await createUser(body);
  return Response.json(user, { status: 201 });
}
```

7. **Migrate Layouts:**

**Before:**
```jsx
// pages/_app.js
export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

**After:**
```jsx
// app/layout.js
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}

// app/dashboard/layout.js - Nested layout
export default function DashboardLayout({ children }) {
  return (
    <div>
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}
```

8. **Add Loading and Error States:**

```jsx
// app/products/loading.js
export default function Loading() {
  return <Skeleton />;
}

// app/products/error.js
'use client';

export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

**Migration Checklist:**
- [ ] Update Next.js to 13.4+
- [ ] Create app directory with root layout
- [ ] Identify high-traffic or complex pages to migrate first
- [ ] Convert getServerSideProps/getStaticProps to async components
- [ ] Add 'use client' to interactive components
- [ ] Migrate API routes to route handlers
- [ ] Update imports and paths
- [ ] Test each migrated route thoroughly
- [ ] Monitor performance and bundle size
- [ ] Remove pages directory when fully migrated

**Common Gotchas:**

```jsx
// ❌ Using hooks in Server Components
export default async function Page() {
  const [state, setState] = useState(); // Error!
  return <div>Page</div>;
}

// ✅ Extract to Client Component
'use client';
export function ClientComponent() {
  const [state, setState] = useState();
  return <button onClick={() => setState(1)}>Click</button>;
}

// ❌ Passing non-serializable props from Server to Client
export default async function Page() {
  const data = await fetch('...');
  return <ClientComponent data={data} />; // Error if data has functions!
}

// ✅ Pass serializable data only
export default async function Page() {
  const data = await fetch('...').then(r => r.json());
  return <ClientComponent data={data} />; // ✅ Plain object
}
```

**Timeline Recommendation:**
- **Small app (<20 pages):** 1-2 sprints
- **Medium app (20-100 pages):** 1-3 months
- **Large app (100+ pages):** 3-6 months, incremental migration

---

### Q8: Explain Server Components vs Client Components and their boundaries.

**Answer:**

**Server Components (Default in App Router):**

Components that render **only on the server**. Never sent to the client bundle.

```jsx
// app/page.js - Server Component (default, no directive needed)
export default async function Page() {
  // Can use async/await directly!
  const data = await fetch('https://api.example.com/data');
  const json = await data.json();

  // Can access backend resources directly
  const user = await db.user.findUnique({ where: { id: 1 } });

  return <div>{user.name}: {json.title}</div>;
}
```

**Benefits:**
- **Zero client-side JavaScript** - component code never sent to browser
- **Direct backend access** - databases, file system, environment variables
- **Automatic code splitting** - only client components bundled
- **Better security** - API keys, secrets stay on server
- **Reduced bundle size** - heavy libraries don't bloat client

**Limitations:**
- Cannot use state (`useState`, `useReducer`)
- Cannot use effects (`useEffect`, `useLayoutEffect`)
- Cannot use browser APIs
- Cannot use event handlers (`onClick`, `onChange`)
- Cannot use Context API (`useContext`)
- Cannot use custom hooks that use client-only features

**Client Components:**

Components marked with `'use client'` that render on both server and client.

```jsx
// app/components/Counter.js
'use client'; // Required directive at top of file

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

**When to Use Client Components:**
- Need interactivity (event handlers)
- Need state or effects
- Need browser APIs (localStorage, window, etc.)
- Need custom hooks with client features
- Need Context API
- Need class components (rare)

**The Boundary:**

```jsx
// app/page.js - Server Component
import ClientButton from './ClientButton';

export default async function Page() {
  const data = await fetch('https://api.example.com/data');
  const json = await data.json();

  return (
    <div>
      <h1>{json.title}</h1>  {/* Server rendered */}
      <ClientButton />        {/* Client boundary */}
    </div>
  );
}

// app/ClientButton.js
'use client';

import { useState } from 'react';

export default function ClientButton() {
  const [clicked, setClicked] = useState(false);
  return (
    <button onClick={() => setClicked(true)}>
      {clicked ? 'Clicked!' : 'Click me'}
    </button>
  );
}
```

**Composition Patterns:**

**Pattern 1: Server Component wrapping Client Component**
```jsx
// ✅ Recommended pattern
// app/page.js - Server Component
export default async function Page() {
  const data = await fetchData();
  
  return (
    <ClientWrapper data={data}>
      <ServerChild data={data} />
    </ClientWrapper>
  );
}

// app/ClientWrapper.js
'use client';

export default function ClientWrapper({ children, data }) {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <div>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>
      {isOpen && children} {/* Server Component passed as children! */}
    </div>
  );
}

// app/ServerChild.js - Server Component
export default async function ServerChild({ data }) {
  const moreData = await fetchMoreData(data.id);
  return <div>{moreData.name}</div>;
}
```

**Pattern 2: Pass Server Components as Props**
```jsx
// app/layout.js - Server Component
export default async function Layout({ children }) {
  const user = await getUser();
  
  return (
    <ClientLayout
      sidebar={<ServerSidebar user={user} />}  // Server Component
      header={<ServerHeader user={user} />}    // Server Component
    >
      {children}  // Server Component
    </ClientLayout>
  );
}

// app/ClientLayout.js
'use client';

export default function ClientLayout({ sidebar, header, children }) {
  const [isCollapsed, setIsCollapsed] = useState(false);
  
  return (
    <div>
      {header}
      <div className={isCollapsed ? 'collapsed' : ''}>
        {sidebar}
      </div>
      <main>{children}</main>
    </div>
  );
}
```

**Pattern 3: Data Fetching in Server, Interactivity in Client**
```jsx
// app/products/page.js - Server Component
export default async function ProductsPage() {
  const products = await fetchProducts();
  
  return <ProductList initialProducts={products} />;
}

// app/components/ProductList.js
'use client';

import { useState } from 'react';

export default function ProductList({ initialProducts }) {
  const [products, setProducts] = useState(initialProducts);
  const [filter, setFilter] = useState('');

  const filteredProducts = products.filter(p => 
    p.name.toLowerCase().includes(filter.toLowerCase())
  );

  return (
    <div>
      <input 
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Search..."
      />
      {filteredProducts.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

**Common Mistakes:**

**❌ Mistake 1: Importing Server Component into Client Component**
```jsx
// app/ClientComponent.js
'use client';

import ServerComponent from './ServerComponent'; // ❌ Won't work!

export default function ClientComponent() {
  return <ServerComponent />; // ServerComponent becomes client component!
}
```

**✅ Solution: Pass as children or props**
```jsx
// app/page.js - Server Component
export default function Page() {
  return (
    <ClientComponent>
      <ServerComponent /> {/* ✅ Works! */}
    </ClientComponent>
  );
}
```

**❌ Mistake 2: Using client-only features in Server Component**
```jsx
// app/page.js - Server Component
import { useState } from 'react'; // ❌ Error!

export default async function Page() {
  const [count, setCount] = useState(0);
  return <div>{count}</div>;
}
```

**✅ Solution: Extract to Client Component**
```jsx
// app/page.js - Server Component
export default async function Page() {
  const data = await fetchData();
  return (
    <div>
      <h1>{data.title}</h1>
      <Counter /> {/* Client Component */}
    </div>
  );
}

// app/Counter.js
'use client';
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**❌ Mistake 3: Passing non-serializable props**
```jsx
// app/page.js - Server Component
export default async function Page() {
  const handleClick = () => console.log('clicked'); // Function!
  
  return <ClientButton onClick={handleClick} />; // ❌ Can't serialize functions!
}
```

**✅ Solution: Define handlers in Client Component**
```jsx
// app/page.js - Server Component
export default async function Page() {
  return <ClientButton />; // ✅
}

// app/ClientButton.js
'use client';

export default function ClientButton() {
  const handleClick = () => console.log('clicked'); // ✅ Defined in client
  return <button onClick={handleClick}>Click</button>;
}
```

**Decision Tree:**

```
Need interactivity (onClick, onChange)?
├─ Yes → Client Component
└─ No
    Need state or effects?
    ├─ Yes → Client Component
    └─ No
        Need browser APIs?
        ├─ Yes → Client Component
        └─ No
            Fetching data?
            ├─ Yes → Server Component (preferred)
            └─ No → Server Component (default, smaller bundle)
```

**Performance Implications:**

**Server Component Advantages:**
```jsx
// Server Component - ~0KB JavaScript sent to client
export default async function HeavyChart() {
  const data = await fetchChartData();
  
  // These libraries never sent to client!
  const processedData = expensiveLibrary.process(data);
  
  return <div>{/* Render chart server-side */}</div>;
}
```

**Client Component Costs:**
```jsx
// Client Component - entire component + dependencies sent to client
'use client';

import HeavyChartLibrary from 'heavy-chart-library'; // ~100KB!

export default function HeavyChart() {
  return <HeavyChartLibrary data={data} />;
}
```

**Best Practices:**

1. **Default to Server Components** - only use 'use client' when needed
2. **Push 'use client' down the tree** - keep as much as possible on server
3. **Pass Server Components as children/props** to Client Components
4. **Use Server Components for data fetching**
5. **Extract interactivity to small Client Components**
6. **Be mindful of the client/server boundary**

**Example: Optimized Dashboard**
```jsx
// app/dashboard/page.js - Server Component
export default async function Dashboard() {
  const [user, stats, activity] = await Promise.all([
    fetchUser(),
    fetchStats(),
    fetchActivity()
  ]);

  return (
    <div>
      {/* Heavy, static header - Server Component */}
      <DashboardHeader user={user} stats={stats} />
      
      {/* Interactive filters - Client Component */}
      <ActivityFilters>
        {/* Activity list - Server Component passed as children */}
        <ActivityList activity={activity} />
      </ActivityFilters>
      
      {/* Interactive chart - Client Component */}
      <InteractiveChart data={stats} />
    </div>
  );
}
```

Result: Minimal JavaScript sent to client, optimal performance!

---

### Q9: When should you use Server Actions vs Route Handlers vs API Routes in Next.js?

**Answer:**

**1. Server Actions (Recommended for Mutations)**

Server-side functions that can be called directly from Client Components.

```jsx
// app/actions.js
'use server'; // Marks entire file as server actions

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

export async function createTodo(formData) {
  const title = formData.get('title');
  
  // Direct database access
  const todo = await db.todo.create({
    data: { title, completed: false }
  });

  // Revalidate cache
  revalidatePath('/todos');
  
  return { success: true, todo };
}

export async function deleteTodo(id) {
  await db.todo.delete({ where: { id } });
  revalidatePath('/todos');
  redirect('/todos');
}
```

**Usage in Client Component:**
```jsx
// app/todos/page.js
'use client';

import { createTodo, deleteTodo } from './actions';
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Creating...' : 'Create Todo'}
    </button>
  );
}

export default function TodosPage() {
  return (
    <div>
      {/* Progressive enhancement - works without JS! */}
      <form action={createTodo}>
        <input name="title" required />
        <SubmitButton />
      </form>
      
      {/* Can also call programmatically */}
      <button onClick={async () => {
        const formData = new FormData();
        formData.set('title', 'New Todo');
        await createTodo(formData);
      }}>
        Quick Add
      </button>
    </div>
  );
}
```

**Server Action with Validation:**
```jsx
// app/actions.js
'use server';

import { z } from 'zod';

const TodoSchema = z.object({
  title: z.string().min(3).max(100),
  priority: z.enum(['low', 'medium', 'high'])
});

export async function createTodo(formData) {
  // Validate input
  const validatedFields = TodoSchema.safeParse({
    title: formData.get('title'),
    priority: formData.get('priority')
  });

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
    };
  }

  const { title, priority } = validatedFields.data;

  try {
    const todo = await db.todo.create({
      data: { title, priority, completed: false }
    });

    revalidatePath('/todos');
    return { success: true, todo };
  } catch (error) {
    return { error: 'Failed to create todo' };
  }
}
```

**2. Route Handlers (API Routes in App Router)**

RESTful API endpoints for external requests or client-side fetch calls.

```jsx
// app/api/todos/route.js
import { NextResponse } from 'next/server';

// GET /api/todos
export async function GET(request) {
  const { searchParams } = new URL(request.url);
  const limit = searchParams.get('limit') || 10;

  const todos = await db.todo.findMany({
    take: parseInt(limit),
    orderBy: { createdAt: 'desc' }
  });

  return NextResponse.json(todos);
}

// POST /api/todos
export async function POST(request) {
  const body = await request.json();
  
  const todo = await db.todo.create({
    data: { title: body.title, completed: false }
  });

  return NextResponse.json(todo, { status: 201 });
}

// app/api/todos/[id]/route.js
export async function GET(request, { params }) {
  const todo = await db.todo.findUnique({
    where: { id: params.id }
  });

  if (!todo) {
    return NextResponse.json(
      { error: 'Todo not found' },
      { status: 404 }
    );
  }

  return NextResponse.json(todo);
}

export async function DELETE(request, { params }) {
  await db.todo.delete({ where: { id: params.id } });
  return NextResponse.json({ success: true });
}
```

**Route Handler with Authentication:**
```jsx
// app/api/user/profile/route.js
import { auth } from '@/lib/auth';
import { NextResponse } from 'next/server';

export async function GET(request) {
  const session = await auth(request);

  if (!session) {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 401 }
    );
  }

  const user = await db.user.findUnique({
    where: { id: session.userId },
    include: { posts: true, comments: true }
  });

  return NextResponse.json(user);
}

export async function PATCH(request) {
  const session = await auth(request);
  if (!session) {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 401 }
    );
  }

  const body = await request.json();
  
  const user = await db.user.update({
    where: { id: session.userId },
    data: body
  });

  return NextResponse.json(user);
}
```

**3. API Routes (Pages Router)**

Traditional API routes in Pages Router.

```jsx
// pages/api/todos.js
export default async function handler(req, res) {
  if (req.method === 'GET') {
    const todos = await db.todo.findMany();
    res.status(200).json(todos);
  } else if (req.method === 'POST') {
    const { title } = req.body;
    const todo = await db.todo.create({
      data: { title, completed: false }
    });
    res.status(201).json(todo);
  } else {
    res.status(405).json({ error: 'Method not allowed' });
  }
}

// pages/api/todos/[id].js
export default async function handler(req, res) {
  const { id } = req.query;

  switch (req.method) {
    case 'GET':
      const todo = await db.todo.findUnique({ where: { id } });
      if (!todo) return res.status(404).json({ error: 'Not found' });
      return res.status(200).json(todo);

    case 'DELETE':
      await db.todo.delete({ where: { id } });
      return res.status(204).end();

    default:
      return res.status(405).json({ error: 'Method not allowed' });
  }
}
```

**Comparison Table:**

| Feature | Server Actions | Route Handlers | API Routes (Pages) |
|---------|---------------|----------------|-------------------|
| **Location** | Any file with 'use server' | app/api/*/route.js | pages/api/*.js |
| **Primary Use** | Form submissions, mutations | RESTful APIs, external clients | RESTful APIs (legacy) |
| **Return Type** | Any serializable data | Response object | res object |
| **Progressive Enhancement** | ✅ Works without JS | ❌ Requires JS | ❌ Requires JS |
| **Revalidation** | Built-in (revalidatePath) | Manual | Manual |
| **Type Safety** | ✅ Full TypeScript | ✅ Full TypeScript | ⚠️ Limited |
| **Caching** | Automatic | Manual control | Manual control |
| **Best For** | Forms, mutations, optimistic UI | External APIs, webhooks, REST | Legacy Pages Router |

**When to Use Each:**

**Use Server Actions when:**
- Handling form submissions
- Performing mutations (create, update, delete)
- Need progressive enhancement (works without JS)
- Working within React component tree
- Want automatic revalidation
- Building internal app features

**Use Route Handlers when:**
- Building external-facing API
- Need RESTful API structure
- Handling webhooks
- Accepting requests from mobile apps
- Need fine-grained control over responses
- Implementing OAuth callbacks
- Generating dynamic files (CSV, PDF)

**Use API Routes (Pages Router) when:**
- Working in Pages Router (legacy)
- Migrating from older Next.js versions
- Not yet ready to adopt App Router

**Real-World Examples:**

**Example 1: Blog Post Creation**

**With Server Action:**
```jsx
// app/actions/posts.js
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

export async function createPost(formData) {
  const post = await db.post.create({
    data: {
      title: formData.get('title'),
      content: formData.get('content'),
      authorId: formData.get('authorId')
    }
  });

  revalidatePath('/blog');
  redirect(`/blog/${post.slug}`);
}

// app/blog/new/page.js
'use client';

import { createPost } from '@/app/actions/posts';

export default function NewPost() {
  return (
    <form action={createPost}>
      <input name="title" required />
      <textarea name="content" required />
      <button type="submit">Publish</button>
    </form>
  );
}
```

**With Route Handler:**
```jsx
// app/api/posts/route.js
export async function POST(request) {
  const { title, content, authorId } = await request.json();
  
  const post = await db.post.create({
    data: { title, content, authorId }
  });

  return NextResponse.json(post, { status: 201 });
}

// Client-side usage
async function createPost(data) {
  const response = await fetch('/api/posts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data)
  });
  return response.json();
}
```

**Example 2: Stripe Webhook**

**Route Handler (Correct Choice):**
```jsx
// app/api/webhooks/stripe/route.js
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

export async function POST(request) {
  const body = await request.text();
  const signature = request.headers.get('stripe-signature');

  let event;
  try {
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET
    );
  } catch (err) {
    return NextResponse.json(
      { error: 'Invalid signature' },
      { status: 400 }
    );
  }

  // Handle different event types
  switch (event.type) {
    case 'payment_intent.succeeded':
      await handlePaymentSuccess(event.data.object);
      break;
    case 'payment_intent.failed':
      await handlePaymentFailure(event.data.object);
      break;
  }

  return NextResponse.json({ received: true });
}
```

**Example 3: File Upload**

**Route Handler (Correct Choice):**
```jsx
// app/api/upload/route.js
import { writeFile } from 'fs/promises';
import { join } from 'path';

export async function POST(request) {
  const formData = await request.formData();
  const file = formData.get('file');

  if (!file) {
    return NextResponse.json(
      { error: 'No file provided' },
      { status: 400 }
    );
  }

  const bytes = await file.arrayBuffer();
  const buffer = Buffer.from(bytes);

  const path = join(process.cwd(), 'public', 'uploads', file.name);
  await writeFile(path, buffer);

  return NextResponse.json({
    success: true,
    url: `/uploads/${file.name}`
  });
}
```

**Best Practice: Combine Both!**
```jsx
// Server Action calls Route Handler for complex logic
'use server';

export async function uploadAvatar(formData) {
  const file = formData.get('avatar');
  
  // Upload via Route Handler
  const uploadFormData = new FormData();
  uploadFormData.set('file', file);
  
  const response = await fetch('http://localhost:3000/api/upload', {
    method: 'POST',
    body: uploadFormData
  });

  const { url } = await response.json();

  // Update user with new avatar URL
  await db.user.update({
    where: { id: formData.get('userId') },
    data: { avatar: url }
  });

  revalidatePath('/profile');
}
```

---

### Q10: Explain Next.js caching layers and strategies.

**Answer:**

Next.js has **four different cache mechanisms** that work together:

**1. Request Memoization (React Feature)**

Deduplicates identical requests during a single render pass.

```jsx
// These two fetches in the same render are deduplicated
// Only ONE network request is made!
async function ComponentA() {
  const data = await fetch('https://api.example.com/data');
  // ...
}

async function ComponentB() {
  const data = await fetch('https://api.example.com/data'); // Memoized!
  // ...
}

// Scope: Single render pass
// Duration: Until render completes
// Location: Server (React)
```

**How it works:**
```jsx
// app/page.js
async function Page() {
  return (
    <div>
      <UserProfile />  {/* Fetches user data */}
      <UserStats />    {/* Fetches same user data - memoized! */}
      <UserPosts />    {/* Fetches same user data - memoized! */}
    </div>
  );
}

// Only ONE fetch executed, all components get same data
```

**Opt-out:**
```jsx
// Add cache: 'no-store' or unique signal
fetch('https://api.example.com/data', { 
  cache: 'no-store' 
});

// Or use AbortController with unique signal
const controller = new AbortController();
fetch('https://api.example.com/data', { 
  signal: controller.signal 
});
```

**2. Data Cache (Next.js Feature)**

Persists fetch results across requests and deployments.

```jsx
// ✅ Cached by default - persists across requests
async function getData() {
  const res = await fetch('https://api.example.com/data');
  return res.json();
}

// Force no-cache
async function getRealtimeData() {
  const res = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Always fetch fresh data
  });
  return res.json();
}

// Time-based revalidation (ISR)
async function getDataWithISR() {
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // Revalidate every hour
  });
  return res.json();
}

// Tag-based revalidation
async function getProducts() {
  const res = await fetch('https://api.example.com/products', {
    next: { tags: ['products'] } // Tag this request
  });
  return res.json();
}
```

**Revalidation strategies:**
```jsx
// Time-based - automatic after specified seconds
{ next: { revalidate: 60 } } // Revalidate after 60 seconds

// On-demand - manual revalidation
import { revalidatePath, revalidateTag } from 'next/cache';

// Revalidate specific path
export async function updateProduct() {
  'use server';
  await db.product.update(/*...*/);
  revalidatePath('/products'); // Revalidate all data for this path
}

// Revalidate by tag
export async function updateProduct() {
  'use server';
  await db.product.update(/*...*/);
  revalidateTag('products'); // Revalidate all fetches tagged 'products'
}

// Opt-out completely
{ cache: 'no-store' } // Never cache
```

**3. Full Route Cache**

Caches entire rendered routes (HTML + RSC payload) at build time or on first request.

```jsx
// app/blog/[slug]/page.js

// Static generation - cached at build time
export default async function BlogPost({ params }) {
  const post = await getPost(params.slug);
  return <article>{post.content}</article>;
}

// Generate static paths at build time
export async function generateStaticParams() {
  const posts = await getAllPosts();
  return posts.map(post => ({ slug: post.slug }));
}

// Force dynamic rendering (not cached)
export const dynamic = 'force-dynamic';

// Or use dynamic functions that opt out of caching
export default async function BlogPost({ params }) {
  const headers = await headers(); // Dynamic function - no cache
  const post = await getPost(params.slug);
  return <article>{post.content}</article>;
}
```

**Rendering strategies:**
```jsx
// Static (default) - cached at build time
export default async function Page() {
  const data = await fetch('https://api.example.com/data');
  return <div>{data}</div>;
}

// Dynamic - rendered on each request
export const dynamic = 'force-dynamic';

// ISR - static with revalidation
export const revalidate = 60; // Revalidate every 60 seconds

// Partial prerendering (experimental)
export const experimental_ppr = true;
```

**4. Router Cache (Client-side)**

Client-side cache of visited routes in browser memory.

```jsx
// When user navigates:
<Link href="/about">About</Link>

// First visit: Server fetches and caches on client
// Subsequent visits: Served from client cache (instant!)

// Duration:
// - Static routes: 5 minutes (or until refresh)
// - Dynamic routes: 30 seconds

// Manual invalidation:
import { useRouter } from 'next/navigation';

const router = useRouter();
router.refresh(); // Clear router cache and refetch
```

**Cache Interaction Example:**

```jsx
// app/products/page.js
export const revalidate = 3600; // Full Route Cache: 1 hour

export default async function ProductsPage() {
  // Data Cache: Cached with same revalidation time
  const products = await fetch('https://api.example.com/products', {
    next: { 
      revalidate: 3600,
      tags: ['products']
    }
  });

  return (
    <div>
      {products.map(product => (
        // Router Cache: Cached on client after first visit
        <Link key={product.id} href={`/products/${product.id}`}>
          {product.name}
        </Link>
      ))}
    </div>
  );
}

// Invalidation
export async function updateProduct(id) {
  'use server';
  
  await db.product.update({ where: { id }, data: {/*...*/} });
  
  // Invalidate Data Cache
  revalidateTag('products');
  
  // Invalidate Full Route Cache
  revalidatePath('/products');
  
  // Router Cache clears automatically on navigation
}
```

**Summary Table:**

| Cache Layer | Location | Duration | Invalidation | Purpose |
|-------------|----------|----------|--------------|---------|
| **Request Memoization** | Server | Single render | Automatic | Dedupe identical requests |
| **Data Cache** | Server | Persistent | revalidateTag, revalidatePath, time | Cache fetch responses |
| **Full Route Cache** | Server | Persistent (static) or request (dynamic) | revalidatePath, revalidate, rebuild | Cache rendered routes |
| **Router Cache** | Client | Session or time | router.refresh, next revalidation | Instant navigation |

**Caching Decision Tree:**

```
Is data user-specific or request-specific?
├─ Yes → cache: 'no-store' OR export const dynamic = 'force-dynamic'
└─ No
    Can data be static?
    ├─ Yes
    │   How often does it change?
    │   ├─ Never/rarely → Static (default)
    │   ├─ Predictably → ISR (export const revalidate = seconds)
    │   └─ Unpredictably → ISR with tags (next: { tags: [...] }) + revalidateTag
    └─ No → Dynamic rendering (export const dynamic = 'force-dynamic')
```

**Real-World Scenarios:**

**Scenario 1: Blog with Comments**
```jsx
// app/blog/[slug]/page.js
export const revalidate = 3600; // Post content: 1 hour

export default async function BlogPost({ params }) {
  // Post content - cached for 1 hour
  const post = await fetch(`https://api.example.com/posts/${params.slug}`, {
    next: { revalidate: 3600 }
  });

  // Comments - fresh every time
  const comments = await fetch(`https://api.example.com/posts/${params.slug}/comments`, {
    cache: 'no-store'
  });

  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
      <Comments data={comments} />
    </article>
  );
}
```

**Scenario 2: E-commerce Product Page**
```jsx
// app/products/[id]/page.js
export default async function ProductPage({ params }) {
  // Product info - ISR with tags
  const product = await fetch(`https://api.example.com/products/${params.id}`, {
    next: { 
      revalidate: 3600,
      tags: [`product-${params.id}`]
    }
  });

  // Stock - always fresh
  const stock = await fetch(`https://api.example.com/products/${params.id}/stock`, {
    cache: 'no-store'
  });

  // Reviews - moderate freshness
  const reviews = await fetch(`https://api.example.com/products/${params.id}/reviews`, {
    next: { revalidate: 300 } // 5 minutes
  });

  return <ProductDetails product={product} stock={stock} reviews={reviews} />;
}

// When product is updated
export async function updateProduct(id, data) {
  'use server';
  await db.product.update({ where: { id }, data });
  revalidateTag(`product-${id}`); // Invalidate this specific product
  revalidatePath('/products'); // Invalidate product list
}
```

**Scenario 3: User Dashboard**
```jsx
// app/dashboard/page.js
export const dynamic = 'force-dynamic'; // Always fresh, user-specific

export default async function Dashboard() {
  const user = await getUser(); // Session-based, never cache
  
  // Even with dynamic, can cache specific parts
  const stats = await fetch('https://api.example.com/stats', {
    next: { revalidate: 60 }
  });

  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      <Stats data={stats} />
    </div>
  );
}
```

**Performance Optimization Patterns:**

**Pattern 1: Parallel Data Fetching**
```jsx
// ✅ Fetch in parallel
export default async function Page() {
  const [user, posts, comments] = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments()
  ]);
  
  return <Dashboard user={user} posts={posts} comments={comments} />;
}
```

**Pattern 2: Sequential with Suspense**
```jsx
// Fetch critical data first, defer secondary data
export default async function Page() {
  const user = await fetchUser(); // Critical
  
  return (
    <div>
      <h1>Welcome {user.name}</h1>
      <Suspense fallback={<Loading />}>
        <SecondaryData userId={user.id} /> {/* Deferred */}
      </Suspense>
    </div>
  );
}

async function SecondaryData({ userId }) {
  const data = await fetchSecondaryData(userId);
  return <div>{data}</div>;
}
```

**Pattern 3: Aggressive Caching with Smart Invalidation**
```jsx
// Cache aggressively
export const revalidate = 86400; // 24 hours

// Invalidate precisely
export async function createPost(formData) {
  'use server';
  const post = await db.post.create({ data: {/*...*/} });
  revalidateTag('posts'); // Only invalidate posts, not everything
  revalidatePath('/blog'); // Invalidate blog page
  redirect(`/blog/${post.slug}`);
}
```

**Debugging Caches:**

```jsx
// Add custom headers to see cache status
export async function GET(request) {
  const data = await fetchData();
  
  return NextResponse.json(data, {
    headers: {
      'X-Cache-Status': 'HIT', // or 'MISS'
      'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=30'
    }
  });
}

// In development, check Next.js logs
// ○ (Static) - Rendered at build time
// ƒ (Dynamic) - Rendered on demand
// ● (SSG) - Static Site Generation
```

---

## State Management Architecture

### Q11: Compare different state management approaches: when to use global vs server vs URL state.

**Answer:**

**State Classification:**

Modern React apps have **three distinct types of state**, each requiring different management strategies:

**1. Server State (Remote)**
Data that lives on the server, cached on the client.

**Examples:**
- User data
- Products list
- Blog posts
- API responses
- Database records

**Characteristics:**
- Asynchronous
- Can be outdated (stale)
- Not owned by frontend
- Shared across users
- Needs caching, refetching, invalidation

**❌ Don't Use Redux/Zustand:**
```jsx
// ❌ Anti-pattern - treating server state as client state
const [users, setUsers] = useState([]);
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);

useEffect(() => {
  setLoading(true);
  fetch('/api/users')
    .then(r => r.json())
    .then(setUsers)
    .catch(setError)
    .finally(() => setLoading(false));
}, []);

// Problems:
// - No caching
// - No automatic refetching
// - No optimistic updates
// - Manual loading/error states
// - Stale data issues
```

**✅ Use React Query/TanStack Query:**
```jsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

function UsersList() {
  // Automatic caching, refetching, loading states
  const { data: users, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
    staleTime: 5000, // Consider fresh for 5 seconds
    cacheTime: 10 * 60 * 1000, // Keep in cache for 10 minutes
    refetchOnWindowFocus: true, // Refetch when user returns to tab
  });

  const queryClient = useQueryClient();

  // Mutations with automatic invalidation
  const mutation = useMutation({
    mutationFn: createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });

  if (isLoading) return <Loading />;
  if (error) return <Error error={error} />;

  return (
    <div>
      {users.map(user => <UserCard key={user.id} user={user} />)}
      <button onClick={() => mutation.mutate({ name: 'New User' })}>
        Add User
      </button>
    </div>
  );
}
```

**Advanced Server State Patterns:**
```jsx
// Optimistic updates
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: async (newUser) => {
    // Cancel outgoing refetches
    await queryClient.cancelQueries({ queryKey: ['users', newUser.id] });

    // Snapshot previous value
    const previousUser = queryClient.getQueryData(['users', newUser.id]);

    // Optimistically update
    queryClient.setQueryData(['users', newUser.id], newUser);

    return { previousUser };
  },
  onError: (err, newUser, context) => {
    // Rollback on error
    queryClient.setQueryData(
      ['users', newUser.id],
      context.previousUser
    );
  },
  onSettled: (newUser) => {
    queryClient.invalidateQueries({ queryKey: ['users', newUser.id] });
  },
});

// Pagination
function UsersList() {
  const [page, setPage] = useState(1);

  const { data, isLoading } = useQuery({
    queryKey: ['users', page],
    queryFn: () => fetchUsers({ page }),
    keepPreviousData: true, // Keep old data while fetching new page
  });

  // Prefetch next page
  const queryClient = useQueryClient();
  useEffect(() => {
    if (data?.hasMore) {
      queryClient.prefetchQuery({
        queryKey: ['users', page + 1],
        queryFn: () => fetchUsers({ page: page + 1 }),
      });
    }
  }, [data, page, queryClient]);

  return (/*...*/);
}

// Infinite scroll
function InfiniteUsersList() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
  } = useInfiniteQuery({
    queryKey: ['users'],
    queryFn: ({ pageParam = 1 }) => fetchUsers({ page: pageParam }),
    getNextPageParam: (lastPage, pages) => lastPage.nextPage,
  });

  return (
    <div>
      {data.pages.map((page, i) => (
        <React.Fragment key={i}>
          {page.users.map(user => <UserCard key={user.id} user={user} />)}
        </React.Fragment>
      ))}
      <button
        onClick={() => fetchNextPage()}
        disabled={!hasNextPage || isFetchingNextPage}
      >
        Load More
      </button>
    </div>
  );
}
```

**2. URL State**
State stored in the URL (path, query params, hash).

**Examples:**
- Current page/route
- Search filters
- Sort order
- Selected tab
- Pagination
- Modal state

**Benefits:**
- Shareable links
- Browser history (back/forward)
- Bookmarkable state
- SEO friendly
- No need for separate state management

**✅ Use URL for shareable state:**
```jsx
// Using Next.js App Router
import { useRouter, useSearchParams } from 'next/navigation';

function ProductsList() {
  const router = useRouter();
  const searchParams = useSearchParams();

  const search = searchParams.get('search') || '';
  const category = searchParams.get('category') || 'all';
  const sort = searchParams.get('sort') || 'name';
  const page = parseInt(searchParams.get('page') || '1');

  const updateFilters = (updates) => {
    const params = new URLSearchParams(searchParams);
    Object.entries(updates).forEach(([key, value]) => {
      if (value) {
        params.set(key, value);
      } else {
        params.delete(key);
      }
    });
    router.push(`?${params.toString()}`);
  };

  return (
    <div>
      <input
        value={search}
        onChange={(e) => updateFilters({ search: e.target.value, page: 1 })}
      />
      <select
        value={category}
        onChange={(e) => updateFilters({ category: e.target.value, page: 1 })}
      >
        <option value="all">All</option>
        <option value="electronics">Electronics</option>
      </select>
      <Pagination
        page={page}
        onPageChange={(newPage) => updateFilters({ page: newPage })}
      />
    </div>
  );
}

// Custom hook for URL state
function useQueryState(key, defaultValue) {
  const router = useRouter();
  const searchParams = useSearchParams();

  const value = searchParams.get(key) || defaultValue;

  const setValue = (newValue) => {
    const params = new URLSearchParams(searchParams);
    if (newValue) {
      params.set(key, newValue);
    } else {
      params.delete(key);
    }
    router.push(`?${params.toString()}`);
  };

  return [value, setValue];
}

// Usage
function SearchPage() {
  const [search, setSearch] = useQueryState('q', '');
  const [filter, setFilter] = useQueryState('filter', 'all');

  return (
    <div>
      <input value={search} onChange={(e) => setSearch(e.target.value)} />
      <select value={filter} onChange={(e) => setFilter(e.target.value)}>
        {/*...*/}
      </select>
    </div>
  );
}
```

**URL State with Type Safety:**
```tsx
import { z } from 'zod';

// Define schema for URL params
const SearchParamsSchema = z.object({
  search: z.string().optional(),
  category: z.enum(['electronics', 'clothing', 'books']).optional(),
  minPrice: z.coerce.number().positive().optional(),
  maxPrice: z.coerce.number().positive().optional(),
  sort: z.enum(['name', 'price', 'date']).default('name'),
  page: z.coerce.number().positive().default(1),
});

type SearchParams = z.infer<typeof SearchParamsSchema>;

function ProductsPage({ searchParams }: { searchParams: Record<string, string> }) {
  // Validate and parse URL params
  const params = SearchParamsSchema.parse(searchParams);

  return <ProductsList filters={params} />;
}
```

**3. Client State (UI State)**
Local application state, not persisted, not shared.

**Examples:**
- Form inputs
- Modal open/closed
- Selected items
- UI toggle states
- Hover states
- Animation states

**Options:**

**A. Local State (useState) - Preferred for component-local state:**
```jsx
function TodoItem({ todo }) {
  const [isEditing, setIsEditing] = useState(false);
  const [hovered, setHovered] = useState(false);

  return (
    <div
      onMouseEnter={() => setHovered(true)}
      onMouseLeave={() => setHovered(false)}
    >
      {isEditing ? (
        <input value={todo.title} />
      ) : (
        <span>{todo.title}</span>
      )}
      {hovered && <button onClick={() => setIsEditing(true)}>Edit</button>}
    </div>
  );
}
```

**B. Global State (Zustand/Jotai) - For shared UI state:**
```jsx
import create from 'zustand';

// Zustand store
const useUIStore = create((set) => ({
  sidebarOpen: true,
  theme: 'light',
  modals: {},
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
  setTheme: (theme) => set({ theme }),
  openModal: (id) => set((state) => ({
    modals: { ...state.modals, [id]: true }
  })),
  closeModal: (id) => set((state) => ({
    modals: { ...state.modals, [id]: false }
  })),
}));

// Usage
function Sidebar() {
  const { sidebarOpen, toggleSidebar } = useUIStore();
  return (
    <aside className={sidebarOpen ? 'open' : 'closed'}>
      <button onClick={toggleSidebar}>Toggle</button>
    </aside>
  );
}

function Header() {
  const { sidebarOpen, toggleSidebar } = useUIStore();
  return (
    <header>
      <button onClick={toggleSidebar}>
        {sidebarOpen ? 'Close' : 'Open'} Menu
      </button>
    </header>
  );
}
```

**C. Atomic State (Jotai) - For granular state:**
```jsx
import { atom, useAtom } from 'jotai';

// Define atoms
const sidebarOpenAtom = atom(true);
const themeAtom = atom('light');
const searchAtom = atom('');

// Derived atoms
const filteredProductsAtom = atom((get) => {
  const search = get(searchAtom);
  return products.filter(p => p.name.includes(search));
});

// Usage
function SearchBar() {
  const [search, setSearch] = useAtom(searchAtom);
  return <input value={search} onChange={(e) => setSearch(e.target.value)} />;
}

function ProductsList() {
  const [products] = useAtom(filteredProductsAtom); // Only re-renders on filtered change!
  return products.map(p => <ProductCard key={p.id} product={p} />);
}
```

**Decision Matrix:**

| State Type | Where to Store | Tools | When to Use |
|------------|---------------|-------|-------------|
| **Server State** | React Query cache | React Query, SWR | API data, database records |
| **URL State** | URL params/hash | useSearchParams, router | Shareable, bookmarkable state |
| **Local UI State** | useState | useState, useReducer | Component-specific state |
| **Shared UI State** | Global store | Zustand, Jotai, Context | Cross-component UI state |
| **Form State** | Form library | React Hook Form, Formik | Complex forms |
| **Persistent Local** | LocalStorage + state | localStorage + useState | User preferences |

**Real-World Architecture:**

```jsx
// app/products/page.js
export default function ProductsPage({ searchParams }) {
  // URL state - filters
  const search = searchParams.search || '';
  const category = searchParams.category || 'all';
  const page = parseInt(searchParams.page || '1');

  // Server state - products from API
  const { data: products, isLoading } = useQuery({
    queryKey: ['products', { search, category, page }],
    queryFn: () => fetchProducts({ search, category, page }),
  });

  // Local UI state - view mode
  const [viewMode, setViewMode] = useState('grid');

  // Global UI state - cart
  const { items, addToCart } = useCartStore();

  return (
    <div>
      <SearchBar defaultSearch={search} />
      <CategoryFilter defaultCategory={category} />
      <ViewToggle mode={viewMode} onChange={setViewMode} />
      <ProductGrid
        products={products}
        viewMode={viewMode}
        onAddToCart={addToCart}
      />
      <Pagination page={page} />
      <Cart items={items} />
    </div>
  );
}
```

**Anti-Patterns to Avoid:**

```jsx
// ❌ Storing server state in Redux/Zustand
const store = create((set) => ({
  users: [],
  loading: false,
  fetchUsers: async () => {
    set({ loading: true });
    const users = await fetchUsers();
    set({ users, loading: false });
  }
}));

// ✅ Use React Query instead
const { data: users, isLoading } = useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers
});

// ❌ Storing URL state in useState
const [page, setPage] = useState(1);
const [search, setSearch] = useState('');

// ✅ Use URL params
const searchParams = useSearchParams();
const page = searchParams.get('page');
const search = searchParams.get('search');

// ❌ Using global state for everything
const store = create((set) => ({
  modalOpen: false,
  hoveredItem: null,
  inputValue: '',
  // ... everything in global state
}));

// ✅ Use local state for component-specific state
function Component() {
  const [modalOpen, setModalOpen] = useState(false);
  const [hoveredItem, setHoveredItem] = useState(null);
  // ...
}
```

**Best Practices:**

1. **Server state → React Query** (data from API)
2. **URL state → Search params** (shareable filters, pagination)
3. **Local UI state → useState** (component-specific)
4. **Shared UI state → Zustand/Jotai** (theme, modals, sidebar)
5. **Form state → React Hook Form** (complex forms)
6. **Persistent state → localStorage + state** (user preferences)

---

## Whiteboarding Scenarios - System Design

### Scenario 1: Real-Time Notification System

**Problem Statement:**
Design a scalable real-time notification system for a web application that supports 100k+ concurrent users. The system should handle various notification types (messages, alerts, updates) with proper delivery guarantees and user preferences.

**Requirements:**
- Real-time delivery (< 1 second latency)
- Support for multiple notification channels (in-app, email, push)
- User preferences (mute, do-not-disturb, channel preferences)
- Notification history and read/unread status
- Scalability for 100k+ concurrent users
- Offline support (queue notifications when offline)

---

#### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        Frontend Layer                        │
├──────────────┬──────────────┬──────────────┬────────────────┤
│   React UI   │  WebSocket   │  Service     │  IndexedDB     │
│  Components  │   Client     │   Worker     │   (Offline)    │
└──────┬───────┴──────┬───────┴──────┬───────┴────────┬───────┘
       │              │               │                │
       │              │               │                │
       ▼              ▼               ▼                ▼
┌─────────────────────────────────────────────────────────────┐
│                         API Gateway                          │
│                    (WebSocket + REST)                        │
└──────────────────────────┬──────────────────────────────────┘
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼
┌─────────────┐  ┌──────────────────┐  ┌──────────────┐
│  WebSocket  │  │  Notification    │  │  User Prefs  │
│   Server    │  │   Service        │  │   Service    │
│  (Socket.io)│  │  (Pub/Sub)       │  │              │
└──────┬──────┘  └────────┬─────────┘  └──────┬───────┘
       │                  │                    │
       │                  │                    │
       ▼                  ▼                    ▼
┌─────────────┐  ┌──────────────────┐  ┌──────────────┐
│   Redis     │  │   Message Queue  │  │  PostgreSQL  │
│  (Session)  │  │   (RabbitMQ)     │  │   (Prefs)    │
└─────────────┘  └──────────────────┘  └──────────────┘
```

---

#### Implementation

**1. Frontend - React WebSocket Hook**

```typescript
// hooks/useNotifications.ts
import { useEffect, useState, useCallback, useRef } from 'react';
import { io, Socket } from 'socket.io-client';

export interface Notification {
  id: string;
  type: 'message' | 'alert' | 'update';
  title: string;
  body: string;
  priority: 'low' | 'medium' | 'high';
  timestamp: string;
  read: boolean;
  actionUrl?: string;
  metadata?: Record<string, any>;
}

interface UseNotificationsOptions {
  userId: string;
  onNotification?: (notification: Notification) => void;
  reconnectAttempts?: number;
  reconnectDelay?: number;
}

export function useNotifications({
  userId,
  onNotification,
  reconnectAttempts = 5,
  reconnectDelay = 3000
}: UseNotificationsOptions) {
  const [notifications, setNotifications] = useState<Notification[]>([]);
  const [unreadCount, setUnreadCount] = useState(0);
  const [connected, setConnected] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const socketRef = useRef<Socket | null>(null);
  const reconnectAttemptsRef = useRef(0);
  const queuedNotificationsRef = useRef<Notification[]>([]);

  // Initialize WebSocket connection
  useEffect(() => {
    if (!userId) return;

    const socket = io(process.env.NEXT_PUBLIC_WS_URL!, {
      auth: { userId },
      transports: ['websocket', 'polling'],
      reconnection: true,
      reconnectionAttempts,
      reconnectionDelay
    });

    socketRef.current = socket;

    // Connection events
    socket.on('connect', () => {
      console.log('WebSocket connected');
      setConnected(true);
      setError(null);
      reconnectAttemptsRef.current = 0;

      // Send queued notifications from offline mode
      if (queuedNotificationsRef.current.length > 0) {
        queuedNotificationsRef.current.forEach(notification => {
          socket.emit('notification:ack', { notificationId: notification.id });
        });
        queuedNotificationsRef.current = [];
      }
    });

    socket.on('disconnect', (reason) => {
      console.log('WebSocket disconnected:', reason);
      setConnected(false);
      
      if (reason === 'io server disconnect') {
        // Server initiated disconnect - try to reconnect
        socket.connect();
      }
    });

    socket.on('connect_error', (err) => {
      console.error('Connection error:', err);
      reconnectAttemptsRef.current += 1;
      
      if (reconnectAttemptsRef.current >= reconnectAttempts) {
        setError('Failed to connect to notification service');
      }
    });

    // Notification events
    socket.on('notification', (notification: Notification) => {
      handleNewNotification(notification);
    });

    socket.on('notification:read', (notificationId: string) => {
      markAsRead(notificationId);
    });

    socket.on('notification:read_all', () => {
      markAllAsRead();
    });

    return () => {
      socket.disconnect();
    };
  }, [userId, reconnectAttempts, reconnectDelay]);

  const handleNewNotification = useCallback((notification: Notification) => {
    // Add to state
    setNotifications(prev => [notification, ...prev]);
    setUnreadCount(prev => prev + 1);

    // Store in IndexedDB for offline support
    if ('indexedDB' in window) {
      storeNotificationOffline(notification);
    }

    // Queue if offline
    if (!socketRef.current?.connected) {
      queuedNotificationsRef.current.push(notification);
    } else {
      // Send acknowledgment
      socketRef.current?.emit('notification:ack', {
        notificationId: notification.id
      });
    }

    // Call custom handler
    onNotification?.(notification);

    // Show browser notification if permitted
    if (notification.priority === 'high' && 'Notification' in window) {
      if (Notification.permission === 'granted') {
        new Notification(notification.title, {
          body: notification.body,
          icon: '/notification-icon.png',
          tag: notification.id
        });
      }
    }

    // Play sound for high priority
    if (notification.priority === 'high') {
      playNotificationSound();
    }
  }, [onNotification]);

  const markAsRead = useCallback((notificationId: string) => {
    setNotifications(prev =>
      prev.map(n => n.id === notificationId ? { ...n, read: true } : n)
    );
    setUnreadCount(prev => Math.max(0, prev - 1));

    socketRef.current?.emit('notification:mark_read', { notificationId });
  }, []);

  const markAllAsRead = useCallback(() => {
    setNotifications(prev => prev.map(n => ({ ...n, read: true })));
    setUnreadCount(0);

    socketRef.current?.emit('notification:mark_all_read');
  }, []);

  const deleteNotification = useCallback((notificationId: string) => {
    setNotifications(prev => prev.filter(n => n.id !== notificationId));
    
    socketRef.current?.emit('notification:delete', { notificationId });
  }, []);

  const clearAll = useCallback(() => {
    setNotifications([]);
    setUnreadCount(0);

    socketRef.current?.emit('notification:clear_all');
  }, []);

  return {
    notifications,
    unreadCount,
    connected,
    error,
    markAsRead,
    markAllAsRead,
    deleteNotification,
    clearAll
  };
}

// Helper functions
async function storeNotificationOffline(notification: Notification) {
  const db = await openDB();
  const tx = db.transaction('notifications', 'readwrite');
  await tx.store.add(notification);
}

function playNotificationSound() {
  const audio = new Audio('/notification-sound.mp3');
  audio.volume = 0.5;
  audio.play().catch(console.error);
}

async function openDB(): Promise<IDBDatabase> {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open('NotificationsDB', 1);
    
    request.onerror = () => reject(request.error);
    request.onsuccess = () => resolve(request.result);
    
    request.onupgradeneeded = (event) => {
      const db = (event.target as IDBOpenDBRequest).result;
      if (!db.objectStoreNames.contains('notifications')) {
        db.createObjectStore('notifications', { keyPath: 'id' });
      }
    };
  });
}
```

**2. Notification UI Component**

```typescript
// components/NotificationCenter.tsx
import { useState } from 'react';
import { useNotifications } from '@/hooks/useNotifications';
import { formatDistanceToNow } from 'date-fns';

export function NotificationCenter({ userId }: { userId: string }) {
  const [isOpen, setIsOpen] = useState(false);
  const [filter, setFilter] = useState<'all' | 'unread'>('all');
  
  const {
    notifications,
    unreadCount,
    connected,
    error,
    markAsRead,
    markAllAsRead,
    deleteNotification,
    clearAll
  } = useNotifications({
    userId,
    onNotification: (notification) => {
      // Show toast for high priority
      if (notification.priority === 'high') {
        toast.info(notification.title);
      }
    }
  });

  const filteredNotifications = filter === 'unread'
    ? notifications.filter(n => !n.read)
    : notifications;

  return (
    <div className="relative">
      {/* Bell Icon with Badge */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="relative p-2 rounded-full hover:bg-gray-100"
        aria-label="Notifications"
      >
        <BellIcon className="w-6 h-6" />
        {unreadCount > 0 && (
          <span className="absolute top-0 right-0 bg-red-500 text-white text-xs rounded-full w-5 h-5 flex items-center justify-center">
            {unreadCount > 99 ? '99+' : unreadCount}
          </span>
        )}
        {!connected && (
          <span className="absolute bottom-0 right-0 w-2 h-2 bg-yellow-500 rounded-full" />
        )}
      </button>

      {/* Notification Panel */}
      {isOpen && (
        <div className="absolute right-0 mt-2 w-96 bg-white rounded-lg shadow-xl z-50 max-h-[600px] flex flex-col">
          {/* Header */}
          <div className="p-4 border-b">
            <div className="flex items-center justify-between mb-2">
              <h3 className="font-semibold text-lg">Notifications</h3>
              <button
                onClick={() => setIsOpen(false)}
                className="text-gray-400 hover:text-gray-600"
              >
                <XIcon className="w-5 h-5" />
              </button>
            </div>
            
            {/* Connection Status */}
            {error && (
              <div className="text-sm text-red-600 mb-2">
                {error}
              </div>
            )}
            {!connected && !error && (
              <div className="text-sm text-yellow-600 mb-2">
                Reconnecting...
              </div>
            )}

            {/* Filters */}
            <div className="flex gap-2">
              <button
                onClick={() => setFilter('all')}
                className={`px-3 py-1 rounded ${
                  filter === 'all'
                    ? 'bg-blue-500 text-white'
                    : 'bg-gray-100 text-gray-700'
                }`}
              >
                All
              </button>
              <button
                onClick={() => setFilter('unread')}
                className={`px-3 py-1 rounded ${
                  filter === 'unread'
                    ? 'bg-blue-500 text-white'
                    : 'bg-gray-100 text-gray-700'
                }`}
              >
                Unread ({unreadCount})
              </button>
              
              {unreadCount > 0 && (
                <button
                  onClick={markAllAsRead}
                  className="ml-auto text-sm text-blue-600 hover:text-blue-700"
                >
                  Mark all read
                </button>
              )}
            </div>
          </div>

          {/* Notification List */}
          <div className="flex-1 overflow-y-auto">
            {filteredNotifications.length === 0 ? (
              <div className="p-8 text-center text-gray-500">
                No notifications
              </div>
            ) : (
              <div className="divide-y">
                {filteredNotifications.map(notification => (
                  <NotificationItem
                    key={notification.id}
                    notification={notification}
                    onRead={markAsRead}
                    onDelete={deleteNotification}
                  />
                ))}
              </div>
            )}
          </div>

          {/* Footer */}
          {notifications.length > 0 && (
            <div className="p-3 border-t">
              <button
                onClick={clearAll}
                className="w-full text-center text-sm text-red-600 hover:text-red-700"
              >
                Clear all notifications
              </button>
            </div>
          )}
        </div>
      )}
    </div>
  );
}

function NotificationItem({
  notification,
  onRead,
  onDelete
}: {
  notification: Notification;
  onRead: (id: string) => void;
  onDelete: (id: string) => void;
}) {
  return (
    <div
      className={`p-4 hover:bg-gray-50 cursor-pointer ${
        !notification.read ? 'bg-blue-50' : ''
      }`}
      onClick={() => !notification.read && onRead(notification.id)}
    >
      <div className="flex items-start gap-3">
        {/* Icon based on type */}
        <div className={`flex-shrink-0 w-10 h-10 rounded-full flex items-center justify-center ${
          notification.priority === 'high' ? 'bg-red-100' :
          notification.priority === 'medium' ? 'bg-yellow-100' :
          'bg-blue-100'
        }`}>
          {notification.type === 'message' && <MessageIcon className="w-5 h-5" />}
          {notification.type === 'alert' && <AlertIcon className="w-5 h-5" />}
          {notification.type === 'update' && <UpdateIcon className="w-5 h-5" />}
        </div>

        {/* Content */}
        <div className="flex-1 min-w-0">
          <div className="flex items-start justify-between">
            <div>
              <h4 className="font-medium text-sm">{notification.title}</h4>
              <p className="text-sm text-gray-600 mt-1">{notification.body}</p>
              <span className="text-xs text-gray-400 mt-1">
                {formatDistanceToNow(new Date(notification.timestamp), { addSuffix: true })}
              </span>
            </div>
            
            {!notification.read && (
              <div className="w-2 h-2 bg-blue-500 rounded-full flex-shrink-0 ml-2" />
            )}
          </div>

          {/* Action Button */}
          {notification.actionUrl && (
            <a
              href={notification.actionUrl}
              className="text-sm text-blue-600 hover:text-blue-700 mt-2 inline-block"
            >
              View →
            </a>
          )}
        </div>

        {/* Delete Button */}
        <button
          onClick={(e) => {
            e.stopPropagation();
            onDelete(notification.id);
          }}
          className="text-gray-400 hover:text-gray-600"
        >
          <TrashIcon className="w-4 h-4" />
        </button>
      </div>
    </div>
  );
}
```

**3. Backend - WebSocket Server (Node.js + Socket.io)**

```typescript
// server/notifications.ts
import { Server } from 'socket.io';
import { createClient } from 'redis';
import amqp from 'amqplib';

interface NotificationPayload {
  userId: string;
  notification: Notification;
}

export class NotificationServer {
  private io: Server;
  private redisClient: any;
  private rabbitConnection: amqp.Connection | null = null;
  private rabbitChannel: amqp.Channel | null = null;

  constructor(io: Server) {
    this.io = io;
    this.setupRedis();
    this.setupRabbitMQ();
    this.setupSocketHandlers();
  }

  private async setupRedis() {
    this.redisClient = createClient({
      url: process.env.REDIS_URL
    });
    
    await this.redisClient.connect();
    
    // Subscribe to notification channel
    const subscriber = this.redisClient.duplicate();
    await subscriber.connect();
    
    await subscriber.subscribe('notifications', (message) => {
      const payload: NotificationPayload = JSON.parse(message);
      this.sendNotificationToUser(payload.userId, payload.notification);
    });
  }

  private async setupRabbitMQ() {
    try {
      this.rabbitConnection = await amqp.connect(process.env.RABBITMQ_URL!);
      this.rabbitChannel = await this.rabbitConnection.createChannel();
      
      await this.rabbitChannel.assertQueue('notifications', { durable: true });
      
      // Consume notifications from queue
      this.rabbitChannel.consume('notifications', (msg) => {
        if (msg) {
          const payload: NotificationPayload = JSON.parse(msg.content.toString());
          this.sendNotificationToUser(payload.userId, payload.notification);
          this.rabbitChannel?.ack(msg);
        }
      });
    } catch (error) {
      console.error('RabbitMQ setup error:', error);
    }
  }

  private setupSocketHandlers() {
    this.io.on('connection', async (socket) => {
      const userId = socket.handshake.auth.userId;
      
      if (!userId) {
        socket.disconnect();
        return;
      }

      console.log(`User ${userId} connected`);

      // Join user's personal room
      socket.join(`user:${userId}`);

      // Store socket ID in Redis for tracking
      await this.redisClient.set(
        `socket:${userId}`,
        socket.id,
        { EX: 3600 } // 1 hour expiry
      );

      // Send unread count on connection
      const unreadCount = await this.getUnreadCount(userId);
      socket.emit('unread_count', unreadCount);

      // Handle acknowledgments
      socket.on('notification:ack', async ({ notificationId }) => {
        await this.markAsDelivered(notificationId, userId);
      });

      // Handle mark as read
      socket.on('notification:mark_read', async ({ notificationId }) => {
        await this.markAsRead(notificationId, userId);
        socket.emit('notification:read', notificationId);
      });

      // Handle mark all as read
      socket.on('notification:mark_all_read', async () => {
        await this.markAllAsRead(userId);
        socket.emit('notification:read_all');
      });

      // Handle delete
      socket.on('notification:delete', async ({ notificationId }) => {
        await this.deleteNotification(notificationId, userId);
      });

      // Handle clear all
      socket.on('notification:clear_all', async () => {
        await this.clearAllNotifications(userId);
      });

      // Handle disconnect
      socket.on('disconnect', async () => {
        console.log(`User ${userId} disconnected`);
        await this.redisClient.del(`socket:${userId}`);
      });
    });
  }

  private async sendNotificationToUser(userId: string, notification: Notification) {
    // Store in database
    await this.storeNotification(userId, notification);

    // Send via WebSocket if user is connected
    this.io.to(`user:${userId}`).emit('notification', notification);

    // If user is not connected, queue for later delivery
    const socketId = await this.redisClient.get(`socket:${userId}`);
    if (!socketId) {
      await this.queueForLaterDelivery(userId, notification);
    }
  }

  private async storeNotification(userId: string, notification: Notification) {
    // Store in PostgreSQL
    await db.notification.create({
      data: {
        id: notification.id,
        userId,
        type: notification.type,
        title: notification.title,
        body: notification.body,
        priority: notification.priority,
        read: false,
        delivered: false,
        metadata: notification.metadata
      }
    });
  }

  private async queueForLaterDelivery(userId: string, notification: Notification) {
    // Store in Redis list for later delivery
    await this.redisClient.rPush(
      `queue:${userId}`,
      JSON.stringify(notification)
    );
  }

  private async getUnreadCount(userId: string): Promise<number> {
    return await db.notification.count({
      where: { userId, read: false }
    });
  }

  private async markAsDelivered(notificationId: string, userId: string) {
    await db.notification.update({
      where: { id: notificationId, userId },
      data: { delivered: true }
    });
  }

  private async markAsRead(notificationId: string, userId: string) {
    await db.notification.update({
      where: { id: notificationId, userId },
      data: { read: true, readAt: new Date() }
    });
  }

  private async markAllAsRead(userId: string) {
    await db.notification.updateMany({
      where: { userId, read: false },
      data: { read: true, readAt: new Date() }
    });
  }

  private async deleteNotification(notificationId: string, userId: string) {
    await db.notification.delete({
      where: { id: notificationId, userId }
    });
  }

  private async clearAllNotifications(userId: string) {
    await db.notification.deleteMany({
      where: { userId }
    });
  }
}
```

**4. Notification Service (Pub/Sub Pattern)**

```typescript
// services/NotificationService.ts
import { createClient } from 'redis';
import amqp from 'amqplib';

export class NotificationService {
  private redisClient: any;
  private rabbitChannel: amqp.Channel | null = null;

  async sendNotification(
    userId: string,
    notification: Omit<Notification, 'id' | 'timestamp' | 'read'>
  ) {
    const fullNotification: Notification = {
      id: generateId(),
      timestamp: new Date().toISOString(),
      read: false,
      ...notification
    };

    // Check user preferences
    const preferences = await this.getUserPreferences(userId);
    
    if (!this.shouldSendNotification(preferences, notification)) {
      return;
    }

    // Publish to Redis (for WebSocket servers)
    await this.publishToRedis(userId, fullNotification);

    // Queue in RabbitMQ (for reliability)
    await this.queueInRabbitMQ(userId, fullNotification);

    // Send to other channels based on preferences
    if (preferences.emailEnabled && notification.priority === 'high') {
      await this.sendEmail(userId, fullNotification);
    }

    if (preferences.pushEnabled) {
      await this.sendPushNotification(userId, fullNotification);
    }
  }

  private async publishToRedis(userId: string, notification: Notification) {
    const publisher = this.redisClient.duplicate();
    await publisher.connect();
    
    await publisher.publish(
      'notifications',
      JSON.stringify({ userId, notification })
    );
    
    await publisher.quit();
  }

  private async queueInRabbitMQ(userId: string, notification: Notification) {
    if (!this.rabbitChannel) return;

    await this.rabbitChannel.sendToQueue(
      'notifications',
      Buffer.from(JSON.stringify({ userId, notification })),
      { persistent: true }
    );
  }

  private async getUserPreferences(userId: string) {
    return await db.notificationPreferences.findUnique({
      where: { userId }
    }) || defaultPreferences;
  }

  private shouldSendNotification(
    preferences: NotificationPreferences,
    notification: any
  ): boolean {
    // Check do-not-disturb
    if (preferences.doNotDisturb) {
      return notification.priority === 'high';
    }

    // Check type preferences
    if (!preferences.notificationTypes[notification.type]) {
      return false;
    }

    // Check quiet hours
    if (this.isInQuietHours(preferences.quietHours)) {
      return notification.priority === 'high';
    }

    return true;
  }

  private isInQuietHours(quietHours: { start: string; end: string }): boolean {
    const now = new Date();
    const currentHour = now.getHours();
    
    const startHour = parseInt(quietHours.start.split(':')[0]);
    const endHour = parseInt(quietHours.end.split(':')[0]);

    if (startHour <= endHour) {
      return currentHour >= startHour && currentHour < endHour;
    } else {
      return currentHour >= startHour || currentHour < endHour;
    }
  }
}
```

**Trade-offs & Considerations:**

1. **WebSocket vs Server-Sent Events (SSE)**
   - ✅ WebSocket: Bi-directional, better for interactive apps
   - ✅ SSE: Simpler, automatic reconnection, works through proxies
   - Decision: WebSocket for rich interactions

2. **Redis Pub/Sub vs Message Queue**
   - Redis: Fast, but no message persistence
   - RabbitMQ: Reliable delivery, message persistence
   - Decision: Use both - Redis for real-time, RabbitMQ for reliability

3. **Horizontal Scaling**
   - Use Redis for session persistence
   - Sticky sessions with load balancer
   - Multiple WebSocket servers with shared Redis

4. **Offline Support**
   - IndexedDB for local storage
   - Service Worker for background sync
   - Queue notifications when reconnecting

---

### Scenario 2: Multi-Tenant Application Architecture

**Problem Statement:**
Design a SaaS application that supports multiple tenants (organizations) with complete data isolation, custom branding, feature flags, and per-tenant billing. System should support 10k+ tenants efficiently.

**Requirements:**
- Complete data isolation between tenants
- Custom branding (logo, colors, domain)
- Feature flags per tenant
- Row-level security
- Tenant-specific configurations
- Performance isolation
- Efficient resource utilization

---

#### Architecture Strategy

```
┌─────────────────────────────────────────────────────────────┐
│                     Tenant Resolution                        │
│  (Domain/Subdomain → Tenant ID → Context)                   │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    Next.js Middleware                        │
│          (Extract tenant, inject context)                   │
└────────────────────────┬────────────────────────────────────┘
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Tenant     │  │   Features   │  │   Branding   │
│   Context    │  │   Service    │  │   Service    │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                   Database Layer                             │
│  Schema: tenant_id + data (Row Level Security)              │
└─────────────────────────────────────────────────────────────┘
```

---

#### Implementation

**1. Multi-Tenant Data Models**

```typescript
// prisma/schema.prisma
model Tenant {
  id            String   @id @default(cuid())
  name          String
  slug          String   @unique
  domain        String?  @unique
  plan          Plan     @default(FREE)
  status        TenantStatus @default(ACTIVE)
  
  // Branding
  logo          String?
  primaryColor  String   @default("#3b82f6")
  accentColor   String   @default("#8b5cf6")
  
  // Settings
  settings      Json     @default("{}")
  features      String[] // Feature flags
  
  // Limits
  maxUsers      Int      @default(10)
  maxStorage    Int      @default(1000) // MB
  
  // Relationships
  users         User[]
  projects      Project[]
  
  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt
}

model User {
  id        String   @id @default(cuid())
  email     String
  name      String
  role      Role     @default(MEMBER)
  
  tenantId  String
  tenant    Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  
  // Ensure email is unique per tenant
  @@unique([email, tenantId])
  @@index([tenantId])
}

model Project {
  id          String   @id @default(cuid())
  name        String
  description String?
  
  tenantId    String
  tenant      Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)
  
  ownerId     String
  owner       User     @relation(fields: [ownerId], references: [id])
  
  @@index([tenantId])
  @@index([ownerId])
}

enum Plan {
  FREE
  STARTER
  PROFESSIONAL
  ENTERPRISE
}

enum TenantStatus {
  ACTIVE
  SUSPENDED
  TRIAL
}

enum Role {
  OWNER
  ADMIN
  MEMBER
  VIEWER
}
```

**2. Tenant Resolution Middleware**

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';
import { getTenantByDomain, getTenantBySlug } from '@/lib/tenant';

export async function middleware(request: NextRequest) {
  const { pathname, hostname } = request.nextUrl;

  // Skip for API routes, static files, etc.
  if (
    pathname.startsWith('/api') ||
    pathname.startsWith('/_next') ||
    pathname.startsWith('/static')
  ) {
    return NextResponse.next();
  }

  let tenant;

  // Strategy 1: Custom domain (app.customer.com)
  if (hostname !== process.env.NEXT_PUBLIC_APP_DOMAIN) {
    tenant = await getTenantByDomain(hostname);
  }
  
  // Strategy 2: Subdomain (customer.myapp.com)
  else {
    const subdomain = hostname.split('.')[0];
    if (subdomain !== 'www' && subdomain !== 'myapp') {
      tenant = await getTenantBySlug(subdomain);
    }
  }

  // Strategy 3: Path-based (/t/customer-slug)
  if (!tenant && pathname.startsWith('/t/')) {
    const slug = pathname.split('/')[2];
    if (slug) {
      tenant = await getTenantBySlug(slug);
    }
  }

  if (!tenant) {
    // Redirect to tenant selection or error page
    return NextResponse.redirect(new URL('/select-tenant', request.url));
  }

  // Check tenant status
  if (tenant.status === 'SUSPENDED') {
    return NextResponse.redirect(new URL('/suspended', request.url));
  }

  // Inject tenant context into headers
  const response = NextResponse.next();
  response.headers.set('X-Tenant-ID', tenant.id);
  response.headers.set('X-Tenant-Slug', tenant.slug);
  response.headers.set('X-Tenant-Plan', tenant.plan);
  
  // Set cookie for client-side access
  response.cookies.set('tenant-id', tenant.id, {
    httpOnly: false,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'lax'
  });

  return response;
}

export const config = {
  matcher: [
    /*
     * Match all request paths except:
     * - api (API routes)
     * - _next/static (static files)
     * - _next/image (image optimization files)
     * - favicon.ico (favicon file)
     */
    '/((?!api|_next/static|_next/image|favicon.ico).*)',
  ],
};
```

**3. Tenant Context Provider**

```typescript
// contexts/TenantContext.tsx
import { createContext, useContext, useEffect, useState } from 'react';
import { useRouter } from 'next/router';

interface Tenant {
  id: string;
  name: string;
  slug: string;
  logo?: string;
  primaryColor: string;
  accentColor: string;
  features: string[];
  plan: string;
  settings: Record<string, any>;
}

interface TenantContextValue {
  tenant: Tenant | null;
  loading: boolean;
  hasFeature: (feature: string) => boolean;
  canAccess: (resource: string, action: string) => boolean;
  refreshTenant: () => Promise<void>;
}

const TenantContext = createContext<TenantContextValue | null>(null);

export function TenantProvider({ children, initialTenant }: {
  children: React.ReactNode;
  initialTenant?: Tenant;
}) {
  const [tenant, setTenant] = useState<Tenant | null>(initialTenant || null);
  const [loading, setLoading] = useState(!initialTenant);
  const router = useRouter();

  useEffect(() => {
    if (!initialTenant) {
      loadTenant();
    }
  }, [initialTenant]);

  const loadTenant = async () => {
    try {
      const response = await fetch('/api/tenant/current');
      const data = await response.json();
      setTenant(data.tenant);
    } catch (error) {
      console.error('Failed to load tenant:', error);
      router.push('/select-tenant');
    } finally {
      setLoading(false);
    }
  };

  const hasFeature = (feature: string): boolean => {
    if (!tenant) return false;
    return tenant.features.includes(feature);
  };

  const canAccess = (resource: string, action: string): boolean => {
    if (!tenant) return false;

    // Plan-based access control
    const accessMatrix: Record<string, string[]> = {
      FREE: ['projects:read', 'projects:create:limited'],
      STARTER: ['projects:read', 'projects:create', 'projects:update'],
      PROFESSIONAL: ['projects:*', 'analytics:read', 'api:access'],
      ENTERPRISE: ['*']
    };

    const allowedActions = accessMatrix[tenant.plan] || [];
    
    return allowedActions.some(allowed => {
      if (allowed === '*') return true;
      if (allowed === `${resource}:*`) return true;
      return allowed === `${resource}:${action}`;
    });
  };

  const refreshTenant = async () => {
    await loadTenant();
  };

  return (
    <TenantContext.Provider
      value={{ tenant, loading, hasFeature, canAccess, refreshTenant }}
    >
      {children}
    </TenantContext.Provider>
  );
}

export function useTenant() {
  const context = useContext(TenantContext);
  if (!context) {
    throw new Error('useTenant must be used within TenantProvider');
  }
  return context;
}

// Feature flag hook
export function useFeature(feature: string): boolean {
  const { hasFeature } = useTenant();
  return hasFeature(feature);
}

// Permission hook
export function usePermission(resource: string, action: string): boolean {
  const { canAccess } = useTenant();
  return canAccess(resource, action);
}
```

**4. Tenant-Aware Database Queries**

```typescript
// lib/db.ts
import { PrismaClient } from '@prisma/client';
import { headers } from 'next/headers';

export class TenantAwareDB {
  private prisma: PrismaClient;

  constructor() {
    this.prisma = new PrismaClient();
  }

  private getTenantId(): string {
    const headersList = headers();
    const tenantId = headersList.get('X-Tenant-ID');
    
    if (!tenantId) {
      throw new Error('Tenant context not found');
    }
    
    return tenantId;
  }

  // Tenant-scoped queries
  async findManyProjects(where?: any) {
    const tenantId = this.getTenantId();
    
    return this.prisma.project.findMany({
      where: {
        tenantId,
        ...where
      }
    });
  }

  async createProject(data: any) {
    const tenantId = this.getTenantId();
    
    return this.prisma.project.create({
      data: {
        ...data,
        tenantId
      }
    });
  }

  async updateProject(id: string, data: any) {
    const tenantId = this.getTenantId();
    
    // Ensure project belongs to tenant
    const project = await this.prisma.project.findFirst({
      where: { id, tenantId }
    });
    
    if (!project) {
      throw new Error('Project not found or access denied');
    }
    
    return this.prisma.project.update({
      where: { id },
      data
    });
  }

  async deleteProject(id: string) {
    const tenantId = this.getTenantId();
    
    return this.prisma.project.delete({
      where: {
        id,
        tenantId
      }
    });
  }

  // User management with tenant scope
  async findManyUsers(where?: any) {
    const tenantId = this.getTenantId();
    
    return this.prisma.user.findMany({
      where: {
        tenantId,
        ...where
      }
    });
  }

  async createUser(data: any) {
    const tenantId = this.getTenantId();
    
    // Check tenant limits
    const tenant = await this.prisma.tenant.findUnique({
      where: { id: tenantId },
      include: { _count: { select: { users: true } } }
    });
    
    if (tenant && tenant._count.users >= tenant.maxUsers) {
      throw new Error('User limit reached for this plan');
    }
    
    return this.prisma.user.create({
      data: {
        ...data,
        tenantId
      }
    });
  }
}

export const db = new TenantAwareDB();
```

**5. Tenant Branding & Theming**

```typescript
// components/TenantThemeProvider.tsx
import { useTenant } from '@/contexts/TenantContext';
import { useEffect } from 'react';

export function TenantThemeProvider({ children }: { children: React.ReactNode }) {
  const { tenant } = useTenant();

  useEffect(() => {
    if (!tenant) return;

    // Apply custom colors
    document.documentElement.style.setProperty('--color-primary', tenant.primaryColor);
    document.documentElement.style.setProperty('--color-accent', tenant.accentColor);

    // Apply custom logo
    const favicon = document.querySelector('link[rel="icon"]') as HTMLLinkElement;
    if (favicon && tenant.logo) {
      favicon.href = tenant.logo;
    }

    // Update page title
    document.title = `${tenant.name} - Dashboard`;
  }, [tenant]);

  if (!tenant) {
    return <div>Loading...</div>;
  }

  return (
    <div className="tenant-theme" data-tenant={tenant.slug}>
      {children}
    </div>
  );
}

// Usage in _app.tsx
export default function App({ Component, pageProps }: AppProps) {
  return (
    <TenantProvider initialTenant={pageProps.tenant}>
      <TenantThemeProvider>
        <Component {...pageProps} />
      </TenantThemeProvider>
    </TenantProvider>
  );
}
```

**6. Row-Level Security (PostgreSQL)**

```sql
-- Enable Row Level Security
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Create policy for tenant isolation
CREATE POLICY tenant_isolation_policy ON projects
  USING (tenant_id = current_setting('app.current_tenant_id')::text);

CREATE POLICY tenant_isolation_policy ON users
  USING (tenant_id = current_setting('app.current_tenant_id')::text);

-- In Prisma, set tenant context before queries
-- prisma/middleware.ts
prisma.$use(async (params, next) => {
  if (params.model) {
    // Set tenant context
    await prisma.$executeRaw`
      SET LOCAL app.current_tenant_id = ${tenantId}
    `;
  }
  
  return next(params);
});
```

**Trade-offs & Decisions:**

1. **Database Strategy**
   - Option A: Separate database per tenant (max isolation, complex)
   - Option B: Shared database with tenant_id column (efficient, simpler)
   - Option C: Separate schema per tenant (middle ground)
   - **Decision**: Shared database with RLS for < 10k tenants

2. **Tenant Resolution**
   - Custom domains (best UX, SSL complexity)
   - Subdomains (good UX, wildcard SSL)
   - Path-based (simplest, worse UX)
   - **Decision**: Support all three with priority order

3. **Feature Flags**
   - Database array (simple, tenant-specific)
   - External service (LaunchDarkly - powerful, cost)
   - **Decision**: Database for tenant features, external for app features

4. **Performance**
   - Connection pooling per tenant vs shared pool
   - Caching strategy (tenant-specific cache keys)
   - Query optimization with proper indexing

---

### Scenario 3: Design System / Component Library Structure

**Problem Statement:**
Design a scalable design system and component library for a large organization that supports multiple products, maintains consistency, allows customization, and has clear documentation. The system should serve 50+ developers across 10+ products.

**Requirements:**
- Consistent design language
- Reusable components
- Theme customization
- Accessibility (WCAG 2.1 AA)
- Documentation with live examples
- Version control
- TypeScript support
- Multiple frameworks (React, Vue, Angular)
- Icon system
- Design tokens

---

#### Architecture

```
design-system/
├── packages/
│   ├── tokens/           # Design tokens (colors, spacing, typography)
│   ├── core/            # Core utilities, hooks, context
│   ├── icons/           # Icon library
│   ├── react/           # React components
│   ├── vue/             # Vue components (optional)
│   └── docs/            # Documentation site
├── apps/
│   └── storybook/       # Component playground
├── tools/
│   └── figma-sync/      # Sync from Figma
└── examples/
    ├── react-app/
    └── nextjs-app/
```

---

#### Implementation

**1. Design Tokens Package**

```typescript
// packages/tokens/src/colors.ts
export const colors = {
  // Brand colors
  brand: {
    50: '#eff6ff',
    100: '#dbeafe',
    200: '#bfdbfe',
    300: '#93c5fd',
    400: '#60a5fa',
    500: '#3b82f6', // Primary
    600: '#2563eb',
    700: '#1d4ed8',
    800: '#1e40af',
    900: '#1e3a8a',
  },
  
  // Semantic colors
  semantic: {
    success: {
      light: '#d1fae5',
      base: '#10b981',
      dark: '#047857',
    },
    warning: {
      light: '#fef3c7',
      base: '#f59e0b',
      dark: '#d97706',
    },
    error: {
      light: '#fee2e2',
      base: '#ef4444',
      dark: '#dc2626',
    },
    info: {
      light: '#dbeafe',
      base: '#3b82f6',
      dark: '#1d4ed8',
    },
  },
  
  // Neutral colors
  neutral: {
    0: '#ffffff',
    50: '#f9fafb',
    100: '#f3f4f6',
    200: '#e5e7eb',
    300: '#d1d5db',
    400: '#9ca3af',
    500: '#6b7280',
    600: '#4b5563',
    700: '#374151',
    800: '#1f2937',
    900: '#111827',
    1000: '#000000',
  },
} as const;

// packages/tokens/src/typography.ts
export const typography = {
  fontFamily: {
    sans: 'Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif',
    mono: 'JetBrains Mono, Consolas, Monaco, monospace',
  },
  
  fontSize: {
    xs: '0.75rem',    // 12px
    sm: '0.875rem',   // 14px
    base: '1rem',     // 16px
    lg: '1.125rem',   // 18px
    xl: '1.25rem',    // 20px
    '2xl': '1.5rem',  // 24px
    '3xl': '1.875rem', // 30px
    '4xl': '2.25rem', // 36px
    '5xl': '3rem',    // 48px
  },
  
  fontWeight: {
    light: 300,
    normal: 400,
    medium: 500,
    semibold: 600,
    bold: 700,
  },
  
  lineHeight: {
    tight: 1.25,
    normal: 1.5,
    relaxed: 1.75,
  },
} as const;

// packages/tokens/src/spacing.ts
export const spacing = {
  0: '0',
  1: '0.25rem',  // 4px
  2: '0.5rem',   // 8px
  3: '0.75rem',  // 12px
  4: '1rem',     // 16px
  5: '1.25rem',  // 20px
  6: '1.5rem',   // 24px
  8: '2rem',     // 32px
  10: '2.5rem',  // 40px
  12: '3rem',    // 48px
  16: '4rem',    // 64px
  20: '5rem',    // 80px
  24: '6rem',    // 96px
} as const;

// packages/tokens/src/index.ts
export * from './colors';
export * from './typography';
export * from './spacing';
export * from './shadows';
export * from './borders';
export * from './transitions';
```

**2. Core Package (Utilities & Hooks)**

```typescript
// packages/core/src/theme/ThemeProvider.tsx
import { createContext, useContext, useMemo } from 'react';
import { colors, typography, spacing } from '@design-system/tokens';

export interface Theme {
  colors: typeof colors;
  typography: typeof typography;
  spacing: typeof spacing;
  mode: 'light' | 'dark';
  customizations?: Partial<Theme>;
}

const ThemeContext = createContext<Theme | null>(null);

export function ThemeProvider({
  children,
  theme,
  customizations
}: {
  children: React.ReactNode;
  theme?: Partial<Theme>;
  customizations?: Partial<Theme>;
}) {
  const defaultTheme: Theme = {
    colors,
    typography,
    spacing,
    mode: 'light',
  };

  const mergedTheme = useMemo(
    () => ({
      ...defaultTheme,
      ...theme,
      ...customizations,
    }),
    [theme, customizations]
  );

  return (
    <ThemeContext.Provider value={mergedTheme}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// packages/core/src/hooks/useMediaQuery.ts
export function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const media = window.matchMedia(query);
    
    if (media.matches !== matches) {
      setMatches(media.matches);
    }

    const listener = () => setMatches(media.matches);
    media.addEventListener('change', listener);
    
    return () => media.removeEventListener('change', listener);
  }, [matches, query]);

  return matches;
}

// packages/core/src/hooks/useBreakpoint.ts
export function useBreakpoint() {
  const isSm = useMediaQuery('(min-width: 640px)');
  const isMd = useMediaQuery('(min-width: 768px)');
  const isLg = useMediaQuery('(min-width: 1024px)');
  const isXl = useMediaQuery('(min-width: 1280px)');
  const is2Xl = useMediaQuery('(min-width: 1536px)');

  return {
    isSm,
    isMd,
    isLg,
    isXl,
    is2Xl,
    current:
      is2Xl ? '2xl' :
      isXl ? 'xl' :
      isLg ? 'lg' :
      isMd ? 'md' :
      isSm ? 'sm' : 'xs'
  };
}
```

**3. Component Package Structure**

```typescript
// packages/react/src/Button/Button.tsx
import { forwardRef, ButtonHTMLAttributes } from 'react';
import { cva, type VariantProps } from 'class-variance-authority';

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        primary: 'bg-brand-500 text-white hover:bg-brand-600 focus-visible:ring-brand-500',
        secondary: 'bg-neutral-100 text-neutral-900 hover:bg-neutral-200 focus-visible:ring-neutral-500',
        outline: 'border-2 border-brand-500 text-brand-500 hover:bg-brand-50 focus-visible:ring-brand-500',
        ghost: 'hover:bg-neutral-100 text-neutral-700 focus-visible:ring-neutral-500',
        danger: 'bg-error-500 text-white hover:bg-error-600 focus-visible:ring-error-500',
        link: 'text-brand-500 underline-offset-4 hover:underline',
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-12 px-6 text-lg',
        xl: 'h-14 px-8 text-xl',
      },
      fullWidth: {
        true: 'w-full',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);

export interface ButtonProps
  extends ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  loading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      className,
      variant,
      size,
      fullWidth,
      loading,
      disabled,
      leftIcon,
      rightIcon,
      children,
      ...props
    },
    ref
  ) => {
    return (
      <button
        ref={ref}
        className={buttonVariants({ variant, size, fullWidth, className })}
        disabled={disabled || loading}
        aria-busy={loading}
        {...props}
      >
        {loading && (
          <svg
            className="mr-2 h-4 w-4 animate-spin"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <circle
              className="opacity-25"
              cx="12"
              cy="12"
              r="10"
              stroke="currentColor"
              strokeWidth="4"
            />
            <path
              className="opacity-75"
              fill="currentColor"
              d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
            />
          </svg>
        )}
        {!loading && leftIcon && <span className="mr-2">{leftIcon}</span>}
        {children}
        {!loading && rightIcon && <span className="ml-2">{rightIcon}</span>}
      </button>
    );
  }
);

Button.displayName = 'Button';

// packages/react/src/Button/Button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'outline', 'ghost', 'danger', 'link'],
    },
    size: {
      control: 'select',
      options: ['sm', 'md', 'lg', 'xl'],
    },
    loading: {
      control: 'boolean',
    },
    disabled: {
      control: 'boolean',
    },
  },
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Button',
  },
};

export const Secondary: Story = {
  args: {
    variant: 'secondary',
    children: 'Button',
  },
};

export const AllVariants: Story = {
  render: () => (
    <div className="flex flex-col gap-4">
      <div className="flex gap-4">
        <Button variant="primary">Primary</Button>
        <Button variant="secondary">Secondary</Button>
        <Button variant="outline">Outline</Button>
        <Button variant="ghost">Ghost</Button>
        <Button variant="danger">Danger</Button>
        <Button variant="link">Link</Button>
      </div>
    </div>
  ),
};

export const AllSizes: Story = {
  render: () => (
    <div className="flex items-center gap-4">
      <Button size="sm">Small</Button>
      <Button size="md">Medium</Button>
      <Button size="lg">Large</Button>
      <Button size="xl">Extra Large</Button>
    </div>
  ),
};

export const WithIcons: Story = {
  render: () => (
    <div className="flex gap-4">
      <Button leftIcon={<span>→</span>}>With Left Icon</Button>
      <Button rightIcon={<span>←</span>}>With Right Icon</Button>
      <Button leftIcon={<span>→</span>} rightIcon={<span>←</span>}>
        Both Icons
      </Button>
    </div>
  ),
};

export const Loading: Story = {
  args: {
    loading: true,
    children: 'Loading...',
  },
};
```

**4. Composite Components**

```typescript
// packages/react/src/Card/Card.tsx
export interface CardProps {
  children: React.ReactNode;
  variant?: 'elevated' | 'outlined' | 'filled';
  padding?: keyof typeof spacing;
  hoverable?: boolean;
  clickable?: boolean;
  onClick?: () => void;
}

export function Card({
  children,
  variant = 'elevated',
  padding = 4,
  hoverable,
  clickable,
  onClick,
}: CardProps) {
  const variants = {
    elevated: 'bg-white shadow-lg',
    outlined: 'bg-white border-2 border-neutral-200',
    filled: 'bg-neutral-50',
  };

  const interactiveClasses = 
    (hoverable || clickable) &&
    'transition-transform hover:scale-[1.02] hover:shadow-xl';
  
  const clickableClasses = 
    clickable && 'cursor-pointer';

  return (
    <div
      className={cn(
        'rounded-lg',
        variants[variant],
        `p-${padding}`,
        interactiveClasses,
        clickableClasses
      )}
      onClick={clickable ? onClick : undefined}
      role={clickable ? 'button' : undefined}
      tabIndex={clickable ? 0 : undefined}
    >
      {children}
    </div>
  );
}

// Compound components
Card.Header = function CardHeader({ children }: { children: React.ReactNode }) {
  return <div className="mb-4 pb-4 border-b border-neutral-200">{children}</div>;
};

Card.Title = function CardTitle({ children }: { children: React.ReactNode }) {
  return <h3 className="text-xl font-semibold text-neutral-900">{children}</h3>;
};

Card.Body = function CardBody({ children }: { children: React.ReactNode }) {
  return <div className="text-neutral-700">{children}</div>;
};

Card.Footer = function CardFooter({ children }: { children: React.ReactNode }) {
  return <div className="mt-4 pt-4 border-t border-neutral-200">{children}</div>;
};

// Usage
<Card variant="elevated" hoverable>
  <Card.Header>
    <Card.Title>Card Title</Card.Title>
  </Card.Header>
  <Card.Body>
    Card content goes here
  </Card.Body>
  <Card.Footer>
    <Button>Action</Button>
  </Card.Footer>
</Card>
```

**5. Documentation Structure**

```markdown
# Component Documentation Template

## Button

A versatile button component with multiple variants and states.

### Import

\```tsx
import { Button } from '@design-system/react';
\```

### Usage

\```tsx
<Button variant="primary" size="md">
  Click me
</Button>
\```

### Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'primary' \| 'secondary' \| 'outline' \| 'ghost' \| 'danger' \| 'link' | 'primary' | Visual style variant |
| size | 'sm' \| 'md' \| 'lg' \| 'xl' | 'md' | Button size |
| fullWidth | boolean | false | Makes button full width |
| loading | boolean | false | Shows loading spinner |
| disabled | boolean | false | Disables the button |
| leftIcon | ReactNode | - | Icon to show on the left |
| rightIcon | ReactNode | - | Icon to show on the right |

### Accessibility

- Uses semantic `<button>` element
- Proper ARIA attributes (`aria-busy`, `aria-disabled`)
- Keyboard accessible
- Focus visible styles
- Screen reader friendly

### Examples

#### Variants
[Interactive example here]

#### With Icons
[Interactive example here]

#### Loading State
[Interactive example here]

### Design Tokens

\```ts
// Colors used
colors.brand[500] // Primary
colors.neutral[100] // Secondary
colors.error[500] // Danger

// Spacing
spacing[3] // sm padding
spacing[4] // md padding
spacing[6] // lg padding
\```

### Related Components

- [IconButton](#iconbutton)
- [ButtonGroup](#buttongroup)
- [Link](#link)
```

**6. Version Management & Release Process**

```json
// packages/react/package.json
{
  "name": "@design-system/react",
  "version": "2.1.0",
  "description": "React components for Design System",
  "main": "./dist/index.js",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "require": "./dist/index.js",
      "import": "./dist/index.mjs",
      "types": "./dist/index.d.ts"
    },
    "./Button": {
      "require": "./dist/Button/index.js",
      "import": "./dist/Button/index.mjs",
      "types": "./dist/Button/index.d.ts"
    }
  },
  "sideEffects": false,
  "files": ["dist"],
  "scripts": {
    "build": "tsup src/index.ts --format cjs,esm --dts",
    "dev": "tsup src/index.ts --format cjs,esm --dts --watch",
    "lint": "eslint src/",
    "type-check": "tsc --noEmit"
  },
  "peerDependencies": {
    "react": ">=18.0.0",
    "react-dom": ">=18.0.0"
  },
  "dependencies": {
    "@design-system/tokens": "workspace:*",
    "@design-system/core": "workspace:*",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.0.0"
  }
}
```

**Trade-offs & Best Practices:**

1. **Monorepo vs Multi-repo**
   - Monorepo: Easier coordination, shared tooling
   - Multi-repo: Independent versioning, slower sync
   - **Decision**: Monorepo with Turborepo

2. **Component API Design**
   - Compound components for flexibility
   - Render props for customization
   - Polymorphic components (`as` prop)
   - Controlled vs uncontrolled patterns

3. **Styling Approach**
   - CSS-in-JS (runtime cost, dynamic)
   - Utility-first (Tailwind - fast, consistent)
   - CSS Modules (scoped, traditional)
   - **Decision**: Tailwind + CVA for variants

4. **Documentation**
   - Storybook for component playground
   - MDX for rich documentation
   - Auto-generated API docs from TypeScript
   - Live code examples

5. **Testing Strategy**
   - Unit tests (Jest + Testing Library)
   - Visual regression (Chromatic)
   - Accessibility tests (jest-axe)
   - Integration tests (Playwright)

---

## Monorepo Architecture

### Turborepo Setup

**Why Monorepo?**
- Shared code and dependencies
- Atomic changes across packages
- Simplified dependency management
- Consistent tooling and configs
- Easier refactoring

**Structure:**

```
monorepo/
├── apps/
│   ├── web/              # Main application
│   ├── admin/            # Admin dashboard
│   ├── mobile/           # React Native app
│   └── docs/             # Documentation site
├── packages/
│   ├── ui/               # Shared UI components
│   ├── config/           # Shared configs (ESLint, TS, etc.)
│   ├── utils/            # Shared utilities
│   ├── api-client/       # API client
│   └── types/            # Shared TypeScript types
├── tools/
│   └── generators/       # Code generators
├── turbo.json            # Turborepo configuration
├── package.json          # Root package.json
└── pnpm-workspace.yaml   # Workspace configuration
```

---

#### Implementation

**1. Root Configuration**

```json
// package.json
{
  "name": "monorepo",
  "private": true,
  "workspaces": [
    "apps/*",
    "packages/*"
  ],
  "scripts": {
    "dev": "turbo run dev",
    "build": "turbo run build",
    "lint": "turbo run lint",
    "test": "turbo run test",
    "clean": "turbo run clean && rm -rf node_modules",
    "format": "prettier --write \"**/*.{ts,tsx,md}\""
  },
  "devDependencies": {
    "@turbo/gen": "^1.10.0",
    "eslint": "^8.48.0",
    "prettier": "^3.0.0",
    "turbo": "^1.10.0",
    "typescript": "^5.2.0"
  },
  "packageManager": "pnpm@8.6.0"
}

// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": ["**/.env.*local"],
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "dist/**", "build/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "lint": {
      "dependsOn": ["^lint"]
    },
    "test": {
      "dependsOn": ["^build"],
      "outputs": ["coverage/**"]
    },
    "deploy": {
      "dependsOn": ["build", "test", "lint"]
    }
  }
}

// pnpm-workspace.yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

**2. Shared Package Example**

```typescript
// packages/ui/package.json
{
  "name": "@repo/ui",
  "version": "0.0.0",
  "private": true,
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx"
  },
  "scripts": {
    "lint": "eslint src/",
    "type-check": "tsc --noEmit"
  },
  "peerDependencies": {
    "react": "^18.2.0"
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "@types/react": "^18.2.0",
    "typescript": "^5.2.0"
  }
}

// packages/ui/src/button.tsx
export interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary';
  onClick?: () => void;
}

export function Button({ children, variant = 'primary', onClick }: ButtonProps) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

**3. App Using Shared Packages**

```typescript
// apps/web/package.json
{
  "name": "web",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "@repo/ui": "workspace:*",
    "@repo/utils": "workspace:*",
    "@repo/types": "workspace:*",
    "next": "14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@repo/typescript-config": "workspace:*",
    "@repo/eslint-config": "workspace:*"
  }
}

// apps/web/app/page.tsx
import { Button } from '@repo/ui/button';
import { Card } from '@repo/ui/card';

export default function Page() {
  return (
    <div>
      <Card>
        <h1>Welcome</h1>
        <Button variant="primary">Get Started</Button>
      </Card>
    </div>
  );
}
```

**4. Remote Caching**

```bash
# .turbo/config.json
{
  "teamId": "team_xxx",
  "apiUrl": "https://api.vercel.com"
}

# Enable remote caching
turbo login
turbo link

# Now builds are cached remotely
turbo run build
```

---

### Nx Workspace

**Nx vs Turborepo:**
- Nx: More features, generators, dependency graph
- Turborepo: Simpler, faster, better for simple setups
- Both support remote caching

**Nx Setup:**

```bash
npx create-nx-workspace@latest myorg

# Choose: apps, integrated monorepo, React
```

**Structure:**

```
nx-monorepo/
├── apps/
│   ├── web/
│   ├── admin/
│   └── mobile/
├── libs/
│   ├── ui/
│   ├── data-access/
│   ├── feature-auth/
│   └── util-formatting/
├── tools/
└── nx.json
```

**Key Features:**

```json
// nx.json
{
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build", "test", "lint"],
        "parallel": 3,
        "cacheDirectory": "tmp/nx-cache"
      }
    }
  },
  "targetDefaults": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["production", "^production"],
      "outputs": ["{projectRoot}/dist"]
    }
  }
}

// Affected commands (only test/build what changed)
nx affected:test
nx affected:build
nx affected:lint

// Dependency graph
nx graph

// Generate new library
nx g @nx/react:library my-lib

// Generate new component
nx g @nx/react:component my-component --project=ui
```

---

## Micro-Frontends

### Module Federation (Webpack 5)

**Architecture:**

```
┌─────────────────────────────────────────────┐
│           Shell / Host Application          │
│         (Header, Nav, Auth, Routing)        │
└──────────┬──────────────┬──────────────┬────┘
           │              │              │
           │              │              │
           ▼              ▼              ▼
    ┌──────────┐   ┌──────────┐   ┌──────────┐
    │  Remote  │   │  Remote  │   │  Remote  │
    │ Product  │   │   Cart   │   │ Checkout │
    └──────────┘   └──────────┘   └──────────┘
```

**Implementation:**

```typescript
// apps/shell/next.config.js
const { NextFederationPlugin } = require('@module-federation/nextjs-mf');

module.exports = {
  webpack(config, options) {
    if (!options.isServer) {
      config.plugins.push(
        new NextFederationPlugin({
          name: 'shell',
          filename: 'static/chunks/remoteEntry.js',
          remotes: {
            product: `product@http://localhost:3001/_next/static/chunks/remoteEntry.js`,
            cart: `cart@http://localhost:3002/_next/static/chunks/remoteEntry.js`,
            checkout: `checkout@http://localhost:3003/_next/static/chunks/remoteEntry.js`,
          },
          shared: {
            react: { singleton: true, eager: true },
            'react-dom': { singleton: true, eager: true },
          },
        })
      );
    }
    return config;
  },
};

// apps/product/next.config.js
module.exports = {
  webpack(config, options) {
    if (!options.isServer) {
      config.plugins.push(
        new NextFederationPlugin({
          name: 'product',
          filename: 'static/chunks/remoteEntry.js',
          exposes: {
            './ProductList': './components/ProductList',
            './ProductDetail': './components/ProductDetail',
          },
          shared: {
            react: { singleton: true, eager: true },
            'react-dom': { singleton: true, eager: true },
          },
        })
      );
    }
    return config;
  },
};

// apps/shell/components/DynamicRemote.tsx
import dynamic from 'next/dynamic';
import { Suspense } from 'react';

const ProductList = dynamic(
  () => import('product/ProductList').then(mod => mod.ProductList),
  {
    ssr: false,
    loading: () => <div>Loading products...</div>,
  }
);

export function ShopPage() {
  return (
    <div>
      <h1>Shop</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <ProductList />
      </Suspense>
    </div>
  );
}
```

**Communication Between Micro-Frontends:**

```typescript
// Shared event bus
class EventBus {
  private events: Map<string, Function[]> = new Map();

  subscribe(event: string, callback: Function) {
    if (!this.events.has(event)) {
      this.events.set(event, []);
    }
    this.events.get(event)!.push(callback);
    
    return () => {
      const callbacks = this.events.get(event);
      if (callbacks) {
        const index = callbacks.indexOf(callback);
        if (index > -1) {
          callbacks.splice(index, 1);
        }
      }
    };
  }

  publish(event: string, data?: any) {
    const callbacks = this.events.get(event);
    if (callbacks) {
      callbacks.forEach(callback => callback(data));
    }
  }
}

export const eventBus = new EventBus();

// Product remote
eventBus.publish('product:added', { productId: '123', quantity: 1 });

// Cart remote
eventBus.subscribe('product:added', (data) => {
  addToCart(data.productId, data.quantity);
});
```

**Trade-offs:**

1. **Pros:**
   - Independent deployment
   - Team autonomy
   - Technology diversity possible
   - Scalable architecture

2. **Cons:**
   - Complex setup
   - Shared dependency management
   - Version conflicts
   - Performance overhead
   - Debugging complexity

3. **When to Use:**
   - Large teams (50+ developers)
   - Multiple products/domains
   - Need independent deployments
   - Long-term product

4. **When NOT to Use:**
   - Small team (< 10 developers)
   - Single product
   - Tight coupling required
   - Performance critical

---

## Architectural Decision Records (ADRs)

### ADR Template

```markdown
# ADR-001: Choose State Management Solution

## Status
Accepted

## Context
We need to select a state management solution for our Next.js application that:
- Handles server state (API data)
- Manages client state (UI state)
- Supports 10k+ users
- Provides good developer experience
- Has minimal bundle impact

Our application has:
- 50+ API endpoints
- Real-time features (WebSocket)
- Complex forms
- User preferences
- Shopping cart

## Decision
We will use a combination of:
1. **React Query** for server state
2. **Zustand** for client state
3. **URL params** for shareable state

## Rationale

### Why React Query?
- ✅ Built-in caching, refetching, pagination
- ✅ Automatic background updates
- ✅ Request deduplication
- ✅ Optimistic updates support
- ✅ 13KB gzipped
- ✅ Excellent TypeScript support

### Why Zustand?
- ✅ Minimal boilerplate (vs Redux)
- ✅ 3KB gzipped
- ✅ Simple API
- ✅ Good DevTools
- ✅ No provider needed

### Why URL params?
- ✅ Shareable state (filters, pagination)
- ✅ Deep linking support
- ✅ Browser back/forward works
- ✅ SEO benefits

### Alternatives Considered

**Redux Toolkit:**
- ❌ 11KB gzipped (larger)
- ❌ More boilerplate
- ❌ Overkill for server state
- ✅ Good for complex state logic
- ✅ Excellent DevTools

**Jotai:**
- ✅ 3KB gzipped
- ✅ Atomic state
- ❌ Less mature ecosystem
- ❌ Learning curve

**SWR:**
- ✅ Similar to React Query
- ❌ Less features
- ❌ Smaller community

## Consequences

### Positive
- Reduced API calls due to caching
- Better UX with optimistic updates
- Smaller bundle size
- Faster development
- Better testing (mock stores easily)

### Negative
- Learning curve for team
- Two state systems to learn
- Need to decide: server vs client state
- Migration cost from current Redux

### Mitigation
- Training sessions for team
- Documentation with examples
- Gradual migration (coexist with Redux)
- Clear guidelines: when to use what

## Implementation Plan

1. Week 1: Setup React Query + Zustand
2. Week 2: Migrate auth state to Zustand
3. Week 3: Migrate user data to React Query
4. Week 4: Migrate products to React Query
5. Week 5: Remove Redux dependencies

## Metrics
- Bundle size: Target < 500KB
- Time to Interactive: < 3s
- API calls: Reduce by 40%
- Developer velocity: 20% faster

## References
- [React Query Docs](https://tanstack.com/query)
- [Zustand Docs](https://zustand-demo.pmnd.rs/)
- [State Management Comparison](#)

## Date
2024-01-15

## Authors
- @john-doe (Tech Lead)
- @jane-smith (Senior Engineer)
```

---

### ADR-002: Database Strategy for Multi-Tenant Application

```markdown
# ADR-002: Multi-Tenant Database Strategy

## Status
Accepted

## Context
We're building a B2B SaaS platform with:
- 10,000+ expected tenants
- Strong data isolation requirements
- Compliance: GDPR, SOC2
- Performance: < 200ms response time
- Cost: Budget for infrastructure

Need to decide on database architecture:
1. Separate database per tenant
2. Separate schema per tenant
3. Shared database with tenant_id column

## Decision
**Shared database with tenant_id column + Row Level Security (RLS)**

## Rationale

### Approach 1: Separate Database
```
Pros:
✅ Maximum isolation
✅ Easy backups per tenant
✅ Custom optimizations per tenant

Cons:
❌ 10,000 databases = management nightmare
❌ Connection pool per DB
❌ Expensive ($$$)
❌ Difficult migrations
❌ Hard to aggregate data
```

### Approach 2: Separate Schema
```
Pros:
✅ Good isolation
✅ Simpler than separate DB

Cons:
❌ 10,000 schemas
❌ Still complex management
❌ Connection overhead
❌ Expensive ($$)
```

### Approach 3: Shared DB with RLS (CHOSEN)
```
Pros:
✅ Simple management
✅ One connection pool
✅ Cost effective ($)
✅ Easy migrations
✅ Easy analytics
✅ PostgreSQL RLS for security

Cons:
❌ Need careful query design
❌ Risk of data leakage (mitigated by RLS)
❌ One tenant can impact others
```

## Implementation

### Database Schema
```sql
-- Enable RLS
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Policy
CREATE POLICY tenant_isolation ON projects
  USING (tenant_id = current_setting('app.tenant_id')::uuid);

-- Set context per request
SET LOCAL app.tenant_id = 'tenant-uuid';
```

### Application Layer
```typescript
// Middleware sets tenant context
async function middleware(req, res, next) {
  const tenantId = req.headers['x-tenant-id'];
  
  await prisma.$executeRaw`
    SET LOCAL app.tenant_id = ${tenantId}
  `;
  
  next();
}

// All queries automatically filtered
const projects = await prisma.project.findMany();
// Only returns projects for current tenant
```

### Safety Measures
1. **RLS Policies**: Database-level enforcement
2. **Middleware**: Application-level checks
3. **Testing**: Tenant isolation tests
4. **Monitoring**: Alert on cross-tenant queries
5. **Auditing**: Log all data access

## Consequences

### Positive
- $50K/year savings vs separate databases
- Faster migrations (1 DB vs 10,000)
- Easier monitoring
- Better resource utilization

### Negative
- Must enforce tenant_id in all queries
- Potential noisy neighbor
- Single point of failure (mitigated by replicas)

### Mitigation Strategies
1. **Connection Pooling**: PgBouncer with transaction mode
2. **Query Optimization**: Indexes on tenant_id
3. **Resource Isolation**: 
   - CPU/Memory limits per tenant
   - Rate limiting
4. **Monitoring**:
   - Query performance per tenant
   - Alert on slow queries
5. **Backups**:
   - Point-in-time recovery
   - Per-tenant restore capability

## Rollback Plan
If issues arise:
1. Create separate schema for problematic tenants
2. Move data using pg_dump/restore
3. Update routing logic
4. Gradual migration if needed

## Metrics
- Query performance: < 200ms p95
- Resource usage: < 70% CPU
- Cost: $5,000/month (vs $55,000 separate DBs)
- Incident rate: < 1 per month

## Decision Drivers
1. Cost: 90% savings
2. Simplicity: 1 DB vs 10,000
3. Performance: Adequate with optimization
4. Security: RLS provides strong isolation
5. Scale: Proven at 10k+ tenants (Slack, GitHub)

## Validation
After 6 months, review:
- Performance metrics
- Security incidents
- Cost savings
- Developer feedback

If metrics don't meet goals, revisit decision.

## References
- [PostgreSQL RLS Documentation](#)
- [Multi-Tenant Architecture Patterns](#)
- [Case Study: How Segment Does Multi-Tenancy](#)

## Date
2024-01-20

## Authors
- @john-doe (Tech Lead)
- @sarah-wilson (DBA)
- @mike-jones (Security Lead)
```

---

### ADR-003: Choose Monorepo Tool

```markdown
# ADR-003: Monorepo Tooling Selection

## Status
Accepted

## Context
We have:
- 5 Next.js applications
- 15 shared packages
- 30 developers
- CI/CD pipeline
- Need for remote caching

Options:
1. Turborepo
2. Nx
3. Lerna
4. Rush
5. No tool (just pnpm workspaces)

## Decision
**Turborepo** for our monorepo

## Comparison Matrix

| Feature | Turborepo | Nx | Lerna | Rush |
|---------|-----------|----|----|------|
| Speed | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| Caching | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| Remote Cache | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ | ⭐⭐⭐ |
| Simplicity | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| Features | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| Learning Curve | Easy | Medium | Easy | Hard |
| Community | Large | Large | Medium | Small |

## Rationale

### Why Turborepo?
✅ **Speed**: 85% faster builds (benchmark)
✅ **Simple**: Minimal config (turbo.json)
✅ **Remote Caching**: Free with Vercel
✅ **Dev Experience**: Excellent error messages
✅ **Integration**: Works great with Next.js
✅ **Cost**: Free remote cache (vs Nx Cloud $$$)

### Why NOT Nx?
❌ More complex (generators, plugins)
❌ Steeper learning curve
❌ Nx Cloud costs $25/dev/month
✅ More features (if needed later, can migrate)

### Why NOT Lerna?
❌ Maintenance mode (less active)
❌ No remote caching
❌ Slower than modern tools

## Implementation

```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "dist/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "lint": {},
    "test": {
      "dependsOn": ["^build"],
      "outputs": ["coverage/**"]
    }
  }
}
```

```bash
# Commands
turbo run build --filter=web
turbo run test --filter=...web
turbo run dev --parallel
```

## Consequences

### Positive
- 85% faster CI builds (cached)
- $300/month savings (vs Nx Cloud)
- Easy onboarding (simple config)
- Great Vercel integration

### Negative
- Fewer features than Nx
- No built-in generators
- Less mature plugin ecosystem

### Mitigation
- Create custom generators as needed
- Use Nx if we need advanced features
- Document patterns for common tasks

## Metrics (After 3 Months)
- CI build time: 15min → 3min (80% reduction)
- Cache hit rate: 75%
- Developer satisfaction: 9/10
- Cost: $0 (Vercel Free tier)

## Migration Path to Nx (If Needed)
1. Keep package structure
2. Add nx.json
3. Run both tools in parallel
4. Gradual migration
5. Remove Turborepo config

## References
- [Turborepo Docs](https://turbo.build)
- [Monorepo Comparison](https://monorepo.tools)
- [Vercel Remote Cache](https://vercel.com/docs/monorepos/remote-caching)

## Date
2024-02-01

## Authors
- @tech-lead
- @senior-dev
```

---

### How to Write Good ADRs

**1. Keep It Concise**
- Max 2-3 pages
- Focus on the decision
- Link to detailed docs

**2. Be Specific**
- Include numbers, metrics
- Concrete examples
- Real constraints

**3. Show Alternatives**
- What else was considered?
- Why were they rejected?
- Comparison matrix

**4. Explain Trade-offs**
- Nothing is perfect
- What are we giving up?
- How do we mitigate?

**5. Make It Actionable**
- Implementation plan
- Success metrics
- Review timeline

**6. Update Status**
- Draft → Proposed → Accepted → Superseded
- Link to related ADRs
- Note if deprecated

---

This comprehensive guide covers whiteboarding scenarios, monorepo architecture, micro-frontends, and ADRs with real-world examples and trade-off analysis! 🚀

