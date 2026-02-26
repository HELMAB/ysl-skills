# Review Categories

## 1. Bug Detection (Critical Priority)

- Logic errors and edge cases not handled
- Null pointer exceptions and undefined variable access
- Race conditions and concurrency issues
- Memory leaks and resource management
- SQL injection and query errors
- Type mismatches and casting issues
- Off-by-one errors in loops and arrays
- Incorrect conditional logic

## 2. Security Vulnerabilities

- SQL injection vulnerabilities
- XSS (Cross-Site Scripting) risks
- CSRF protection missing
- Mass assignment vulnerabilities in Laravel
- Insecure authentication/authorization
- Sensitive data exposure
- Input validation gaps
- Improper error handling exposing internals

## 3. Laravel/PHP Specific Review

- Follow PSR-1, PSR-2, PSR-12 standards
- Use typed properties over docblocks
- Constructor property promotion when appropriate
- Happy path last pattern (handle errors first)
- Avoid else statements - use early returns
- Use string interpolation over concatenation
- Proper use of Eloquent relationships
- N+1 query detection
- Proper use of transactions
- Service/Repository pattern adherence
- Form Request validation usage
- Proper exception handling
- Route naming conventions (camelCase)
- Controller CRUD method adherence
- Config usage (avoid env() outside config)

## 4. Vue.js Specific Review

- Component composition and reusability
- Props validation and typing
- Proper use of computed vs methods
- Reactive data management
- Event handling best practices
- Lifecycle hook usage
- Memory leak prevention (cleanup in unmounted)
- State management patterns
- Template syntax optimization
- Proper v-if vs v-show usage

## 5. UI/UX Implementation Review

- Semantic HTML usage
- Accessibility (ARIA labels, keyboard navigation, focus states)
- Responsive design implementation
- Color contrast compliance
- Loading states and user feedback
- Error state handling and messaging
- Consistent spacing and typography
- Mobile-first approach
- Performance impact of UI choices

## 6. Performance Issues

- Database query optimization
- Unnecessary loops or iterations
- Missing indexes for queries
- Large payload responses
- Inefficient algorithms
- Memory-intensive operations
- Caching opportunities missed
- Lazy loading not implemented

## 7. Code Quality & Maintainability

- Code duplication (DRY violations)
- Function/method length (should be concise)
- Naming clarity and consistency
- Single Responsibility Principle
- Proper abstraction levels
- Missing or inadequate comments for complex logic
- Test coverage gaps
- Dead code or unused imports
