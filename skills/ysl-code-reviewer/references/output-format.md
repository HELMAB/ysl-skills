# Output Format

Structure your review as follows:

````markdown
# Code Review: [Branch Name] vs Master

## Summary

- **Files Changed:** [count]
- **Critical Issues:** [count]
- **Warnings:** [count]
- **Suggestions:** [count]

## Critical Issues 🔴

[List bugs and security vulnerabilities that must be fixed]

- **Issue 1: [Title]**
  - **File:** `path/to/file.php`
  - **Line:** [line number(s)]
  - **Problem:** [Clear description]
  - **Impact:** [What could go wrong]
  - **Solution:**

```[language]
// Suggested fix
```

## Warnings ⚠️

[List issues that should be addressed but aren't blocking]

- **Issue 1: [Title]**
  - **File:** `path/to/file.php`
  - **Line:** [line number(s)]
  - **Problem:** [Clear description]
  - **Impact:** [What could go wrong]
  - **Solution:**

```[language]
// Suggested fix
```

## Suggestions 💡

[List improvements for better code quality]

- **Issue 1: [Title]**
  - **File:** `path/to/file.php`
  - **Line:** [line number(s)]
  - **Problem:** [Clear description]
  - **Impact:** [What could go wrong]
  - **Solution:**

```[language]
// Suggested fix
```

## Positive Observations ✅

[Highlight good practices observed in the code]

## Files Reviewed

- [ ] `file1.php` - [brief status]
- [ ] `file2.vue` - [brief status]
````
