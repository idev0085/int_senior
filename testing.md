# JavaScript Testing - Complete Guide

## Table of Contents
- [Testing Strategies Overview](#testing-strategies-overview)
- [Unit Testing](#unit-testing)
- [Integration Testing](#integration-testing)
- [End-to-End (E2E) Testing](#end-to-end-e2e-testing)
- [React Testing Library Best Practices](#react-testing-library-best-practices)
- [MSW - Mock Service Worker](#msw---mock-service-worker)
- [Playwright](#playwright)
- [Cypress](#cypress)
- [Testing Patterns & Best Practices](#testing-patterns--best-practices)

---

## Testing Strategies Overview

### The Testing Pyramid

```
        /\
       /E2E\      Few, slow, expensive
      /------\    
     /  INT  \    Some, medium speed
    /--------\ 
   /   UNIT   \   Many, fast, cheap
  /------------\
```

**Unit Tests (70%):**
- Test individual functions, components, classes in isolation
- Fast execution, easy to debug
- High code coverage

**Integration Tests (20%):**
- Test multiple units working together
- Test component integration with services, APIs
- Medium execution time

**E2E Tests (10%):**
- Test complete user workflows
- Test from UI to database
- Slow execution, harder to maintain

---

## Unit Testing

### Setting Up Jest

```bash
npm install --save-dev jest @types/jest
npm install --save-dev @testing-library/react @testing-library/jest-dom
npm install --save-dev @testing-library/user-event
```

**jest.config.js:**

```javascript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapper: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
    '\\.(jpg|jpeg|png|gif|svg)$': '<rootDir>/__mocks__/fileMock.js'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/**/*.stories.tsx',
    '!src/index.tsx'
  ],
  coverageThresholds: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  testMatch: [
    '<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}',
    '<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}'
  ]
};
```

**jest.setup.js:**

```javascript
import '@testing-library/jest-dom';

// Mock window.matchMedia
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});

// Mock IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
  constructor() {}
  disconnect() {}
  observe() {}
  takeRecords() {
    return [];
  }
  unobserve() {}
};
```

---

### Testing Pure Functions

```typescript
// utils/math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}

export function divide(a: number, b: number): number {
  if (b === 0) {
    throw new Error('Cannot divide by zero');
  }
  return a / b;
}

export function calculateDiscount(price: number, discountPercent: number): number {
  if (price < 0 || discountPercent < 0 || discountPercent > 100) {
    throw new Error('Invalid input');
  }
  return price - (price * discountPercent / 100);
}
```

**utils/math.test.ts:**

```typescript
import { add, multiply, divide, calculateDiscount } from './math';

describe('Math utilities', () => {
  describe('add', () => {
    it('should add two positive numbers', () => {
      expect(add(2, 3)).toBe(5);
    });

    it('should add negative numbers', () => {
      expect(add(-2, -3)).toBe(-5);
    });

    it('should handle zero', () => {
      expect(add(0, 5)).toBe(5);
      expect(add(5, 0)).toBe(5);
    });
  });

  describe('multiply', () => {
    it('should multiply two numbers', () => {
      expect(multiply(2, 3)).toBe(6);
    });

    it('should handle zero', () => {
      expect(multiply(5, 0)).toBe(0);
    });
  });

  describe('divide', () => {
    it('should divide two numbers', () => {
      expect(divide(6, 2)).toBe(3);
    });

    it('should throw error when dividing by zero', () => {
      expect(() => divide(5, 0)).toThrow('Cannot divide by zero');
    });

    it('should handle decimal results', () => {
      expect(divide(5, 2)).toBe(2.5);
    });
  });

  describe('calculateDiscount', () => {
    it('should calculate 10% discount', () => {
      expect(calculateDiscount(100, 10)).toBe(90);
    });

    it('should calculate 50% discount', () => {
      expect(calculateDiscount(200, 50)).toBe(100);
    });

    it('should handle 0% discount', () => {
      expect(calculateDiscount(100, 0)).toBe(100);
    });

    it('should throw error for negative price', () => {
      expect(() => calculateDiscount(-100, 10)).toThrow('Invalid input');
    });

    it('should throw error for discount > 100', () => {
      expect(() => calculateDiscount(100, 150)).toThrow('Invalid input');
    });
  });
});
```

---

### Testing Async Functions

```typescript
// services/userService.ts
export interface User {
  id: string;
  name: string;
  email: string;
}

export async function fetchUser(userId: string): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
}

export async function createUser(user: Omit<User, 'id'>): Promise<User> {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(user)
  });
  
  if (!response.ok) {
    throw new Error('Failed to create user');
  }
  
  return response.json();
}

export async function retry<T>(
  fn: () => Promise<T>,
  retries: number = 3,
  delay: number = 1000
): Promise<T> {
  try {
    return await fn();
  } catch (error) {
    if (retries === 0) throw error;
    await new Promise(resolve => setTimeout(resolve, delay));
    return retry(fn, retries - 1, delay);
  }
}
```

**services/userService.test.ts:**

```typescript
import { fetchUser, createUser, retry } from './userService';

// Mock fetch globally
global.fetch = jest.fn();

describe('UserService', () => {
  beforeEach(() => {
    // Clear all mocks before each test
    jest.clearAllMocks();
  });

  describe('fetchUser', () => {
    it('should fetch user successfully', async () => {
      const mockUser = { id: '1', name: 'John', email: 'john@example.com' };
      
      (global.fetch as jest.Mock).mockResolvedValueOnce({
        ok: true,
        json: async () => mockUser
      });

      const result = await fetchUser('1');

      expect(result).toEqual(mockUser);
      expect(global.fetch).toHaveBeenCalledWith('/api/users/1');
      expect(global.fetch).toHaveBeenCalledTimes(1);
    });

    it('should throw error when fetch fails', async () => {
      (global.fetch as jest.Mock).mockResolvedValueOnce({
        ok: false
      });

      await expect(fetchUser('1')).rejects.toThrow('Failed to fetch user');
    });

    it('should handle network errors', async () => {
      (global.fetch as jest.Mock).mockRejectedValueOnce(
        new Error('Network error')
      );

      await expect(fetchUser('1')).rejects.toThrow('Network error');
    });
  });

  describe('createUser', () => {
    it('should create user successfully', async () => {
      const newUser = { name: 'Jane', email: 'jane@example.com' };
      const createdUser = { id: '2', ...newUser };

      (global.fetch as jest.Mock).mockResolvedValueOnce({
        ok: true,
        json: async () => createdUser
      });

      const result = await createUser(newUser);

      expect(result).toEqual(createdUser);
      expect(global.fetch).toHaveBeenCalledWith('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newUser)
      });
    });
  });

  describe('retry', () => {
    beforeEach(() => {
      jest.useFakeTimers();
    });

    afterEach(() => {
      jest.useRealTimers();
    });

    it('should succeed on first try', async () => {
      const mockFn = jest.fn().mockResolvedValue('success');
      
      const result = await retry(mockFn, 3, 1000);
      
      expect(result).toBe('success');
      expect(mockFn).toHaveBeenCalledTimes(1);
    });

    it('should retry on failure', async () => {
      const mockFn = jest
        .fn()
        .mockRejectedValueOnce(new Error('fail'))
        .mockRejectedValueOnce(new Error('fail'))
        .mockResolvedValue('success');

      const promise = retry(mockFn, 3, 1000);
      
      // Fast-forward timers
      jest.advanceTimersByTime(2000);
      
      const result = await promise;
      
      expect(result).toBe('success');
      expect(mockFn).toHaveBeenCalledTimes(3);
    });

    it('should throw after all retries exhausted', async () => {
      const mockFn = jest.fn().mockRejectedValue(new Error('fail'));

      const promise = retry(mockFn, 2, 1000);
      
      jest.advanceTimersByTime(3000);
      
      await expect(promise).rejects.toThrow('fail');
      expect(mockFn).toHaveBeenCalledTimes(3); // Initial + 2 retries
    });
  });
});
```

---

### Testing Classes

```typescript
// services/CartService.ts
export class CartService {
  private items: Map<string, { name: string; price: number; quantity: number }> = new Map();

  addItem(id: string, name: string, price: number, quantity: number = 1): void {
    if (quantity <= 0) {
      throw new Error('Quantity must be positive');
    }

    const existing = this.items.get(id);
    if (existing) {
      existing.quantity += quantity;
    } else {
      this.items.set(id, { name, price, quantity });
    }
  }

  removeItem(id: string): boolean {
    return this.items.delete(id);
  }

  updateQuantity(id: string, quantity: number): void {
    if (quantity <= 0) {
      throw new Error('Quantity must be positive');
    }

    const item = this.items.get(id);
    if (!item) {
      throw new Error('Item not found');
    }

    item.quantity = quantity;
  }

  getTotal(): number {
    let total = 0;
    for (const item of this.items.values()) {
      total += item.price * item.quantity;
    }
    return total;
  }

  getItemCount(): number {
    let count = 0;
    for (const item of this.items.values()) {
      count += item.quantity;
    }
    return count;
  }

  clear(): void {
    this.items.clear();
  }

  getItems() {
    return Array.from(this.items.entries()).map(([id, item]) => ({
      id,
      ...item
    }));
  }
}
```

**services/CartService.test.ts:**

```typescript
import { CartService } from './CartService';

describe('CartService', () => {
  let cart: CartService;

  beforeEach(() => {
    cart = new CartService();
  });

  describe('addItem', () => {
    it('should add new item to cart', () => {
      cart.addItem('1', 'Laptop', 999, 1);

      expect(cart.getItemCount()).toBe(1);
      expect(cart.getTotal()).toBe(999);
    });

    it('should increment quantity for existing item', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      cart.addItem('1', 'Laptop', 999, 2);

      expect(cart.getItemCount()).toBe(3);
      expect(cart.getTotal()).toBe(2997);
    });

    it('should throw error for invalid quantity', () => {
      expect(() => cart.addItem('1', 'Laptop', 999, 0)).toThrow(
        'Quantity must be positive'
      );
      expect(() => cart.addItem('1', 'Laptop', 999, -1)).toThrow(
        'Quantity must be positive'
      );
    });

    it('should add multiple different items', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      cart.addItem('2', 'Mouse', 25, 2);

      expect(cart.getItemCount()).toBe(3);
      expect(cart.getTotal()).toBe(1049);
    });
  });

  describe('removeItem', () => {
    it('should remove item from cart', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      const removed = cart.removeItem('1');

      expect(removed).toBe(true);
      expect(cart.getItemCount()).toBe(0);
    });

    it('should return false for non-existent item', () => {
      const removed = cart.removeItem('999');

      expect(removed).toBe(false);
    });
  });

  describe('updateQuantity', () => {
    beforeEach(() => {
      cart.addItem('1', 'Laptop', 999, 1);
    });

    it('should update item quantity', () => {
      cart.updateQuantity('1', 3);

      expect(cart.getItemCount()).toBe(3);
      expect(cart.getTotal()).toBe(2997);
    });

    it('should throw error for invalid quantity', () => {
      expect(() => cart.updateQuantity('1', 0)).toThrow(
        'Quantity must be positive'
      );
    });

    it('should throw error for non-existent item', () => {
      expect(() => cart.updateQuantity('999', 5)).toThrow('Item not found');
    });
  });

  describe('getTotal', () => {
    it('should return 0 for empty cart', () => {
      expect(cart.getTotal()).toBe(0);
    });

    it('should calculate correct total', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      cart.addItem('2', 'Mouse', 25, 2);
      cart.addItem('3', 'Keyboard', 75, 1);

      expect(cart.getTotal()).toBe(1124);
    });
  });

  describe('clear', () => {
    it('should clear all items', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      cart.addItem('2', 'Mouse', 25, 2);

      cart.clear();

      expect(cart.getItemCount()).toBe(0);
      expect(cart.getTotal()).toBe(0);
    });
  });

  describe('getItems', () => {
    it('should return empty array for empty cart', () => {
      expect(cart.getItems()).toEqual([]);
    });

    it('should return all items with ids', () => {
      cart.addItem('1', 'Laptop', 999, 1);
      cart.addItem('2', 'Mouse', 25, 2);

      const items = cart.getItems();

      expect(items).toHaveLength(2);
      expect(items).toEqual(
        expect.arrayContaining([
          { id: '1', name: 'Laptop', price: 999, quantity: 1 },
          { id: '2', name: 'Mouse', price: 25, quantity: 2 }
        ])
      );
    });
  });
});
```

---

## React Testing Library Best Practices

### Core Principles

1. **Test behavior, not implementation**
2. **Query by accessibility attributes**
3. **User-centric testing**
4. **Avoid testing internal state**

### Query Priority

```typescript
// ✅ 1. Queries accessible to everyone (best)
getByRole('button', { name: /submit/i })
getByLabelText(/username/i)
getByPlaceholderText(/enter your email/i)
getByText(/welcome/i)
getByDisplayValue(/john/i)

// ✅ 2. Semantic queries (good)
getByAltText(/profile picture/i)
getByTitle(/close/i)

// ⚠️ 3. Test IDs (last resort)
getByTestId('submit-button')
```

---

### Testing React Components

```typescript
// components/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  loading?: boolean;
  children: ReactNode;
}

export function Button({
  variant = 'primary',
  size = 'md',
  loading = false,
  disabled,
  children,
  ...props
}: ButtonProps) {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled || loading}
      aria-busy={loading}
      {...props}
    >
      {loading ? 'Loading...' : children}
    </button>
  );
}
```

**components/Button.test.tsx:**

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  it('should render with text', () => {
    render(<Button>Click me</Button>);
    
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('should call onClick when clicked', async () => {
    const user = userEvent.setup();
    const handleClick = jest.fn();
    
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await user.click(screen.getByRole('button', { name: /click me/i }));
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('should be disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>);
    
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('should show loading state', () => {
    render(<Button loading>Click me</Button>);
    
    const button = screen.getByRole('button');
    
    expect(button).toHaveTextContent('Loading...');
    expect(button).toBeDisabled();
    expect(button).toHaveAttribute('aria-busy', 'true');
  });

  it('should apply correct classes for variant', () => {
    const { rerender } = render(<Button variant="primary">Click me</Button>);
    expect(screen.getByRole('button')).toHaveClass('btn-primary');

    rerender(<Button variant="danger">Click me</Button>);
    expect(screen.getByRole('button')).toHaveClass('btn-danger');
  });

  it('should not be clickable when loading', async () => {
    const user = userEvent.setup();
    const handleClick = jest.fn();
    
    render(<Button loading onClick={handleClick}>Click me</Button>);
    
    await user.click(screen.getByRole('button'));
    
    expect(handleClick).not.toHaveBeenCalled();
  });
});
```

---

### Testing Forms

```typescript
// components/LoginForm.tsx
import { useState, FormEvent } from 'react';

interface LoginFormProps {
  onSubmit: (email: string, password: string) => Promise<void>;
}

export function LoginForm({ onSubmit }: LoginFormProps) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setError('');
    setLoading(true);

    try {
      await onSubmit(email, password);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Login failed');
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} aria-label="Login form">
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>

      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
      </div>

      {error && <div role="alert">{error}</div>}

      <button type="submit" disabled={loading}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
}
```

**components/LoginForm.test.tsx:**

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('should render all form fields', () => {
    render(<LoginForm onSubmit={jest.fn()} />);

    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /login/i })).toBeInTheDocument();
  });

  it('should update input values when typing', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={jest.fn()} />);

    const emailInput = screen.getByLabelText(/email/i);
    const passwordInput = screen.getByLabelText(/password/i);

    await user.type(emailInput, 'test@example.com');
    await user.type(passwordInput, 'password123');

    expect(emailInput).toHaveValue('test@example.com');
    expect(passwordInput).toHaveValue('password123');
  });

  it('should call onSubmit with form data', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn().mockResolvedValue(undefined);
    
    render(<LoginForm onSubmit={handleSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /login/i }));

    await waitFor(() => {
      expect(handleSubmit).toHaveBeenCalledWith('test@example.com', 'password123');
    });
  });

  it('should show loading state during submission', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn(
      () => new Promise(resolve => setTimeout(resolve, 100))
    );
    
    render(<LoginForm onSubmit={handleSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /login/i }));

    expect(screen.getByRole('button')).toHaveTextContent('Logging in...');
    expect(screen.getByRole('button')).toBeDisabled();

    await waitFor(() => {
      expect(screen.getByRole('button')).toHaveTextContent('Login');
    });
  });

  it('should display error message on submission failure', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn().mockRejectedValue(
      new Error('Invalid credentials')
    );
    
    render(<LoginForm onSubmit={handleSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'wrong');
    await user.click(screen.getByRole('button', { name: /login/i }));

    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Invalid credentials');
    });
  });

  it('should clear error when resubmitting', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest
      .fn()
      .mockRejectedValueOnce(new Error('Invalid credentials'))
      .mockResolvedValueOnce(undefined);
    
    render(<LoginForm onSubmit={handleSubmit} />);

    // First submission - fail
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'wrong');
    await user.click(screen.getByRole('button', { name: /login/i }));

    await waitFor(() => {
      expect(screen.getByRole('alert')).toBeInTheDocument();
    });

    // Second submission - success
    await user.clear(screen.getByLabelText(/password/i));
    await user.type(screen.getByLabelText(/password/i), 'correct');
    await user.click(screen.getByRole('button', { name: /login/i }));

    await waitFor(() => {
      expect(screen.queryByRole('alert')).not.toBeInTheDocument();
    });
  });
});
```

---

### Testing with Context

```typescript
// context/ThemeContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

type Theme = 'light' | 'dark';

interface ThemeContextValue {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextValue | null>(null);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
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

// components/ThemeToggle.tsx
export function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();

  return (
    <button onClick={toggleTheme}>
      Current theme: {theme}
    </button>
  );
}
```

**components/ThemeToggle.test.tsx:**

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ThemeProvider } from '../context/ThemeContext';
import { ThemeToggle } from './ThemeToggle';

// Helper to render with context
function renderWithTheme(ui: React.ReactElement) {
  return render(<ThemeProvider>{ui}</ThemeProvider>);
}

describe('ThemeToggle', () => {
  it('should render with initial theme', () => {
    renderWithTheme(<ThemeToggle />);

    expect(screen.getByRole('button')).toHaveTextContent('Current theme: light');
  });

  it('should toggle theme when clicked', async () => {
    const user = userEvent.setup();
    renderWithTheme(<ThemeToggle />);

    const button = screen.getByRole('button');
    
    expect(button).toHaveTextContent('light');

    await user.click(button);
    expect(button).toHaveTextContent('dark');

    await user.click(button);
    expect(button).toHaveTextContent('light');
  });

  it('should throw error when used outside provider', () => {
    // Suppress console.error for this test
    const spy = jest.spyOn(console, 'error').mockImplementation();

    expect(() => render(<ThemeToggle />)).toThrow(
      'useTheme must be used within ThemeProvider'
    );

    spy.mockRestore();
  });
});
```

---

### Testing Custom Hooks

```typescript
// hooks/useCounter.ts
import { useState, useCallback } from 'react';

export function useCounter(initialValue: number = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  const decrement = useCallback(() => {
    setCount(c => c - 1);
  }, []);

  const reset = useCallback(() => {
    setCount(initialValue);
  }, [initialValue]);

  return { count, increment, decrement, reset };
}
```

**hooks/useCounter.test.ts:**

```typescript
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter());

    expect(result.current.count).toBe(0);
  });

  it('should initialize with custom value', () => {
    const { result } = renderHook(() => useCounter(10));

    expect(result.current.count).toBe(10);
  });

  it('should increment count', () => {
    const { result } = renderHook(() => useCounter(0));

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(2);
  });

  it('should decrement count', () => {
    const { result } = renderHook(() => useCounter(5));

    act(() => {
      result.current.decrement();
    });

    expect(result.current.count).toBe(4);
  });

  it('should reset to initial value', () => {
    const { result } = renderHook(() => useCounter(10));

    act(() => {
      result.current.increment();
      result.current.increment();
    });

    expect(result.current.count).toBe(12);

    act(() => {
      result.current.reset();
    });

    expect(result.current.count).toBe(10);
  });

  it('should reset to new initial value when it changes', () => {
    const { result, rerender } = renderHook(
      ({ initialValue }) => useCounter(initialValue),
      { initialProps: { initialValue: 5 } }
    );

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(6);

    rerender({ initialValue: 20 });

    act(() => {
      result.current.reset();
    });

    expect(result.current.count).toBe(20);
  });
});
```

---

## MSW - Mock Service Worker

### Setting Up MSW

```bash
npm install --save-dev msw
npx msw init public/ --save
```

**mocks/handlers.ts:**

```typescript
import { http, HttpResponse } from 'msw';

export interface User {
  id: string;
  name: string;
  email: string;
}

export interface Post {
  id: string;
  title: string;
  content: string;
  authorId: string;
}

// Mock data
const users: User[] = [
  { id: '1', name: 'John Doe', email: 'john@example.com' },
  { id: '2', name: 'Jane Smith', email: 'jane@example.com' }
];

const posts: Post[] = [
  { id: '1', title: 'First Post', content: 'Hello World', authorId: '1' },
  { id: '2', title: 'Second Post', content: 'MSW is great', authorId: '1' }
];

export const handlers = [
  // GET /api/users
  http.get('/api/users', () => {
    return HttpResponse.json(users);
  }),

  // GET /api/users/:id
  http.get('/api/users/:id', ({ params }) => {
    const { id } = params;
    const user = users.find(u => u.id === id);

    if (!user) {
      return new HttpResponse(null, { status: 404 });
    }

    return HttpResponse.json(user);
  }),

  // POST /api/users
  http.post('/api/users', async ({ request }) => {
    const newUser = await request.json() as Omit<User, 'id'>;
    const user: User = {
      id: String(users.length + 1),
      ...newUser
    };
    users.push(user);

    return HttpResponse.json(user, { status: 201 });
  }),

  // PUT /api/users/:id
  http.put('/api/users/:id', async ({ params, request }) => {
    const { id } = params;
    const updates = await request.json() as Partial<User>;
    const userIndex = users.findIndex(u => u.id === id);

    if (userIndex === -1) {
      return new HttpResponse(null, { status: 404 });
    }

    users[userIndex] = { ...users[userIndex], ...updates };
    return HttpResponse.json(users[userIndex]);
  }),

  // DELETE /api/users/:id
  http.delete('/api/users/:id', ({ params }) => {
    const { id } = params;
    const index = users.findIndex(u => u.id === id);

    if (index === -1) {
      return new HttpResponse(null, { status: 404 });
    }

    users.splice(index, 1);
    return new HttpResponse(null, { status: 204 });
  }),

  // GET /api/posts
  http.get('/api/posts', ({ request }) => {
    const url = new URL(request.url);
    const authorId = url.searchParams.get('authorId');

    if (authorId) {
      const filtered = posts.filter(p => p.authorId === authorId);
      return HttpResponse.json(filtered);
    }

    return HttpResponse.json(posts);
  }),

  // Error simulation
  http.get('/api/error', () => {
    return new HttpResponse(null, { status: 500 });
  }),

  // Delayed response
  http.get('/api/slow', async () => {
    await new Promise(resolve => setTimeout(resolve, 2000));
    return HttpResponse.json({ message: 'Slow response' });
  })
];
```

**mocks/server.ts:**

```typescript
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

**jest.setup.js:**

```javascript
import '@testing-library/jest-dom';
import { server } from './mocks/server';

// Establish API mocking before all tests
beforeAll(() => server.listen());

// Reset any request handlers that are declared in a test
afterEach(() => server.resetHandlers());

// Clean up after tests are finished
afterAll(() => server.close());
```

---

### Testing with MSW

```typescript
// components/UserList.tsx
import { useEffect, useState } from 'react';

interface User {
  id: string;
  name: string;
  email: string;
}

export function UserList() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch('/api/users')
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch users');
        return res.json();
      })
      .then(data => {
        setUsers(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div role="alert">Error: {error}</div>;

  return (
    <ul aria-label="Users">
      {users.map(user => (
        <li key={user.id}>
          {user.name} - {user.email}
        </li>
      ))}
    </ul>
  );
}
```

**components/UserList.test.tsx:**

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import { http, HttpResponse } from 'msw';
import { server } from '../mocks/server';
import { UserList } from './UserList';

describe('UserList', () => {
  it('should display loading state initially', () => {
    render(<UserList />);
    
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });

  it('should display users after loading', async () => {
    render(<UserList />);

    await waitFor(() => {
      expect(screen.queryByText(/loading/i)).not.toBeInTheDocument();
    });

    expect(screen.getByText(/john doe/i)).toBeInTheDocument();
    expect(screen.getByText(/jane smith/i)).toBeInTheDocument();
  });

  it('should display error when fetch fails', async () => {
    // Override handler for this test
    server.use(
      http.get('/api/users', () => {
        return new HttpResponse(null, { status: 500 });
      })
    );

    render(<UserList />);

    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Failed to fetch users');
    });
  });

  it('should display network error', async () => {
    server.use(
      http.get('/api/users', () => {
        return HttpResponse.error();
      })
    );

    render(<UserList />);

    await waitFor(() => {
      expect(screen.getByRole('alert')).toBeInTheDocument();
    });
  });

  it('should handle empty user list', async () => {
    server.use(
      http.get('/api/users', () => {
        return HttpResponse.json([]);
      })
    );

    render(<UserList />);

    await waitFor(() => {
      const list = screen.getByLabelText('Users');
      expect(list.children).toHaveLength(0);
    });
  });
});
```

---

### Testing POST Requests with MSW

```typescript
// components/UserForm.tsx
import { useState, FormEvent } from 'react';

interface UserFormProps {
  onSuccess: (user: { id: string; name: string; email: string }) => void;
}

export function UserForm({ onSuccess }: UserFormProps) {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();
    setLoading(true);
    setError('');

    try {
      const response = await fetch('/api/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      });

      if (!response.ok) {
        throw new Error('Failed to create user');
      }

      const user = await response.json();
      onSuccess(user);
      setName('');
      setEmail('');
    } catch (err) {
      setError(err instanceof Error ? err.message : 'An error occurred');
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="name">Name</label>
        <input
          id="name"
          value={name}
          onChange={(e) => setName(e.target.value)}
          required
        />
      </div>

      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>

      {error && <div role="alert">{error}</div>}

      <button type="submit" disabled={loading}>
        {loading ? 'Creating...' : 'Create User'}
      </button>
    </form>
  );
}
```

**components/UserForm.test.tsx:**

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { http, HttpResponse } from 'msw';
import { server } from '../mocks/server';
import { UserForm } from './UserForm';

describe('UserForm', () => {
  it('should create user successfully', async () => {
    const user = userEvent.setup();
    const handleSuccess = jest.fn();
    
    render(<UserForm onSuccess={handleSuccess} />);

    await user.type(screen.getByLabelText(/name/i), 'Alice Brown');
    await user.type(screen.getByLabelText(/email/i), 'alice@example.com');
    await user.click(screen.getByRole('button', { name: /create user/i }));

    await waitFor(() => {
      expect(handleSuccess).toHaveBeenCalledWith({
        id: expect.any(String),
        name: 'Alice Brown',
        email: 'alice@example.com'
      });
    });

    // Form should be cleared
    expect(screen.getByLabelText(/name/i)).toHaveValue('');
    expect(screen.getByLabelText(/email/i)).toHaveValue('');
  });

  it('should display error on failure', async () => {
    const user = userEvent.setup();
    
    server.use(
      http.post('/api/users', () => {
        return new HttpResponse(null, { status: 500 });
      })
    );

    render(<UserForm onSuccess={jest.fn()} />);

    await user.type(screen.getByLabelText(/name/i), 'Alice Brown');
    await user.type(screen.getByLabelText(/email/i), 'alice@example.com');
    await user.click(screen.getByRole('button', { name: /create user/i }));

    await waitFor(() => {
      expect(screen.getByRole('alert')).toHaveTextContent('Failed to create user');
    });
  });

  it('should show loading state during submission', async () => {
    const user = userEvent.setup();
    
    server.use(
      http.post('/api/users', async () => {
        await new Promise(resolve => setTimeout(resolve, 100));
        return HttpResponse.json({ id: '3', name: 'Test', email: 'test@example.com' });
      })
    );

    render(<UserForm onSuccess={jest.fn()} />);

    await user.type(screen.getByLabelText(/name/i), 'Test User');
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.click(screen.getByRole('button', { name: /create user/i }));

    expect(screen.getByRole('button')).toHaveTextContent('Creating...');
    expect(screen.getByRole('button')).toBeDisabled();

    await waitFor(() => {
      expect(screen.getByRole('button')).toHaveTextContent('Create User');
    });
  });
});
```

---

## Integration Testing

### Testing Component Integration

```typescript
// pages/UserManagement.tsx
import { useState, useEffect } from 'react';
import { UserList } from '../components/UserList';
import { UserForm } from '../components/UserForm';

interface User {
  id: string;
  name: string;
  email: string;
}

export function UserManagement() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    loadUsers();
  }, []);

  const loadUsers = async () => {
    setLoading(true);
    try {
      const response = await fetch('/api/users');
      const data = await response.json();
      setUsers(data);
    } catch (error) {
      console.error('Failed to load users:', error);
    } finally {
      setLoading(false);
    }
  };

  const handleUserCreated = (user: User) => {
    setUsers(prev => [...prev, user]);
  };

  const handleDeleteUser = async (id: string) => {
    try {
      await fetch(`/api/users/${id}`, { method: 'DELETE' });
      setUsers(prev => prev.filter(u => u.id !== id));
    } catch (error) {
      console.error('Failed to delete user:', error);
    }
  };

  if (loading) return <div>Loading users...</div>;

  return (
    <div>
      <h1>User Management</h1>
      
      <section aria-labelledby="create-user-heading">
        <h2 id="create-user-heading">Create New User</h2>
        <UserForm onSuccess={handleUserCreated} />
      </section>

      <section aria-labelledby="user-list-heading">
        <h2 id="user-list-heading">All Users</h2>
        <ul>
          {users.map(user => (
            <li key={user.id}>
              {user.name} - {user.email}
              <button onClick={() => handleDeleteUser(user.id)}>Delete</button>
            </li>
          ))}
        </ul>
      </section>
    </div>
  );
}
```

**pages/UserManagement.test.tsx:**

```typescript
import { render, screen, waitFor, within } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { UserManagement } from './UserManagement';

describe('UserManagement Integration', () => {
  it('should load and display users on mount', async () => {
    render(<UserManagement />);

    expect(screen.getByText(/loading users/i)).toBeInTheDocument();

    await waitFor(() => {
      expect(screen.queryByText(/loading users/i)).not.toBeInTheDocument();
    });

    expect(screen.getByText(/john doe/i)).toBeInTheDocument();
    expect(screen.getByText(/jane smith/i)).toBeInTheDocument();
  });

  it('should create and display new user', async () => {
    const user = userEvent.setup();
    render(<UserManagement />);

    // Wait for initial load
    await waitFor(() => {
      expect(screen.queryByText(/loading users/i)).not.toBeInTheDocument();
    });

    // Fill and submit form
    await user.type(screen.getByLabelText(/name/i), 'Bob Johnson');
    await user.type(screen.getByLabelText(/email/i), 'bob@example.com');
    await user.click(screen.getByRole('button', { name: /create user/i }));

    // New user should appear in list
    await waitFor(() => {
      expect(screen.getByText(/bob johnson/i)).toBeInTheDocument();
    });
  });

  it('should delete user from list', async () => {
    const user = userEvent.setup();
    render(<UserManagement />);

    await waitFor(() => {
      expect(screen.getByText(/john doe/i)).toBeInTheDocument();
    });

    // Find John Doe's list item and delete button
    const listSection = screen.getByLabelText(/all users/i);
    const johnItem = within(listSection).getByText(/john doe/i).closest('li');
    const deleteButton = within(johnItem!).getByRole('button', { name: /delete/i });

    await user.click(deleteButton);

    await waitFor(() => {
      expect(screen.queryByText(/john doe/i)).not.toBeInTheDocument();
    });

    // Jane should still be there
    expect(screen.getByText(/jane smith/i)).toBeInTheDocument();
  });

  it('should handle full user workflow', async () => {
    const user = userEvent.setup();
    render(<UserManagement />);

    // Wait for load
    await waitFor(() => {
      expect(screen.queryByText(/loading users/i)).not.toBeInTheDocument();
    });

    // Initial state - 2 users
    expect(screen.getAllByRole('button', { name: /delete/i })).toHaveLength(2);

    // Create new user
    await user.type(screen.getByLabelText(/name/i), 'Charlie Brown');
    await user.type(screen.getByLabelText(/email/i), 'charlie@example.com');
    await user.click(screen.getByRole('button', { name: /create user/i }));

    // Now 3 users
    await waitFor(() => {
      expect(screen.getAllByRole('button', { name: /delete/i })).toHaveLength(3);
    });

    // Delete one user
    const deleteButtons = screen.getAllByRole('button', { name: /delete/i });
    await user.click(deleteButtons[0]);

    // Back to 2 users
    await waitFor(() => {
      expect(screen.getAllByRole('button', { name: /delete/i })).toHaveLength(2);
    });
  });
});
```

---

## End-to-End (E2E) Testing

## Playwright

### Setting Up Playwright

```bash
npm init playwright@latest
```

**playwright.config.ts:**

```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },

  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

---

### Playwright Tests

**e2e/login.spec.ts:**

```typescript
import { test, expect } from '@playwright/test';

test.describe('Login Flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/login');
  });

  test('should display login form', async ({ page }) => {
    await expect(page.getByRole('heading', { name: /login/i })).toBeVisible();
    await expect(page.getByLabel(/email/i)).toBeVisible();
    await expect(page.getByLabel(/password/i)).toBeVisible();
    await expect(page.getByRole('button', { name: /login/i })).toBeVisible();
  });

  test('should login successfully', async ({ page }) => {
    await page.getByLabel(/email/i).fill('test@example.com');
    await page.getByLabel(/password/i).fill('password123');
    await page.getByRole('button', { name: /login/i }).click();

    // Should redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.getByRole('heading', { name: /dashboard/i })).toBeVisible();
  });

  test('should show error for invalid credentials', async ({ page }) => {
    await page.getByLabel(/email/i).fill('wrong@example.com');
    await page.getByLabel(/password/i).fill('wrongpassword');
    await page.getByRole('button', { name: /login/i }).click();

    await expect(page.getByRole('alert')).toHaveText(/invalid credentials/i);
    
    // Should stay on login page
    await expect(page).toHaveURL('/login');
  });

  test('should validate required fields', async ({ page }) => {
    await page.getByRole('button', { name: /login/i }).click();

    // HTML5 validation should prevent submission
    const emailInput = page.getByLabel(/email/i);
    await expect(emailInput).toHaveAttribute('required');
    
    const passwordInput = page.getByLabel(/password/i);
    await expect(passwordInput).toHaveAttribute('required');
  });

  test('should show loading state during submission', async ({ page }) => {
    await page.getByLabel(/email/i).fill('test@example.com');
    await page.getByLabel(/password/i).fill('password123');
    
    const button = page.getByRole('button', { name: /login/i });
    await button.click();

    // Button should show loading state
    await expect(button).toHaveText(/logging in/i);
    await expect(button).toBeDisabled();

    // Eventually redirects
    await expect(page).toHaveURL('/dashboard');
  });

  test('should toggle password visibility', async ({ page }) => {
    const passwordInput = page.getByLabel(/password/i);
    const toggleButton = page.getByRole('button', { name: /show password/i });

    await expect(passwordInput).toHaveAttribute('type', 'password');

    await toggleButton.click();
    await expect(passwordInput).toHaveAttribute('type', 'text');

    await toggleButton.click();
    await expect(passwordInput).toHaveAttribute('type', 'password');
  });
});
```

---

**e2e/shopping-cart.spec.ts:**

```typescript
import { test, expect } from '@playwright/test';

