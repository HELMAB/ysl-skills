# Web Architecture

Root path: `young_sia_web/`

```text
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
