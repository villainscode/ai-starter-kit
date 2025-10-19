# Frontend Development Guide

## Project Overview
- **Project Name:** [Your Project Name]
- **Description:** [Brief description of what this frontend application does]
- **Tech Stack:** React, TypeScript, Vite, Tailwind CSS
- **Live URL:** https://example.com
- **API Base URL:** https://api.example.com/v1

---

## Architecture

### Application Architecture
```
User Interface (Components)
        ↓
State Management (Zustand/Redux)
        ↓
API Layer (Axios/Fetch)
        ↓
Backend API
```

### Tech Stack Details
- **Framework:** React 18.x
- **Language:** TypeScript 5.x
- **Build Tool:** Vite 5.x
- **Styling:** Tailwind CSS 3.x
- **State Management:** Zustand / Redux Toolkit
- **Routing:** React Router v6
- **HTTP Client:** Axios / TanStack Query
- **Form Handling:** React Hook Form + Zod
- **UI Components:** shadcn/ui, Radix UI
- **Icons:** Lucide React
- **Testing:** Vitest, React Testing Library
- **Linting:** ESLint + Prettier

---

## Project Structure

```
frontend/
├── public/                # Static assets
│   ├── images/
│   ├── fonts/
│   └── favicon.ico
├── src/
│   ├── assets/            # Images, icons, etc.
│   ├── components/        # React components
│   │   ├── common/        # Reusable components
│   │   ├── layout/        # Layout components
│   │   └── features/      # Feature-specific components
│   ├── pages/             # Page components
│   ├── hooks/             # Custom React hooks
│   ├── services/          # API services
│   ├── store/             # State management
│   ├── utils/             # Utility functions
│   ├── types/             # TypeScript types
│   ├── constants/         # Constants and configs
│   ├── styles/            # Global styles
│   ├── App.tsx
│   ├── main.tsx
│   └── vite-env.d.ts
├── tests/                 # Test files
├── .env.example
├── .eslintrc.json
├── .prettierrc
├── index.html
├── package.json
├── tsconfig.json
├── tailwind.config.js
├── vite.config.ts
└── README.md
```

---

## Setup Instructions

### Prerequisites
- Node.js >= 20.x
- npm or yarn or pnpm
- Git

### Installation Steps

1. **Clone the repository**
```bash
git clone https://github.com/your-org/your-project.git
cd your-project/frontend
```

2. **Install dependencies**
```bash
npm install
# or
yarn install
# or
pnpm install
```

3. **Environment Setup**
```bash
cp .env.example .env.local
# Edit .env.local with your configuration
```

4. **Start Development Server**
```bash
npm run dev
```

Application will run at: `http://localhost:5173`

---

## Environment Variables

```bash
# API Configuration
VITE_API_BASE_URL=http://localhost:3000/api/v1
VITE_API_TIMEOUT=30000

# Authentication
VITE_JWT_SECRET_KEY=your-jwt-secret-key

# External Services
VITE_GOOGLE_MAPS_API_KEY=your-google-maps-api-key
VITE_STRIPE_PUBLIC_KEY=your-stripe-public-key
VITE_ANALYTICS_ID=your-analytics-id

# Feature Flags
VITE_ENABLE_ANALYTICS=true
VITE_ENABLE_ERROR_REPORTING=true
VITE_ENABLE_MAINTENANCE_MODE=false

# Environment
VITE_APP_ENV=development
VITE_APP_VERSION=1.0.0
```

**Note:** All environment variables must be prefixed with `VITE_` to be accessible in the app.

---

## Available Scripts

```bash
# Development
npm run dev              # Start dev server
npm run dev:host         # Start dev server accessible from network

# Build
npm run build            # Production build
npm run build:staging    # Staging build

# Preview
npm run preview          # Preview production build

# Testing
npm run test             # Run all tests
npm run test:watch       # Run tests in watch mode
npm run test:coverage    # Generate coverage report

# Linting & Formatting
npm run lint             # Run ESLint
npm run lint:fix         # Fix ESLint errors
npm run format           # Format code with Prettier
npm run format:check     # Check formatting

# Type Checking
npm run type-check       # Run TypeScript type checking

# Analysis
npm run analyze          # Analyze bundle size
```

---

## Component Structure