test.describe('Shopping Cart', () => {
  test.beforeEach(async ({ page }) => {
    // Login first
    await page.goto('/login');
    await page.getByLabel(/email/i).fill('test@example.com');
    await page.getByLabel(/password/i).fill('password123');
    await page.getByRole('button', { name: /login/i }).click();
    
    await expect(page).toHaveURL('/dashboard');
    
    // Navigate to products
    await page.getByRole('link', { name: /products/i }).click();
  });

  test('should add product to cart', async ({ page }) => {
    // Find first product and add to cart
    const firstProduct = page.locator('[data-testid="product-card"]').first();
    await firstProduct.getByRole('button', { name: /add to cart/i }).click();

    // Cart badge should update
    await expect(page.getByTestId('cart-badge')).toHaveText('1');

    // Success toast should appear
    await expect(page.getByRole('status')).toHaveText(/added to cart/i);
  });

  test('should update cart quantity', async ({ page }) => {
    // Add product to cart
    await page.locator('[data-testid="product-card"]').first()
      .getByRole('button', { name: /add to cart/i }).click();

    // Go to cart
    await page.getByRole('link', { name: /cart/i }).click();

    // Increase quantity
    const quantityInput = page.getByLabel(/quantity/i);
    await quantityInput.fill('3');

    // Total should update
    await expect(page.getByTestId('cart-total')).toContainText('$2,997');
  });

  test('should remove product from cart', async ({ page }) => {
    // Add product
    await page.locator('[data-testid="product-card"]').first()
      .getByRole('button', { name: /add to cart/i }).click();

    // Go to cart
    await page.getByRole('link', { name: /cart/i }).click();

    // Remove product
    await page.getByRole('button', { name: /remove/i }).click();

    // Cart should be empty
    await expect(page.getByText(/your cart is empty/i)).toBeVisible();
    await expect(page.getByTestId('cart-badge')).toHaveText('0');
  });

  test('should complete checkout', async ({ page }) => {
    // Add products to cart
    const products = page.locator('[data-testid="product-card"]');
    await products.nth(0).getByRole('button', { name: /add to cart/i }).click();
    await products.nth(1).getByRole('button', { name: /add to cart/i }).click();

    // Go to cart
    await page.getByRole('link', { name: /cart/i }).click();

    // Proceed to checkout
    await page.getByRole('button', { name: /checkout/i }).click();

    // Fill shipping info
    await page.getByLabel(/full name/i).fill('John Doe');
    await page.getByLabel(/address/i).fill('123 Main St');
    await page.getByLabel(/city/i).fill('New York');
    await page.getByLabel(/zip code/i).fill('10001');

    // Fill payment info
    await page.getByLabel(/card number/i).fill('4242424242424242');
    await page.getByLabel(/expiry/i).fill('12/25');
    await page.getByLabel(/cvc/i).fill('123');

    // Place order
    await page.getByRole('button', { name: /place order/i }).click();

    // Should see success page
    await expect(page).toHaveURL(/\/order-success/);
    await expect(page.getByRole('heading', { name: /order confirmed/i })).toBeVisible();
    
    // Cart should be cleared
    await expect(page.getByTestId('cart-badge')).toHaveText('0');
  });

  test('should persist cart across page reloads', async ({ page }) => {
    // Add product
    await page.locator('[data-testid="product-card"]').first()
      .getByRole('button', { name: /add to cart/i }).click();

    await expect(page.getByTestId('cart-badge')).toHaveText('1');

    // Reload page
    await page.reload();

    // Cart should still have item
    await expect(page.getByTestId('cart-badge')).toHaveText('1');
  });

  test('should filter products', async ({ page }) => {
    const productCount = await page.locator('[data-testid="product-card"]').count();
    expect(productCount).toBeGreaterThan(0);

    // Apply category filter
    await page.getByLabel(/category/i).selectOption('electronics');

    // Product count should change
    await page.waitForTimeout(500); // Wait for filter to apply
    const filteredCount = await page.locator('[data-testid="product-card"]').count();
    expect(filteredCount).toBeLessThan(productCount);
  });

  test('should search for products', async ({ page }) => {
    await page.getByPlaceholder(/search products/i).fill('laptop');
    await page.getByRole('button', { name: /search/i }).click();

    // Should show only laptops
    const products = page.locator('[data-testid="product-card"]');
    await expect(products.first()).toContainText(/laptop/i);
  });
});
```

---

### Playwright Page Object Model

```typescript
// e2e/pages/LoginPage.ts
import { Page, Locator } from '@playwright/test';

