# Admin Rules

## General

- Follow Vue 2 best practices and ESLint rules
- Do not create any markdown files
- Maintain a TODO list and mark items as completed
- After completing a task, write a summary of changes and suggest improvements

## CSS

- Do not write scoped styles inside Vue components
- Keep styles in the global main CSS file or existing CSS modules
- Do not create additional CSS files

## Components

- Do not put all sections inside a single component
- Split each section into its own component for maintainability
- Feature-specific components go in `views/{feature}/components/`

## i18n

- Translate all new user-facing strings
- Support all three languages: `en`, `km`, `zh`
- Use `$t('key')` for all user-facing text