### Component Template
```tsx
// components/features/UserCard/UserCard.tsx
import { FC } from 'react';
import { User } from '@/types/user';
import styles from './UserCard.module.css';

interface UserCardProps {
  user: User;
  onEdit?: (userId: string) => void;
  onDelete?: (userId: string) => void;
}

export const UserCard: FC<UserCardProps> = ({ 
  user, 
  onEdit, 
  onDelete 
}) => {
  return (
    <div className="user-card">
      <img src={user.avatar} alt={user.name} />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      
      <div className="actions">
        {onEdit && (
          <button onClick={() => onEdit(user.id)}>
            Edit
          </button>
        )}
        {onDelete && (
          <button onClick={() => onDelete(user.id)}>
            Delete
          </button>
        )}
      </div>
    </div>
  );
};
```

### Common Components

#### Button
```tsx
// components/common/Button/Button.tsx
import { ButtonHTMLAttributes, FC, ReactNode } from 'react';
import { cn } from '@/utils/cn';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  children: ReactNode;
}

export const Button: FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  children,
  className,
  disabled,
  ...props
}) => {
  return (
    <button
      className={cn(
        'button',
        `button--${variant}`,
        `button--${size}`,
        isLoading && 'button--loading',
        className
      )}
      disabled={disabled || isLoading}
      {...props}
    >
      {isLoading ? 'Loading...' : children}
    </button>
  );
};
```

#### Input
```tsx
// components/common/Input/Input.tsx
import { forwardRef, InputHTMLAttributes } from 'react';
import { cn } from '@/utils/cn';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  error?: string;
  helperText?: string;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, helperText, className, ...props }, ref) => {
    return (
      <div className="input-wrapper">
        {label && (
          <label className="input-label">
            {label}
          </label>
        )}
        
        <input
          ref={ref}
          className={cn(
            'input',
            error && 'input--error',
            className
          )}
          {...props}
        />
        
        {error && (
          <span className="input-error">{error}</span>
        )}
        
        {helperText && !error && (
          <span className="input-helper">{helperText}</span>
        )}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

---

## Routing

### Route Configuration
```tsx
// App.tsx
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { Layout } from '@/components/layout/Layout';
import { ProtectedRoute } from '@/components/auth/ProtectedRoute';