export class LoginPage {
  readonly page: Page;
  readonly emailInput: Locator;
  readonly passwordInput: Locator;
  readonly loginButton: Locator;
  readonly errorMessage: Locator;

  constructor(page: Page) {
    this.page = page;
    this.emailInput = page.getByLabel(/email/i);
    this.passwordInput = page.getByLabel(/password/i);
    this.loginButton = page.getByRole('button', { name: /login/i });
    this.errorMessage = page.getByRole('alert');
  }

  async goto() {
    await this.page.goto('/login');
  }

  async login(email: string, password: string) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.loginButton.click();
  }

  async loginAsTestUser() {
    await this.login('test@example.com', 'password123');
  }

  async expectError(message: string | RegExp) {
    await this.errorMessage.waitFor();
    await this.errorMessage.textContent();
  }
}

// e2e/pages/CartPage.ts
export class CartPage {
  readonly page: Page;
  readonly cartItems: Locator;
  readonly cartTotal: Locator;
  readonly checkoutButton: Locator;
  readonly emptyMessage: Locator;

  constructor(page: Page) {
    this.page = page;
    this.cartItems = page.locator('[data-testid="cart-item"]');
    this.cartTotal = page.getByTestId('cart-total');
    this.checkoutButton = page.getByRole('button', { name: /checkout/i });
    this.emptyMessage = page.getByText(/your cart is empty/i);
  }

