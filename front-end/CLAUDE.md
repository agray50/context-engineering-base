# CLAUDE_TYPESCRIPT_REACT.md

This file provides comprehensive guidance to Claude Code when working with TypeScript and React code in this repository.

## Core Development Philosophy

### KISS (Keep It Simple, Stupid)

Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible. Simple solutions are easier to understand, maintain, and debug.

### YAGNI (You Aren't Gonna Need It)

Avoid building functionality on speculation. Implement features only when they are needed, not when you anticipate they might be useful in the future.

### Design Principles

- **Dependency Inversion**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Single Responsibility**: Each function, component, and module should have one clear purpose.
- **Fail Fast**: Check for potential errors early and throw exceptions immediately when issues occur.

## 🧱 Code Structure & Modularity

### File and Function Limits

- **Never create a file longer than 500 lines of code**. If approaching this limit, refactor by splitting into modules.
- **Functions should be under 50 lines** with a single, clear responsibility.
- **Components should be under 100 lines** and represent a single UI concept or entity.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.
- **Line length should be max 100 characters** (configured in Prettier)
- **Use npm** for package management and ensure all commands are run in the project root.

### Project Architecture

Follow strict vertical slice architecture with tests living next to the code they test:

```
src/
    index.tsx
    App.tsx
    __tests__/
        App.test.tsx
    setupTests.ts

    # Core modules
    components/
        ui/
            Button/
                Button.tsx
                Button.module.css
                Button.test.tsx
                index.ts
            Input/
                Input.tsx
                Input.module.css
                Input.test.tsx
                index.ts
        layout/
            Header/
                Header.tsx
                Header.test.tsx
                index.ts

    # Feature slices
    features/
        auth/
            components/
                LoginForm/
                    LoginForm.tsx
                    LoginForm.test.tsx
                    index.ts
            hooks/
                useAuth.ts
                useAuth.test.ts
            services/
                authApi.ts
                authApi.test.ts
            types/
                auth.types.ts

        dashboard/
            components/
                DashboardWidget/
                    DashboardWidget.tsx
                    DashboardWidget.test.tsx
                    index.ts
            hooks/
                useDashboard.ts
            services/
                dashboardApi.ts

    # Shared utilities
    hooks/
        useLocalStorage.ts
        useDebounce.ts
    utils/
        formatters.ts
        validators.ts
        __tests__/
            formatters.test.ts
            validators.test.ts
    types/
        common.types.ts
        api.types.ts
    services/
        api.ts
        httpClient.ts
```

## 🛠️ Development Environment

### Package Management

This project uses npm for fast TypeScript and React package management.

```bash
# Install dependencies
npm install

# Add a package ***NEVER UPDATE A DEPENDENCY DIRECTLY IN PACKAGE.JSON***
# ALWAYS USE PACKAGE MANAGER COMMANDS
npm install axios

# Add development dependency
npm install --save-dev @types/jest eslint prettier

# Remove a package
npm uninstall axios

# Run commands
npm run dev
npm run build
npm run test
npm run lint
```

### Development Commands

```bash
# Start development server
npm run dev

# Run all tests
npm test

# Run specific tests with watch mode
npm test -- --watch LoginForm.test.tsx

# Run tests with coverage
npm test -- --coverage

# Build for production
npm run build

# Lint code
npm run lint

# Fix linting issues automatically
npm run lint:fix

# Format code
npm run format

# Type checking
npm run type-check
```

## 📋 Style & Conventions

### TypeScript Style Guide

- **Follow strict TypeScript configuration** with these specific choices:
  - Line length: 100 characters (set by Prettier)
  - Use double quotes for strings
  - Use trailing commas in multi-line structures
  - Enable strict mode in tsconfig.json
- **Always use explicit types** for function signatures, component props, and complex objects
- **Format with Prettier** and lint with ESLint
- **Use Zod or similar** for runtime data validation