// Pages
import { HomePage } from '@/pages/Home';
import { LoginPage } from '@/pages/Login';
import { DashboardPage } from '@/pages/Dashboard';
import { ProfilePage } from '@/pages/Profile';
import { NotFoundPage } from '@/pages/NotFound';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* Public Routes */}
        <Route path="/" element={<Layout />}>
          <Route index element={<HomePage />} />
          <Route path="login" element={<LoginPage />} />
          
          {/* Protected Routes */}
          <Route element={<ProtectedRoute />}>
            <Route path="dashboard" element={<DashboardPage />} />
            <Route path="profile" element={<ProfilePage />} />
          </Route>
          
          {/* Catch all */}
          <Route path="404" element={<NotFoundPage />} />
          <Route path="*" element={<Navigate to="/404" replace />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### Protected Route Component
```tsx
// components/auth/ProtectedRoute.tsx
import { Navigate, Outlet } from 'react-router-dom';
import { useAuth } from '@/hooks/useAuth';

export const ProtectedRoute = () => {
  const { isAuthenticated, isLoading } = useAuth();

  if (isLoading) {
    return <div>Loading...</div>;
  }

  return isAuthenticated ? <Outlet /> : <Navigate to="/login" replace />;
};
```

---

## State Management

### Zustand Store Example
```tsx
// store/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (user: User, token: string) => void;
  logout: () => void;
  updateUser: (user: Partial<User>) => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      
      login: (user, token) => 
        set({ user, token, isAuthenticated: true }),
      
      logout: () => 
        set({ user: null, token: null, isAuthenticated: false }),
      
      updateUser: (userData) =>
        set((state) => ({
          user: state.user ? { ...state.user, ...userData } : null,
        })),
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

### Using Store in Components
```tsx
// components/Header.tsx
import { useAuthStore } from '@/store/authStore';

export const Header = () => {
  const { user, logout } = useAuthStore();

  return (
    <header>
      {user && (
        <>
          <span>Welcome, {user.name}</span>
          <button onClick={logout}>Logout</button>
        </>
      )}
    </header>
  );
};
```

---

## API Integration

### API Service Setup
```tsx
// services/api.ts
import axios, { AxiosError, AxiosRequestConfig } from 'axios';
import { useAuthStore } from '@/store/authStore';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor
api.interceptors.request.use(
  (config) => {
    const token = useAuthStore.getState().token;
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  async (error: AxiosError) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout();
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### API Service Functions
```tsx
// services/userService.ts
import api from './api';
import { User, UpdateUserDto } from '@/types/user';

export const userService = {
  // Get current user
  getCurrentUser: async (): Promise<User> => {
    const response = await api.get('/auth/me');
    return response.data.data;
  },

  // Get all users
  getUsers: async (params?: {
    page?: number;
    limit?: number;
    search?: string;
  }): Promise<{ data: User[]; pagination: any }> => {
    const response = await api.get('/users', { params });
    return response.data;
  },

  // Get user by ID
  getUserById: async (id: string): Promise<User> => {
    const response = await api.get(`/users/${id}`);
    return response.data.data;
  },

  // Update user
  updateUser: async (id: string, data: UpdateUserDto): Promise<User> => {
    const response = await api.put(`/users/${id}`, data);
    return response.data.data;
  },

  // Delete user
  deleteUser: async (id: string): Promise<void> => {
    await api.delete(`/users/${id}`);
  },
};
```

### Using TanStack Query (React Query)
```tsx
// hooks/useUsers.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { userService } from '@/services/userService';
import { UpdateUserDto } from '@/types/user';

export const useUsers = (params?: { page?: number; limit?: number }) => {
  return useQuery({
    queryKey: ['users', params],
    queryFn: () => userService.getUsers(params),
  });
};

export const useUser = (id: string) => {
  return useQuery({
    queryKey: ['user', id],
    queryFn: () => userService.getUserById(id),
    enabled: !!id,
  });
};

export const useUpdateUser = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, data }: { id: string; data: UpdateUserDto }) =>
      userService.updateUser(id, data),
    onSuccess: (data) => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
      queryClient.setQueryData(['user', data.id], data);
    },
  });
};

export const useDeleteUser = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (id: string) => userService.deleteUser(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
};
```

### Usage in Components
```tsx
// pages/Users.tsx
import { useState } from 'react';
import { useUsers, useDeleteUser } from '@/hooks/useUsers';
import { Button } from '@/components/common/Button';
import { UserCard } from '@/components/features/UserCard';

export const UsersPage = () => {
  const [page, setPage] = useState(1);
  const { data, isLoading, error } = useUsers({ page, limit: 10 });
  const deleteUser = useDeleteUser();

  const handleDelete = async (userId: string) => {
    if (confirm('Are you sure?')) {
      await deleteUser.mutateAsync(userId);
    }
  };

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div className="users-page">
      <h1>Users</h1>
      
      <div className="users-grid">
        {data?.data.map((user) => (
          <UserCard
            key={user.id}
            user={user}
            onDelete={handleDelete}
          />
        ))}
      </div>

      <div className="pagination">
        <Button
          onClick={() => setPage(p => Math.max(1, p - 1))}
          disabled={page === 1}
        >
          Previous
        </Button>
        <span>Page {page}</span>
        <Button
          onClick={() => setPage(p => p + 1)}
          disabled={page === data?.pagination.totalPages}
        >
          Next
        </Button>
      </div>
    </div>
  );
};
```

---

## Form Handling

### Form with React Hook Form + Zod
```tsx
// pages/Login.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import { Input } from '@/components/common/Input';
import { Button } from '@/components/common/Button';
import { useAuthStore } from '@/store/authStore';
import { authService } from '@/services/authService';

const loginSchema = z.object({
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

type LoginFormData = z.infer<typeof loginSchema>;

export const LoginPage = () => {
  const login = useAuthStore((state) => state.login);
  
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
  });

  const onSubmit = async (data: LoginFormData) => {
    try {
      const response = await authService.login(data);
      login(response.user, response.token);
      // Redirect to dashboard
    } catch (error) {
      console.error('Login failed:', error);
    }
  };

  return (
    <div className="login-page">
      <h1>Login</h1>
      
      <form onSubmit={handleSubmit(onSubmit)}>
        <Input
          label="Email"
          type="email"
          error={errors.email?.message}
          {...register('email')}
        />

        <Input
          label="Password"
          type="password"
          error={errors.password?.message}
          {...register('password')}
        />

        <Button
          type="submit"
          isLoading={isSubmitting}
        >
          Login
        </Button>
      </form>
    </div>
  );
};
```

---

## Custom Hooks

### useDebounce
```tsx
// hooks/useDebounce.ts
import { useEffect, useState } from 'react';

export function useDebounce<T>(value: T, delay: number = 500): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);

  return debouncedValue;
}
```

### useLocalStorage
```tsx
// hooks/useLocalStorage.ts
import { useState, useEffect } from 'react';

export function useLocalStorage<T>(
  key: string,
  initialValue: T
): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}
```

### useMediaQuery
```tsx
// hooks/useMediaQuery.ts
import { useState, useEffect } from 'react';

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

// Usage:
// const isMobile = useMediaQuery('(max-width: 768px)');
```

---

## TypeScript Types

### Common Types
```tsx
// types/common.ts
export interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
}

export interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

export interface ApiError {
  success: false;
  error: {
    code: string;
    message: string;
    details?: Record<string, any>;
  };
}
```

### User Types
```tsx
// types/user.ts
export interface User {
  id: string;
  email: string;
  name: string;
  avatar?: string;
  role: 'user' | 'admin';
  createdAt: string;
  updatedAt: string;
}

export interface UpdateUserDto {
  name?: string;
  avatar?: string;
  bio?: string;
}

export interface CreateUserDto {
  email: string;
  password: string;
  name: string;
}
```

---

## Styling

### Tailwind CSS Configuration
```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
        secondary: {
          500: '#6366f1',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
    },
  },
  plugins: [],
}
```

### Global Styles
```css
/* styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  * {
    @apply border-border;
  }
  
  body {
    @apply bg-background text-foreground;
    font-feature-settings: "rlig" 1, "calt" 1;
  }
}

@layer components {
  .btn {
    @apply px-4 py-2 rounded-md font-medium transition-colors;
  }
  
  .btn-primary {
    @apply bg-primary-600 text-white hover:bg-primary-700;
  }
  
  .btn-secondary {
    @apply bg-gray-200 text-gray-900 hover:bg-gray-300;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
  
  .input {
    @apply w-full px-3 py-2 border border-gray-300 rounded-md;
    @apply focus:outline-none focus:ring-2 focus:ring-primary-500;
  }
}
```

---

## Testing

### Unit Test Example
```tsx
// components/Button/Button.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('handles click events', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('shows loading state', () => {
    render(<Button isLoading>Click me</Button>);
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });

  it('is disabled when loading', () => {
    render(<Button isLoading>Click me</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

### Integration Test Example
```tsx
// pages/Login/Login.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { LoginPage } from './Login';
import { authService } from '@/services/authService';

vi.mock('@/services/authService');

describe('LoginPage', () => {
  it('submits form with valid data', async () => {
    const mockLogin = vi.fn().mockResolvedValue({
      user: { id: '1', email: 'test@example.com', name: 'Test' },
      token: 'fake-token',
    });
    
    (authService.login as any) = mockLogin;

    render(<LoginPage />);

    fireEvent.change(screen.getByLabelText('Email'), {
      target: { value: 'test@example.com' },
    });
    
    fireEvent.change(screen.getByLabelText('Password'), {
      target: { value: 'password123' },
    });

    fireEvent.click(screen.getByText('Login'));

    await waitFor(() => {
      expect(mockLogin).toHaveBeenCalledWith({
        email: 'test@example.com',
        password: 'password123',
      });
    });
  });
});
```

---

## Performance Optimization

### Code Splitting
```tsx
// Lazy load components
import { lazy, Suspense } from 'react';

const DashboardPage = lazy(() => import('@/pages/Dashboard'));
const ProfilePage = lazy(() => import('@/pages/Profile'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/dashboard" element={<DashboardPage />} />
        <Route path="/profile" element={<ProfilePage />} />
      </Routes>
    </Suspense>
  );
}
```

### Memoization
```tsx
import { memo, useMemo, useCallback } from 'react';

// Memo component
export const ExpensiveComponent = memo(({ data }) => {
  // Component only re-renders when data changes
  return <div>{/* ... */}</div>;
});

// useMemo for expensive calculations
const sortedData = useMemo(() => {
  return data.sort((a, b) => a.value - b.value);
}, [data]);

// useCallback for functions
const handleClick = useCallback(() => {
  console.log('clicked');
}, []);
```

### Image Optimization
```tsx
// Use lazy loading for images
<img
  src={imageUrl}
  alt="Description"
  loading="lazy"
  decoding="async"
/>

// Use modern image formats
<picture>
  <source srcSet="image.webp" type="image/webp" />
  <source srcSet="image.jpg" type="image/jpeg" />
  <img src="image.jpg" alt="Description" />
</picture>
```

---

## Accessibility (a11y)

### Best Practices
```tsx
// Use semantic HTML
<nav aria-label="Main navigation">
  <ul>
    <li><a href="/">Home</a></li>
  </ul>
</nav>

// Proper button labels
<button aria-label="Close dialog">
  <XIcon />
</button>

// Form accessibility
<label htmlFor="email">Email</label>
<input
  id="email"
  type="email"
  aria-required="true"
  aria-invalid={hasError}
  aria-describedby="email-error"
/>
{hasError && (
  <span id="email-error" role="alert">
    Please enter a valid email
  </span>
)}

// Focus management
useEffect(() => {
  modalRef.current?.focus();
}, [isOpen]);

// Skip to main content
<a href="#main-content" className="skip-link">
  Skip to main content
</a>
<main id="main-content">
  {/* Content */}
</main>
```

---

## SEO Optimization

### React Helmet for Meta Tags
```tsx
import { Helmet } from 'react-helmet-async';

export const HomePage = () => {
  return (
    <>
      <Helmet>
        <title>Home | Your App Name</title>
        <meta
          name="description"
          content="Welcome to Your App - The best app for..."
        />
        <meta property="og:title" content="Home | Your App" />
        <meta property="og:description" content="..." />
        <meta property="og:image" content="/og-image.jpg" />
        <meta property="og:type" content="website" />
        <link rel="canonical" href="https://example.com" />
      </Helmet>

      {/* Page content */}
    </>
  );
};
```

---

## Build & Deployment

### Build Configuration
```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
        },
      },
    },
  },
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
      },
    },
  },
});
```

### Deployment to Vercel
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel

# Deploy to production
vercel --prod
```