  async goto() {
    await this.page.goto('/cart');
  }

  async removeItem(productName: string) {
    const item = this.page.locator('[data-testid="cart-item"]', {
      hasText: productName
    });
    await item.getByRole('button', { name: /remove/i }).click();
  }

  async updateQuantity(productName: string, quantity: number) {
    const item = this.page.locator('[data-testid="cart-item"]', {
      hasText: productName
    });
    await item.getByLabel(/quantity/i).fill(String(quantity));
  }

  async proceedToCheckout() {
    await this.checkoutButton.click();
  }

  async getItemCount(): Promise<number> {
    return await this.cartItems.count();
  }
}

// Usage in tests
test('should login and add to cart using POM', async ({ page }) => {
  const loginPage = new LoginPage(page);
  const cartPage = new CartPage(page);

  await loginPage.goto();
  await loginPage.loginAsTestUser();

  // Add product (simplified)
  await page.goto('/products');
  await page.locator('[data-testid="product-card"]').first()
    .getByRole('button', { name: /add to cart/i }).click();

  await cartPage.goto();
  expect(await cartPage.getItemCount()).toBe(1);
});
```

---

## Cypress

### Setting Up Cypress

```bash
npm install --save-dev cypress
npx cypress open
```

**cypress.config.ts:**

```typescript
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    setupNodeEvents(on, config) {
      // implement node event listeners here
    },
    viewportWidth: 1280,
    viewportHeight: 720,
    video: false,
    screenshotOnRunFailure: true,
  },
  component: {
    devServer: {
      framework: 'react',
      bundler: 'vite',
    },
  },
});
```

---

### Cypress E2E Tests

**cypress/e2e/login.cy.ts:**

```typescript
describe('Login Flow', () => {
  beforeEach(() => {
    cy.visit('/login');
  });

  it('should display login form', () => {
    cy.contains('h1', /login/i).should('be.visible');
    cy.get('input[type="email"]').should('be.visible');
    cy.get('input[type="password"]').should('be.visible');
    cy.contains('button', /login/i).should('be.visible');
  });

  it('should login successfully', () => {
    cy.get('input[type="email"]').type('test@example.com');
    cy.get('input[type="password"]').type('password123');
    cy.contains('button', /login/i).click();

    cy.url().should('include', '/dashboard');
    cy.contains('h1', /dashboard/i).should('be.visible');
  });

  it('should show error for invalid credentials', () => {
    cy.get('input[type="email"]').type('wrong@example.com');
    cy.get('input[type="password"]').type('wrongpassword');
    cy.contains('button', /login/i).click();

    cy.get('[role="alert"]').should('contain', 'Invalid credentials');
    cy.url().should('include', '/login');
  });

  it('should validate email format', () => {
    cy.get('input[type="email"]').type('invalid-email');
    cy.get('input[type="password"]').type('password123');
    cy.contains('button', /login/i).click();

    // HTML5 validation
    cy.get('input[type="email"]').should('have.prop', 'validationMessage');
  });

  it('should persist session after reload', () => {
    cy.get('input[type="email"]').type('test@example.com');
    cy.get('input[type="password"]').type('password123');
    cy.contains('button', /login/i).click();

    cy.url().should('include', '/dashboard');

    cy.reload();

    cy.url().should('include', '/dashboard');
    cy.contains('h1', /dashboard/i).should('be.visible');
  });
});
```

---

### Cypress Custom Commands

**cypress/support/commands.ts:**

```typescript
declare global {
  namespace Cypress {
    interface Chainable {
      login(email: string, password: string): Chainable<void>;
      loginAsTestUser(): Chainable<void>;
      addToCart(productId: string): Chainable<void>;
      clearCart(): Chainable<void>;
    }
  }
}

