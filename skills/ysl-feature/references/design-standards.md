# Design System & Technical Constraints

## Brand Identity

- **Primary Color**: `#129748` (Green)
- Use this color consistently for primary actions, headers, and key UI elements

## UI Framework

- **Framework**: Bootstrap Vue
- Use Bootstrap Vue components exclusively
- Refer to [Bootstrap Vue documentation](https://bootstrap-vue.org/) for available components
- Common components: `b-table`, `b-form`, `b-button`, `b-card`, `b-modal`, `b-pagination`

## CSS Guidelines

**CRITICAL RULES:**
- ❌ **DO NOT** write scoped styles inside Vue components
- ❌ **DO NOT** create additional CSS files
- ✅ **DO** add all styles to the global main CSS file
- ✅ **DO** use Bootstrap utility classes where possible

**Example**:
```vue
<!-- WRONG: Don't do this -->
<style scoped>
.my-component {
  color: red;
}
</style>

<!-- CORRECT: Use utility classes or add to global CSS -->
<template>
  <div class="text-danger">Content</div>
</template>
```

## File Creation Constraints

- ❌ **DO NOT** create or modify Markdown (.md) files unless explicitly defined in the project plan
- ✅ **DO** create only necessary code files (.php, .vue, .js)
- ✅ **DO** follow the exact file structure specified in this guide

## Code Quality Standards

### UI/UX Principles

- Ensure responsive design (mobile, tablet, desktop)
- Provide clear feedback for user actions (loading states, success/error messages)
- Use consistent spacing and alignment
- Implement proper form validation with clear error messages
- Add confirmation dialogs for destructive actions (delete, etc.)
- Use meaningful icons and labels
- Ensure accessibility (ARIA labels, keyboard navigation)

### Vue.js Best Practices

- Use Vue 2 Options API consistently
- Implement proper component lifecycle hooks
- Clean up event listeners and subscriptions in `beforeDestroy`
- Use computed properties for derived data
- Keep components focused and single-responsibility
- Extract reusable logic into mixins or utilities
- Use async/await for asynchronous operations
- Handle errors gracefully with try/catch blocks

### Testing

- Test all CRUD operations
- Verify form validation
- Check authorization and permissions
- Test edge cases and error scenarios
- Verify pagination and filtering
- Test on different screen sizes

### Internationalization (i18n)

- ✅ **DO** translate all user-facing text
- ✅ **DO** use `$t()` for all hardcoded strings in Vue components
- ✅ **DO** use `trans()` or `__()` helpers in Laravel backend
- ✅ **DO** provide translations in all three languages: English (en), Khmer (km), Chinese (zh)
- ❌ **DO NOT** leave hardcoded English text in the UI

**Example**:
```vue
<!-- WRONG -->
<b-button>Save</b-button>

<!-- CORRECT -->
<b-button>{{ $t('common.save') }}</b-button>
```

## Documentation Requirements

- Add inline comments for complex logic
- Document API endpoints in controllers
- Add PHPDoc blocks for methods and classes
- Document component props and events
- Include usage examples for reusable components

## Security Considerations

- Validate all user inputs on both frontend and backend
- Implement proper authorization checks
- Sanitize data before display (prevent XSS)
- Use CSRF protection for form submissions
- Don't expose sensitive data in API responses
- Implement rate limiting for API endpoints

## Common Pitfalls to Avoid

1. **Scoped Styles**: Never add `<style scoped>` to Vue components
2. **Missing Translations**: Always translate all text for all three languages
3. **Hard-coded URLs**: Use API config and named routes
4. **Missing Authorization**: Always check permissions before actions
5. **Poor Error Handling**: Always wrap API calls in try/catch
6. **Inconsistent Naming**: Follow kebab-case for URLs, PascalCase for components
7. **Missing Validation**: Validate on both frontend and backend
8. **Raw Models in API**: Always use API Resources for responses
9. **Forgotten Repository Binding**: Register in RepositoryServiceProvider
10. **Missing Loading States**: Show spinners during async operations