### Docker Deployment
```dockerfile
# Dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## Error Handling

### Error Boundary
```tsx
// components/ErrorBoundary.tsx
import { Component, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: any) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-boundary">
          <h1>Something went wrong</h1>
          <p>{this.state.error?.message}</p>
        </div>
      );
    }

    return this.props.children;
  }
}
```

---

## Security Best Practices

### XSS Prevention
```tsx
// Sanitize user input
import DOMPurify from 'dompurify';

const SafeHTML = ({ html }: { html: string }) => {
  const sanitized = DOMPurify.sanitize(html);
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
};
```

### CSRF Protection
```tsx
// Include CSRF token in requests
api.defaults.headers.common['X-CSRF-Token'] = getCsrfToken();
```

### Content Security Policy
```html
<!-- index.html -->
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';"
/>
```

---

## Resources

### Documentation
- React: https://react.dev/
- TypeScript: https://www.typescriptlang.org/
- Vite: https://vitejs.dev/
- Tailwind CSS: https://tailwindcss.com/
- TanStack Query: https://tanstack.com/query/

### UI Libraries
- shadcn/ui: https://ui.shadcn.com/
- Radix UI: https://www.radix-ui.com/
- Headless UI: https://headlessui.com/

### Learning Resources
- React Patterns: https://reactpatterns.com/
- TypeScript Handbook: https://www.typescriptlang.org/docs/handbook/

---

## Troubleshooting

### Common Issues

#### Build Fails
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Clear Vite cache
rm -rf .vite
```

#### Type Errors
```bash
# Regenerate types
npm run type-check
```

#### Hot Reload Not Working
```bash
# Check vite.config.ts
server: {
  watch: {
    usePolling: true,
  },
}
```

---

## Contributing

See backend-guide.md for contributing guidelines.

---

## Contact & Support

- **Tech Lead:** tech-lead@example.com
- **Frontend Team:** frontend@example.com
- **Slack Channel:** #frontend-support
- **Documentation:** https://docs.example.com

---

**Last Updated:** 2024-01-20  
**Version:** 1.0.0