Cypress.Commands.add('login', (email: string, password: string) => {
  cy.visit('/login');
  cy.get('input[type="email"]').type(email);
  cy.get('input[type="password"]').type(password);
  cy.contains('button', /login/i).click();
  cy.url().should('include', '/dashboard');
});

Cypress.Commands.add('loginAsTestUser', () => {
  cy.login('test@example.com', 'password123');
});

Cypress.Commands.add('addToCart', (productId: string) => {
  cy.request('POST', '/api/cart', { productId, quantity: 1 });
});

Cypress.Commands.add('clearCart', () => {
  cy.request('DELETE', '/api/cart');
});

// Usage in tests
describe('Dashboard', () => {
  beforeEach(() => {
    cy.loginAsTestUser();
  });

  it('should display user dashboard', () => {
    cy.contains('h1', /dashboard/i).should('be.visible');
  });
});
```

---

### Cypress Component Testing

**cypress/component/Button.cy.tsx:**

```typescript
import { Button } from '../../src/components/Button';

describe('Button Component', () => {
  it('should render with text', () => {
    cy.mount(<Button>Click me</Button>);
    cy.contains('Click me').should('be.visible');
  });

  it('should call onClick when clicked', () => {
    const onClickSpy = cy.spy().as('onClickSpy');
    cy.mount(<Button onClick={onClickSpy}>Click me</Button>);
    
    cy.contains('Click me').click();
    cy.get('@onClickSpy').should('have.been.calledOnce');
  });

  it('should be disabled', () => {
    cy.mount(<Button disabled>Click me</Button>);
    cy.contains('Click me').should('be.disabled');
  });

  it('should show loading state', () => {
    cy.mount(<Button loading>Click me</Button>);
    cy.contains('Loading...').should('be.visible');
    cy.get('button').should('be.disabled');
  });

  it('should apply variant classes', () => {
    cy.mount(<Button variant="danger">Delete</Button>);
    cy.get('button').should('have.class', 'btn-danger');
  });
});
```

---

## Testing Patterns & Best Practices

### 1. AAA Pattern (Arrange-Act-Assert)

```typescript
test('should calculate total price', () => {
  // Arrange
  const cart = new CartService();
  cart.addItem('1', 'Laptop', 999, 2);
  cart.addItem('2', 'Mouse', 25, 1);

  // Act
  const total = cart.getTotal();

  // Assert
  expect(total).toBe(2023);
});
```

---

### 2. Test Organization

```typescript
describe('UserService', () => {
  describe('fetchUser', () => {
    it('should fetch user successfully', async () => {
      // Test success case
    });

    it('should throw error when user not found', async () => {
      // Test error case
    });

    it('should handle network errors', async () => {
      // Test edge case
    });
  });

  describe('createUser', () => {
    it('should create user with valid data', async () => {
      // Test success case
    });

    it('should validate required fields', async () => {
      // Test validation
    });
  });
});
```

---

### 3. Testing Async Code

```typescript
// ✅ Using async/await
test('should fetch data', async () => {
  const data = await fetchData();
  expect(data).toBeDefined();
});

