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

(Continuing with remaining sections in next message due to length...)