### Component Documentation Standards

Use JSDoc comments for all React components and complex functions:

```typescript
interface ButtonProps {
  /** The button variant style */
  variant: 'primary' | 'secondary' | 'danger';
  /** Button size */
  size?: 'sm' | 'md' | 'lg';
  /** Whether the button is disabled */
  disabled?: boolean;
  /** Click handler */
  onClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
  /** Button content */
  children: React.ReactNode;
}

/**
 * A reusable button component with multiple variants and sizes.
 *
 * @param props - The button props
 * @returns A styled button element
 *
 * @example
 * ```tsx
 * <Button variant="primary" size="lg" onClick={handleClick}>
 *   Click me
 * </Button>
 * ```
 */
export const Button: React.FC<ButtonProps> = ({
  variant,
  size = 'md',
  disabled = false,
  onClick,
  children,
}) => {
  // Implementation
};
```

### Naming Conventions

- **Variables and functions**: `camelCase`
- **Components**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Types and interfaces**: `PascalCase`
- **Enum values**: `PascalCase`
- **CSS classes**: `kebab-case` or follow CSS Modules convention

## 🧪 Testing Strategy

### Test-Driven Development (TDD)

1. **Write the test first** - Define expected behavior before implementation
2. **Watch it fail** - Ensure the test actually tests something
3. **Write minimal code** - Just enough to make the test pass
4. **Refactor** - Improve code while keeping tests green
5. **Repeat** - One test at a time

### Testing Best Practices

```typescript
// Always use React Testing Library and Jest
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

// Use descriptive test names
describe('LoginForm', () => {
  it('should render email and password fields', () => {
    render(<LoginForm onSubmit={jest.fn()} />);
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
  });

  it('should call onSubmit with form data when submitted with valid input', async () => {
    const mockSubmit = jest.fn();
    const user = userEvent.setup();
    
    render(<LoginForm onSubmit={mockSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    expect(mockSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    });
  });

  // Test edge cases and error conditions
  it('should display validation error for invalid email format', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={jest.fn()} />);
    
    await user.type(screen.getByLabelText(/email/i), 'invalid-email');
    await user.click(screen.getByRole('button', { name: /sign in/i }));
    
    expect(screen.getByText(/invalid email format/i)).toBeInTheDocument();
  });
});
```

### Test Organization

- Unit tests: Test individual components/functions in isolation
- Integration tests: Test component interactions and data flow
- End-to-end tests: Test complete user workflows with tools like Cypress or Playwright
- Keep test files next to the code they test
- Use `setupTests.ts` for global test configuration
- Aim for 80%+ code coverage, but focus on critical paths

## 🚨 Error Handling

### Error Boundary Best Practices

```typescript
// Create custom error boundaries for different parts of your app
interface ErrorBoundaryState {
  hasError: boolean;
  error?: Error;
}

class FeatureErrorBoundary extends React.Component<
  React.PropsWithChildren<{}>,
  ErrorBoundaryState
> {
  constructor(props: React.PropsWithChildren<{}>) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Feature error boundary caught an error:', error, errorInfo);
    // Log to error reporting service
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-fallback">
          <h2>Something went wrong in this feature.</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}

// Use custom hooks for error handling
const useErrorHandler = () => {
  const [error, setError] = useState<Error | null>(null);

  const handleError = useCallback((error: Error) => {
    setError(error);
    console.error('Error occurred:', error);
    // Log to error reporting service
  }, []);

  const clearError = useCallback(() => {
    setError(null);
  }, []);

  return { error, handleError, clearError };
};
```

### Async Error Handling

```typescript
// Handle async operations with proper error states
const useAsyncOperation = <T>(
  asyncFn: () => Promise<T>
) => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  const execute = useCallback(async () => {
    try {
      setLoading(true);
      setError(null);
      const result = await asyncFn();
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err : new Error('Unknown error'));
    } finally {
      setLoading(false);
    }
  }, [asyncFn]);

  return { data, loading, error, execute };
};
```

