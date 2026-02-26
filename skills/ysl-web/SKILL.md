---
name: ysl-web
description: Laravel 12 public website: API gateway pattern, Guzzle HTTP client, Blade templates, device token management.
---

# Web Public Skill

You are working on the **E-commerce Public Website** — a Laravel 12 + PHP 8.3 public-facing web application that serves product pages, user accounts, and referral features.

## Tech Stack

- Laravel 12, PHP 8.3
- Guzzle HTTP Client 7.0
- Intervention Image 2.5
- Simple QRCode 4.2

## Architecture Overview

```
young_sia_web/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── BaseController.php     # Device token initialization
│   │   │   ├── ProductController.php  # Product pages
│   │   │   ├── AccountController.php  # User accounts
│   │   │   ├── PageController.php     # Static pages
│   │   │   ├── PostController.php     # Blog posts
│   │   │   ├── ReferralController.php # Referral system
│   │   │   └── TopUpController.php    # Top-up features
│   │   ├── Middleware/
│   │   └── Requests/
│   ├── Facades/
│   ├── Helpers/
│   ├── Mail/
│   └── Providers/
├── config/
│   └── custom.php                     # API endpoints, keys
├── resources/
│   └── views/                         # Blade templates
├── routes/
│   ├── web.php                        # Main entry, loads modular routes
│   └── web/                           # Modular route files
│       ├── product.php
│       ├── user.php
│       ├── page.php
│       └── ...
└── public/
```

## Key Pattern: API Gateway

This app acts as a **frontend gateway** to the main API (`young_sia_api`). Controllers call external API endpoints using Guzzle/HTTP facade.

## Workflow

When building a new feature:

1. **Create controller** → `app/Http/Controllers/{Name}Controller.php` extending `BaseController`
2. **Add route file** → `routes/web/{feature}.php`
3. **Create Blade views** → `resources/views/{feature}/`
4. **Configure API endpoint** → `config/custom.php` or `.env`

## References

- @references/controllers.md — BaseController pattern, API calls
- @references/routing.md — Modular route loading