// ✅ Using promises
test('should fetch data', () => {
  return fetchData().then(data => {
    expect(data).toBeDefined();
  });
});

// ✅ Using resolves/rejects
test('should fetch data', async () => {
  await expect(fetchData()).resolves.toBeDefined();
});

test('should throw error', async () => {
  await expect(fetchDataWithError()).rejects.toThrow('Error message');
});
```

---

### 4. Test Coverage Best Practices

```typescript
// ✅ DO: Test public interfaces
test('should add two numbers', () => {
  expect(add(2, 3)).toBe(5);
});

// ❌ DON'T: Test implementation details
test('should call internal method', () => {
  const calculator = new Calculator();
  const spy = jest.spyOn(calculator as any, '_internalMethod');
  calculator.add(2, 3);
  expect(spy).toHaveBeenCalled(); // Testing implementation!
});

// ✅ DO: Test behavior
test('should display error when login fails', async () => {
  render(<LoginForm />);
  // ... fill form
  await user.click(submitButton);
  expect(screen.getByRole('alert')).toHaveTextContent('Invalid credentials');
});

// ❌ DON'T: Test state directly
test('should set error state', async () => {
  const { result } = renderHook(() => useLoginForm());
  await act(() => result.current.login('wrong', 'credentials'));
  expect(result.current.error).toBe('Invalid credentials'); // Testing internal state!
});
```

---

### 5. Mocking Best Practices

```typescript
// ✅ DO: Mock external dependencies
jest.mock('./api/userService');