## 🔧 Configuration Management

### Environment Variables and Configuration

```typescript
// Define environment variables with validation
interface EnvironmentConfig {
  NODE_ENV: 'development' | 'production' | 'test';
  REACT_APP_API_URL: string;
  REACT_APP_API_KEY: string;
  REACT_APP_FEATURE_FLAGS: string;
}

const getEnvironmentConfig = (): EnvironmentConfig => {
  const config = {
    NODE_ENV: process.env.NODE_ENV as EnvironmentConfig['NODE_ENV'],
    REACT_APP_API_URL: process.env.REACT_APP_API_URL,
    REACT_APP_API_KEY: process.env.REACT_APP_API_KEY,
    REACT_APP_FEATURE_FLAGS: process.env.REACT_APP_FEATURE_FLAGS || '{}',
  };

  // Validate required environment variables
  if (!config.REACT_APP_API_URL) {
    throw new Error('REACT_APP_API_URL is required');
  }

  if (!config.REACT_APP_API_KEY) {
    throw new Error('REACT_APP_API_KEY is required');
  }

  return config as EnvironmentConfig;
};

export const config = getEnvironmentConfig();
```

## 🏗️ Data Models and Validation

### TypeScript Types and Zod Validation

```typescript
import { z } from 'zod';

// Define Zod schemas for runtime validation
const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(1).max(255),
  email: z.string().email(),
  age: z.number().int().min(0).max(150),
  roles: z.array(z.enum(['admin', 'user', 'moderator'])),
  createdAt: z.string().datetime(),
  updatedAt: z.string().datetime(),
  isActive: z.boolean().default(true),
});

// Infer TypeScript types from Zod schemas
export type User = z.infer<typeof UserSchema>;

// Create validation functions
export const validateUser = (data: unknown): User => {
  return UserSchema.parse(data);
};

export const validatePartialUser = (data: unknown): Partial<User> => {
  return UserSchema.partial().parse(data);
};

// API response types
export interface ApiResponse<T> {
  data: T;
  message: string;
  success: boolean;
}

export interface PaginatedResponse<T> extends ApiResponse<T[]> {
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

// Form types
export interface UserFormData {
  name: string;
  email: string;
  age: string; // Forms typically use strings
}

export interface UserFormErrors {
  name?: string;
  email?: string;
  age?: string;
  general?: string;
}
```

## 🔄 Git Workflow

### Branch Strategy

- `main` - Production-ready code
- `develop` - Integration branch for features
- `feature/*` - New features
- `fix/*` - Bug fixes
- `docs/*` - Documentation updates
- `refactor/*` - Code refactoring
- `test/*` - Test additions or fixes

### Commit Message Format

Never include claude code, or written by claude code in commit messages

```
<type>(<scope>): <subject>

<body>

<footer>
```

Types: feat, fix, docs, style, refactor, test, chore

Example:
```
feat(auth): add two-factor authentication

- Implement TOTP generation and validation
- Add QR code component for authenticator apps
- Update user interface with 2FA fields

Closes #123
```

## 🗄️ API Integration Standards

### HTTP Client Configuration

```typescript
// Configure axios or fetch with proper typing
import axios, { AxiosResponse } from 'axios';
import { config } from './config';

const apiClient = axios.create({
  baseURL: config.REACT_APP_API_URL,
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${config.REACT_APP_API_KEY}`,
  },
  timeout: 10000,
});

// Add request/response interceptors
apiClient.interceptors.request.use(
  (config) => {
    console.log(`Making ${config.method?.toUpperCase()} request to ${config.url}`);
    return config;
  },
  (error) => Promise.reject(error)
);

