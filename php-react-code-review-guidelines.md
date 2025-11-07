# PHP & React Code Review Guidelines

## Quick Reference Checklist

### PHP Reviews
- [ ] Security vulnerabilities addressed
- [ ] Database queries optimized and protected
- [ ] Error handling implemented
- [ ] Code follows PSR standards
- [ ] Tests included/updated
- [ ] Performance implications considered

### React Reviews
- [ ] Component structure is logical
- [ ] State management is appropriate
- [ ] No unnecessary re-renders
- [ ] Accessibility requirements met
- [ ] Props validated
- [ ] Hooks used correctly

---

## PHP Code Review Guide

### Security (Critical)

**SQL Injection Prevention**
- ✅ Use prepared statements with parameterized queries
- ✅ Never concatenate user input directly into queries
- ❌ Avoid: `$sql = "SELECT * FROM users WHERE id = " . $_GET['id'];`
- ✅ Prefer: `$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");`

**XSS Protection**
- Always escape output: `htmlspecialchars()`, `htmlentities()`
- Validate and sanitize all user inputs
- Use Content Security Policy headers

**Authentication & Authorization**
- Verify user permissions before sensitive operations
- Use password_hash() and password_verify() for passwords
- Implement CSRF tokens for forms
- Check session management is secure

### Code Quality

**PSR Standards**
- Follow PSR-12 for coding style
- Use PSR-4 for autoloading
- Consistent naming conventions (camelCase for methods, PascalCase for classes)

**Error Handling**
- Use try-catch blocks for expected failures
- Log errors appropriately (don't expose stack traces to users)
- Return meaningful error messages
- Use type declarations and return types

**Database**
- Use transactions for multi-step operations
- Index frequently queried columns
- Avoid N+1 query problems
- Use eager loading when appropriate
- Limit SELECT * queries (specify needed columns)

**Performance**
- Cache expensive operations
- Use lazy loading where appropriate
- Avoid unnecessary database calls in loops
- Profile slow endpoints

### Best Practices

**Dependency Management**
- Use Composer for dependencies
- Keep dependencies updated
- Lock versions in composer.lock

**Testing**
- Include unit tests for business logic
- Add integration tests for API endpoints
- Aim for meaningful coverage, not just high percentages

**Documentation**
- Add PHPDoc blocks for public methods
- Document complex business logic
- Keep README updated

---

## React Code Review Guide

### Component Structure

**Component Organization**
- One component per file
- Logical folder structure (features/components)
- Colocate related files (styles, tests, hooks)

**Component Size**
- Keep components focused and single-purpose
- Extract logic into custom hooks when appropriate
- Break down large components (>300 lines usually too big)

### State Management

**When to Use Different State Types**
- `useState` for local component state
- `useContext` for shared state across multiple components
- Redux/Zustand for complex global state
- URL parameters for shareable/bookmarkable state

**State Updates**
- Avoid unnecessary state (can it be derived?)
- Use functional updates when depending on previous state
- Don't mutate state directly
- Batch related state updates

### Performance

**Prevent Unnecessary Re-renders**
- Use `React.memo()` for expensive components
- Memoize callbacks with `useCallback`
- Memoize computed values with `useMemo`
- Only optimize when performance issues exist (don't over-optimize)

**Lists and Keys**
- Always provide stable keys for lists
- Don't use array index as key (unless list is static)
- Keys should be unique among siblings

**Code Splitting**
- Use `React.lazy()` for route-based splitting
- Load heavy dependencies asynchronously
- Consider bundle size implications

### Hooks Best Practices

**Rules of Hooks**
- Call hooks at top level (not in conditions/loops)
- Only call from React functions
- Properly list all dependencies in useEffect/useCallback/useMemo

**useEffect**
- Include all dependencies in dependency array
- Clean up side effects (return cleanup function)
- Separate concerns into multiple useEffect calls
- Consider if useEffect is actually needed

**Custom Hooks**
- Extract reusable logic
- Name with "use" prefix
- Return consistent structure

### Accessibility (A11y)

**Semantic HTML**
- Use proper HTML elements (button, nav, header, etc.)
- Don't use div/span for interactive elements

**ARIA**
- Add aria-label for icon buttons
- Use aria-live for dynamic content
- Implement keyboard navigation
- Ensure proper focus management

**Forms**
- Label all form inputs
- Show validation errors clearly
- Ensure keyboard accessibility

### Props and TypeScript

**Props Validation**
- Use TypeScript interfaces or PropTypes
- Provide defaults for optional props
- Document complex prop shapes

**Component APIs**
- Keep prop interfaces simple and focused
- Avoid prop drilling (use context if needed)
- Consider composition over complex props

### Code Quality

**Naming Conventions**
- Components: PascalCase
- Functions/variables: camelCase
- Constants: UPPER_SNAKE_CASE
- Boolean props: is/has/should prefix

**Imports**
- Group imports logically (external, internal, components, styles)
- Use absolute imports for cleaner paths
- Remove unused imports

**Error Handling**
- Use Error Boundaries for component errors
- Handle async errors gracefully
- Provide user-friendly error messages

### Testing

**What to Test**
- User interactions and workflows
- Conditional rendering logic
- Custom hooks
- Edge cases and error states

**Testing Best Practices**
- Test behavior, not implementation
- Use React Testing Library patterns
- Mock external dependencies
- Keep tests maintainable

---

## Common Issues to Flag

### PHP Red Flags
- Deprecated PHP functions (mysql_*, etc.)
- Eval() usage
- Extract() on user input
- @ error suppression operator overuse
- Hardcoded credentials
- Missing input validation

### React Red Flags
- Missing key props in lists
- Mutating state directly
- Infinite useEffect loops
- Memory leaks (missing cleanup)
- Prop drilling through many levels
- Mixing concerns in one component

---

## Review Process Tips

### For Reviewers
1. Check security issues first
2. Verify tests pass and new tests added
3. Look for breaking changes
4. Consider maintainability
5. Be constructive in feedback
6. Approve if minor suggestions can be addressed in follow-up

### For Authors
1. Self-review before submitting
2. Keep PRs focused and reasonably sized
3. Write clear PR descriptions
4. Add screenshots for UI changes
5. Update documentation
6. Respond to all comments

---

## Quick Decision Trees

### "Should I request changes?"
- Security issue? → YES, block merge
- Will break production? → YES, block merge
- Significant maintainability concern? → YES, discuss
- Minor style issue? → Comment, don't block
- Personal preference? → Let it go

### "Is this component too complex?"
- More than 3 pieces of state? → Consider splitting
- Multiple useEffects? → Consider splitting
- Handles multiple features? → Consider splitting
- Hard to test? → Consider refactoring

---

## Resources

### PHP
- [PSR Standards](https://www.php-fig.org/psr/)
- [OWASP PHP Security](https://owasp.org/www-project-php-security/)
- [PHP The Right Way](https://phptherightway.com/)

### React
- [React Docs](https://react.dev/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [Web Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