test('should call user service', async () => {
  (fetchUser as jest.Mock).mockResolvedValue({ id: '1', name: 'John' });
  
  const user = await loadUser('1');
  expect(user.name).toBe('John');
});

// ✅ DO: Use MSW for HTTP mocking (better than fetch mocks)
test('should fetch users', async () => {
  // MSW handles this
  const users = await fetchUsers();
  expect(users).toHaveLength(2);
});

// ❌ DON'T: Mock too much
jest.mock('react'); // Way too broad!
```

---

### 6. Test Data Builders

```typescript
// Test data builder pattern
class UserBuilder {
  private user: Partial<User> = {
    id: '1',
    name: 'Test User',
    email: 'test@example.com',
    role: 'user'
  };

  withId(id: string): this {
    this.user.id = id;
    return this;
  }

  withName(name: string): this {
    this.user.name = name;
    return this;
  }

  withEmail(email: string): this {
    this.user.email = email;
    return this;
  }

  asAdmin(): this {
    this.user.role = 'admin';
    return this;
  }

  build(): User {
    return this.user as User;
  }
}

// Usage
test('should create admin user', () => {
  const admin = new UserBuilder()
    .withName('Admin User')
    .withEmail('admin@example.com')
    .asAdmin()
    .build();

  expect(admin.role).toBe('admin');
});
```

---

### 7. Snapshot Testing (Use Sparingly)

```typescript
import renderer from 'react-test-renderer';

