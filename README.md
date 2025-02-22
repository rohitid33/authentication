# Authentication Module Integration Guide

## 1. Required Dependencies
First, install these dependencies in your project:
```bash
express-rate-limit
helmet
winston
passport
passport-local
express-session
```

## 2. Files to Copy
Copy these core authentication files to your project:

### Backend Files:
1. `server/auth.ts` - Core authentication logic
2. `server/middleware/rate-limit.ts` - Rate limiting
3. `server/middleware/security.ts` - Security middleware
4. `server/logger.ts` - Logging utilities

### Frontend Files:
1. `client/src/hooks/use-auth.tsx` - Authentication React hook
2. `client/src/lib/protected-route.tsx` - Route protection component

## 3. Integration Steps

### Backend Integration:

1. Setup Authentication Middleware:
```typescript
// In your main Express app file
import { setupAuth } from './auth';
import { setupSecurity } from './middleware/security';
import { authLimiter, apiLimiter } from './middleware/rate-limit';

// Apply middleware
app.use("/api/login", authLimiter);
app.use("/api/register", authLimiter);
app.use("/api", apiLimiter);

// Setup security and auth
setupSecurity(app);
setupAuth(app);
```

2. Implement Storage Interface:
```typescript
// Implement IStorage interface in your data layer
interface IStorage {
  getUser(id: number): Promise<User | undefined>;
  getUserByUsername(username: string): Promise<User | undefined>;
  createUser(user: InsertUser): Promise<User>;
  updateUserRole(userId: number, role: string): Promise<User>;
  deactivateUser(userId: number): Promise<void>;
  sessionStore: session.Store;
}
```

### Frontend Integration:

1. Wrap your app with AuthProvider:
```typescript
import { AuthProvider } from './hooks/use-auth';

function App() {
  return (
    <AuthProvider>
      {/* Your app components */}
    </AuthProvider>
  );
}
```

2. Use the auth hook in your components:
```typescript
import { useAuth } from './hooks/use-auth';

function YourComponent() {
  const { user, loginMutation, logoutMutation } = useAuth();
  
  // Use auth functionality
}
```

3. Protect routes:
```typescript
import { ProtectedRoute } from './lib/protected-route';

<Switch>
  <ProtectedRoute path="/protected" component={ProtectedComponent} />
  <Route path="/public" component={PublicComponent} />
</Switch>
```

## 4. Environment Variables
Required environment variables:
```
SESSION_SECRET=your_secret_key_here
```

## 5. Authentication API Endpoints
The module provides these endpoints:
- POST `/api/register` - User registration
- POST `/api/login` - User login
- POST `/api/logout` - User logout
- GET `/api/user` - Get current user

## 6. Security Features
- Rate limiting for auth endpoints
- Session management
- Password hashing with salt
- CSRF protection
- XSS protection via Helmet
- Role-based access control

## 7. Additional Features
- Audit logging
- User role management
- Account deactivation
- Brute force protection

## Notes:
- Ensure your database schema matches the required user model
- Test the integration thoroughly in a development environment
- Monitor the security logs after integration
- Consider implementing additional security measures based on your needs
