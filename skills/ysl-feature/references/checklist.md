# Feature Implementation Checklist

Before marking a feature as complete, verify:

## Backend (young_sia_api)

- [ ] Database schema created/updated in `schema/{year}.sql`
- [ ] Model created with proper fillable, casts, and relationships
- [ ] Repository interface and implementation created
- [ ] Repository binding registered in RepositoryServiceProvider
- [ ] Controller created with all CRUD methods
- [ ] Form Requests created for validation (Create and Update)
- [ ] API Resources created for transforming responses
- [ ] Routes registered with proper naming and middleware
- [ ] Translations added for all three languages (en, km, zh)
- [ ] Authorization/permissions implemented
- [ ] Error handling implemented
- [ ] API endpoints tested

## Frontend (young_sia_admin)

- [ ] Menu item created with authorization check
- [ ] Menu item registered in App.vue
- [ ] API configuration created
- [ ] Vuex store module created (state, getters, mutations, actions)
- [ ] Router configuration created
- [ ] Index.vue (list view) created
- [ ] Create.vue (create form) created
- [ ] Edit.vue (edit form) created
- [ ] Show.vue (detail view) created
- [ ] Form.vue (reusable form) created
- [ ] Filter components created if needed
- [ ] Translations added for all three languages
- [ ] All user-facing text uses i18n
- [ ] Authorization checks implemented
- [ ] Loading states implemented
- [ ] Error handling implemented
- [ ] Success/error notifications implemented
- [ ] Responsive design verified
- [ ] NO scoped styles in components
- [ ] Bootstrap Vue components used throughout

## Testing & Quality

- [ ] All CRUD operations tested
- [ ] Form validation tested
- [ ] Authorization tested
- [ ] Edge cases tested
- [ ] Mobile responsive verified
- [ ] Code follows project conventions
- [ ] All new PHP files pass `pint`
- [ ] All new Vue files pass `eslint`
- [ ] No console errors or warnings
- [ ] Suggest improvements for code quality, readability, and maintainability