test('should match snapshot', () => {
  const tree = renderer
    .create(<Button variant="primary">Click me</Button>)
    .toJSON();
  
  expect(tree).toMatchSnapshot();
});

// ⚠️ Snapshots are fragile - use for:
// - Static content (documentation, error messages)
// - Complex data structures (API responses)
// 
// ❌ Avoid for:
// - Components with frequent changes
// - Dynamic content (dates, IDs)
// - As a replacement for proper assertions
```

---

### Testing Checklist

**Unit Tests:**
- ✅ Pure functions
- ✅ Business logic
- ✅ Utility functions
- ✅ Complex calculations
- ✅ Edge cases and error handling

**Integration Tests:**
- ✅ Component with hooks
- ✅ Component with context
- ✅ Component with API calls
- ✅ Form submission workflows
- ✅ User interactions

**E2E Tests:**
- ✅ Critical user paths
- ✅ Authentication flows
- ✅ Checkout processes
- ✅ Data mutations
- ✅ Cross-browser compatibility

**Best Practices:**
- ✅ Write tests first (TDD) or alongside code
- ✅ Test behavior, not implementation
- ✅ Use meaningful test names
- ✅ Keep tests independent
- ✅ Mock external dependencies
- ✅ Use setup/teardown properly
- ✅ Aim for 80%+ coverage
- ✅ Run tests in CI/CD pipeline

---

This comprehensive guide covers unit testing, integration testing, E2E testing with Playwright and Cypress, React Testing Library best practices, and MSW for API mocking! 🚀
