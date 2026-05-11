# TechShop — Simple Laravel E-Commerce
## Demonstrates: Form Validation + Events/Listeners

---

## ⚡ Quick Setup

```bash
# 1. Install dependencies
composer install

# 2. Copy environment file
cp .env.example .env

# 3. Generate app key
php artisan key:generate

# 4. Run the development server
php artisan serve
```

Open http://localhost:8000 in your browser.

---

## 📁 Project Structure (Key Files)

```
app/
├── Events/
│   ├── OrderPlaced.php          ← EVENT: fired when order is placed
│   └── ItemAddedToCart.php      ← EVENT: fired when item added to cart
│
├── Listeners/
│   ├── SendOrderConfirmationEmail.php  ← LISTENER: reacts to OrderPlaced
│   └── LogCartActivity.php             ← LISTENER: reacts to ItemAddedToCart
│
├── Http/
│   ├── Controllers/
│   │   ├── ProductController.php
│   │   ├── CartController.php   ← fires ItemAddedToCart event
│   │   └── OrderController.php  ← fires OrderPlaced event
│   │
│   └── Requests/
│       ├── CheckoutRequest.php  ← VALIDATION: checkout form rules
│       └── AddToCartRequest.php ← VALIDATION: add-to-cart rules
│
└── Providers/
    └── EventServiceProvider.php ← WIRES Events → Listeners
```

---

## ✅ Validation (CheckoutRequest.php)

Rules enforced on checkout:

| Field           | Rules                                      |
|-----------------|--------------------------------------------|
| customer_name   | required, min:2, max:100                   |
| customer_email  | required, valid email format               |
| phone           | required, 10–15 digits only (regex)        |
| address         | required, min:10 characters               |
| city            | required                                   |
| payment_method  | required, must be: cod / card / bank       |
| card_number     | required_if card, exactly 16 digits        |
| card_expiry     | required_if card, format MM/YY             |
| card_cvv        | required_if card, exactly 3 digits         |

Try submitting with empty fields — you'll see inline error messages and the form retains your old values via `old()`.

---

## 🔔 Events & Listeners

### Event 1: `ItemAddedToCart`
- **Fired in:** `CartController@add`
- **Listener:** `LogCartActivity` → logs to `storage/logs/laravel.log`

### Event 2: `OrderPlaced`
- **Fired in:** `OrderController@store` (only after validation passes)
- **Listener:** `SendOrderConfirmationEmail` → logs to `storage/logs/laravel.log`

All events/listeners are registered in `app/Providers/EventServiceProvider.php`.

To see events firing:
```bash
tail -f storage/logs/laravel.log
```

---

## 🗺️ Routes

| Method | URL                      | Description              |
|--------|--------------------------|--------------------------|
| GET    | /                        | Product listing          |
| GET    | /cart                    | View cart                |
| POST   | /cart/add                | Add item (validated)     |
| POST   | /cart/remove             | Remove item              |
| GET    | /checkout                | Checkout form            |
| POST   | /orders                  | Place order (validated)  |
| GET    | /orders/{id}/confirmation| Confirmation page        |

---

## 🛠️ Requirements
- PHP 8.1+
- Composer
- Laravel 10