apiClient.interceptors.response.use(
  (response: AxiosResponse) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized access
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export { apiClient };
```

### API Service Pattern

```typescript
// Create typed API services
export class UserService {
  static async getUsers(params?: {
    page?: number;
    limit?: number;
  }): Promise<PaginatedResponse<User>> {
    const response = await apiClient.get<PaginatedResponse<User>>('/users', {
      params,
    });
    return response.data;
  }

  static async getUserById(id: string): Promise<ApiResponse<User>> {
    const response = await apiClient.get<ApiResponse<User>>(`/users/${id}`);
    return response.data;
  }

  static async createUser(userData: UserFormData): Promise<ApiResponse<User>> {
    const response = await apiClient.post<ApiResponse<User>>('/users', userData);
    return response.data;
  }

  static async updateUser(
    id: string,
    userData: Partial<UserFormData>
  ): Promise<ApiResponse<User>> {
    const response = await apiClient.put<ApiResponse<User>>(`/users/${id}`, userData);
    return response.data;
  }

  static async deleteUser(id: string): Promise<ApiResponse<null>> {
    const response = await apiClient.delete<ApiResponse<null>>(`/users/${id}`);
    return response.data;
  }
}
```

## 📝 Documentation Standards

### Component Documentation

- Every component should have a clear interface with TypeScript
- Public components must have JSDoc comments
- Complex logic should have inline comments with `// Reason:` prefix
- Keep README.md updated with setup instructions and examples
- Maintain CHANGELOG.md for version history

### Storybook Documentation

```typescript
// Create stories for components
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Components/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: { type: 'select' },
      options: ['primary', 'secondary', 'danger'],
    },
    size: {
      control: { type: 'select' },
      options: ['sm', 'md', 'lg'],
    },
  },
};

export default meta;
type Story = StoryObj<typeof meta>;

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

export const Large: Story = {
  args: {
    size: 'lg',
    children: 'Button',
  },
};
```

## 🚀 Performance Considerations

### Optimization Guidelines

- Profile before optimizing - use React DevTools Profiler
- Use `useMemo` and `useCallback` for expensive computations
- Implement code splitting with `React.lazy` and `Suspense`
- Optimize bundle size with tree shaking
- Use `React.memo` for component memoization
- Implement virtual scrolling for large lists

### Example Optimizations

```typescript
import { memo, useMemo, useCallback, lazy, Suspense } from 'react';

// Lazy load components
const HeavyComponent = lazy(() => import('./HeavyComponent'));

// Memoize expensive calculations
const ExpensiveComponent: React.FC<{ items: Item[] }> = memo(({ items }) => {
  const expensiveValue = useMemo(() => {
    return items.reduce((acc, item) => acc + item.value, 0);
  }, [items]);

  const handleClick = useCallback((id: string) => {
    // Handle click logic
  }, []);

  return (
    <div>
      <p>Total: {expensiveValue}</p>
      {items.map(item => (
        <button key={item.id} onClick={() => handleClick(item.id)}>
          {item.name}
        </button>
      ))}
    </div>
  );
});

// Use Suspense for lazy loading
const App = () => (
  <Suspense fallback={<div>Loading...</div>}>
    <HeavyComponent />
  </Suspense>
);
```

## 🛡️ Security Best Practices

### Security Guidelines

- Never commit secrets - use environment variables
- Validate all user input with Zod or similar
- Sanitize HTML content to prevent XSS
- Implement proper authentication and authorization
- Use HTTPS for all external communications
- Keep dependencies updated with npm audit

### Example Security Implementation

```typescript
import DOMPurify from 'dompurify';

// Sanitize HTML content
const SafeHtmlContent: React.FC<{ html: string }> = ({ html }) => {
  const sanitizedHtml = useMemo(() => {
    return DOMPurify.sanitize(html);
  }, [html]);

  return <div dangerouslySetInnerHTML={{ __html: sanitizedHtml }} />;
};

// Secure token storage
const useSecureStorage = () => {
  const setToken = useCallback((token: string) => {
    // Use httpOnly cookies or secure storage
    document.cookie = `token=${token}; Secure; HttpOnly; SameSite=Strict`;
  }, []);

  const getToken = useCallback(() => {
    // Retrieve token securely
    return document.cookie
      .split('; ')
      .find(row => row.startsWith('token='))
      ?.split('=')[1];
  }, []);

  return { setToken, getToken };
};
```

## 🔍 Debugging Tools

### Debugging Commands and Tools

```bash
# React DevTools (browser extension)
# Install React DevTools browser extension

# Debug with source maps
npm run build
# Analyze bundle
npm install --save-dev webpack-bundle-analyzer
npm run analyze

# Performance profiling
# Use React DevTools Profiler tab

# Memory profiling
# Use browser DevTools Memory tab

# Network debugging
# Use browser DevTools Network tab
```

### Development Debugging

```typescript
// Use React DevTools hooks
import { useDebugValue } from 'react';

const useCustomHook = (value: string) => {
  const processedValue = useMemo(() => value.toUpperCase(), [value]);
  
  // Show debug info in React DevTools
  useDebugValue(processedValue);
  
  return processedValue;
};

// Add development-only logging
const isDevelopment = process.env.NODE_ENV === 'development';

const debugLog = (message: string, data?: any) => {
  if (isDevelopment) {
    console.log(`[DEBUG] ${message}`, data);
  }
};
```

## 📊 Monitoring and Observability

### Error Tracking and Analytics

```typescript
// Integrate with error tracking services
import * as Sentry from '@sentry/react';

// Initialize Sentry
Sentry.init({
  dsn: process.env.REACT_APP_SENTRY_DSN,
  environment: process.env.NODE_ENV,
});

// Custom error tracking hook
const useErrorTracking = () => {
  const trackError = useCallback((error: Error, context?: Record<string, any>) => {
    Sentry.captureException(error, { extra: context });
  }, []);

  const trackEvent = useCallback((event: string, data?: Record<string, any>) => {
    Sentry.addBreadcrumb({
      message: event,
      data,
      level: 'info',
    });
  }, []);

  return { trackError, trackEvent };
};
```

## 📚 Useful Resources

### Essential Tools

- React Documentation: https://react.dev/
- TypeScript Handbook: https://www.typescriptlang.org/docs/
- React Testing Library: https://testing-library.com/docs/react-testing-library/intro/
- Zod: https://zod.dev/
- React Hook Form: https://react-hook-form.com/

### React Best Practices

- React Patterns: https://reactpatterns.com/
- React Performance: https://react.dev/learn/render-and-commit
- Accessibility: https://react.dev/learn/accessibility

## ⚠️ Important Notes

- **NEVER ASSUME OR GUESS** - When in doubt, ask for clarification
- **Always verify file paths and component names** before use
- **Keep CLAUDE_TYPESCRIPT_REACT.md updated** when adding new patterns or dependencies
- **Test your code** - No feature is complete without tests
- **Document your decisions** - Future developers (including yourself) will thank you

## 🔍 Search Command Requirements

**CRITICAL**: Always use `rg` (ripgrep) instead of traditional `grep` and `find` commands:

```bash
# ❌ Don't use grep
grep -r "pattern" .

# ✅ Use rg instead
rg "pattern"

# ❌ Don't use find with name
find . -name "*.tsx"

# ✅ Use rg with file filtering
rg --files | rg "\.tsx$"
# or
rg --files -g "*.tsx"
```

## 🚀 GitHub Flow Workflow Summary

main (protected) ←── PR ←── feature/your-feature
↓ ↑
deploy development

### Daily Workflow:

1. git checkout main && git pull origin main
2. git checkout -b feature/new-feature
3. Make changes + tests
4. git push origin feature/new-feature
5. Create PR → Review → Merge to main

---

_This document is a living guide. Update it as the project evolves and new patterns emerge._
