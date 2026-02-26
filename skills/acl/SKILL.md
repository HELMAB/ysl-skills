# ACL Skill

You are working on the **Youngsia Access Control** system — role-based permissions across the API and frontend applications.

## Overview

The platform uses a **role-based access control** system with 14 predefined roles. The API enforces access via middleware, while frontend apps filter UI elements by role.

## Roles

| ID | Constant | Description |
|----|----------|-------------|
| 1 | ADMIN | Full system access |
| 2 | OPERATION | Operations management |
| 3 | WAREHOUSE | Warehouse operations |
| 4 | USER | Regular end user |
| 5 | ACCOUNTANT | Financial operations |
| 6 | PUBLISHER | Content publishing |
| 7 | DRIVER | Delivery driver |
| 8 | SELLER | Shop owner (full seller access) |
| 9 | CUSTOMER_SERVICE | Customer support |
| 10 | DIGITAL_MARKETING | Marketing operations |
| 11 | DIGITAL_SALE | Digital sales |
| 12 | PARTNER | Business partner |
| 13 | SELLER_STAFF | Seller staff member |
| 14 | SELLER_EDITOR | Seller product editor (catalog only) |

## References

- @references/permissions.md — Permission enforcement, role constants
- @references/middleware.md — Permission middleware, route protection
