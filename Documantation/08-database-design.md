# 08. Database Design

## 8.1 Entity-Relationship Diagram (ERD)

This diagram defines the relational database model for **Ruqi Store**, representing user accounts, product catalog, persistent shopping carts, customer addresses, order processing, payment records, reviews, and system audit logs.

```mermaid
erDiagram

    APPLICATION_USERS ||--o{ CARTS : "owns"
    APPLICATION_USERS ||--o{ ADDRESSES : "has"
    APPLICATION_USERS ||--o{ ORDERS : "places"
    APPLICATION_USERS ||--o{ PRODUCT_REVIEWS : "writes"
    APPLICATION_USERS ||--o{ PAYMENT_LOGS : "processes"
    APPLICATION_USERS ||--o{ AUDIT_LOGS : "generates"

    CATEGORIES ||--o{ PRODUCTS : "contains"
    PRODUCTS ||--o{ PRODUCT_IMAGES : "has"
    PRODUCTS ||--o{ CART_ITEMS : "referenced by"
    PRODUCTS ||--o{ ORDER_ITEMS : "referenced by"
    PRODUCTS ||--o{ PRODUCT_REVIEWS : "receives"

    CARTS ||--o{ CART_ITEMS : "contains"

    ORDERS ||--o{ ORDER_ITEMS : "contains"
    ORDERS ||--|| PAYMENTS : "has"
    ORDERS ||--o{ PAYMENT_LOGS : "records"

    APPLICATION_USERS {
        string user_id PK
        string email UK
        string password_hash
        boolean is_active
        datetime created_at
    }

    CATEGORIES {
        int category_id PK
        string name UK
        string description
        boolean is_active
    }

    PRODUCTS {
        int product_id PK
        string name
        string product_name
        string sku UK
        decimal price
        int stock_quantity
        string material
        string dimensions
        boolean is_active
        int category_id FK
        datetime created_at
    }

    PRODUCT_IMAGES {
        int image_id PK
        int product_id FK
        string file_name
        string file_path
        boolean is_primary
        datetime uploaded_at
    }

    CARTS {
        int cart_id PK
        string user_id FK
        datetime created_at
        datetime updated_at
    }

    CART_ITEMS {
        int cart_item_id PK
        int cart_id FK
        int product_id FK
        int quantity
    }

    ADDRESSES {
        int address_id PK
        string user_id FK
        string address_line
        string city
        string phone_number
        boolean is_default
    }

    ORDERS {
        int order_id PK
        string user_id FK
        datetime order_date
        string fulfillment_status
        string payment_status
        decimal total_amount
        string shipping_address_snapshot
    }

    ORDER_ITEMS {
        int order_item_id PK
        int order_id FK
        int product_id FK
        string product_name_snapshot
        decimal unit_price
        int quantity
    }

    PAYMENTS {
        int payment_id PK
        int order_id FK
        decimal amount
        string payment_status
        datetime processed_at
    }

    PAYMENT_LOGS {
        int payment_log_id PK
        int order_id FK
        string user_id FK
        string action
        decimal amount
        datetime timestamp
    }

    PRODUCT_REVIEWS {
        int review_id PK
        string user_id FK
        int product_id FK
        int rating
        string comment
        string visibility
        datetime created_at
    }

    AUDIT_LOGS {
        int audit_log_id PK
        string user_id FK
        string action
        string entity_name
        int entity_id
        datetime timestamp
    }
```
## 8.2 Normalized Schema (3NF)

The relational database schema of **Ruqi Store** is designed according to **Third Normal Form (3NF)** principles to minimize data redundancy, maintain referential integrity, and prevent insertion, update, and deletion anomalies.

---

## Core Identity & Role Management

### application_users

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| user_id | VARCHAR(450) | PK | ASP.NET Core Identity user identifier |
| email | VARCHAR(256) | NOT NULL, UNIQUE | Primary login credential |
| password_hash | VARCHAR(MAX) | NOT NULL | Password hash managed by ASP.NET Core Identity |
| is_active | BOOLEAN | NOT NULL, DEFAULT TRUE | Controls whether the account is active |
| created_at | DATETIME2 | NOT NULL | Account creation timestamp |

### Identity Roles

Roles are managed through **ASP.NET Core Identity** rather than a custom role column in the application user table.

The defined system roles are:

- `Customer`
- `Store Manager`
- `Payment Officer`
- `Administrator`

Role assignments determine authorization for protected controllers, actions, and administrative functions.

---

## Catalog & Inventory Tables

### categories

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| category_id | INT | PK, AUTO_INCREMENT | Unique category identifier |
| name | VARCHAR(100) | NOT NULL, UNIQUE | Furniture category name |
| description | NVARCHAR(500) | NULL | Category description |
| is_active | BOOLEAN | NOT NULL, DEFAULT TRUE | Controls category visibility |

### products

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| product_id | INT | PK, AUTO_INCREMENT | Unique product identifier |
| name | NVARCHAR(200) | NOT NULL | Public product name |
| sku | VARCHAR(50) | NOT NULL, UNIQUE | Stock Keeping Unit |
| description | NVARCHAR(MAX) | NULL | Detailed product description |
| price | DECIMAL(18,2) | NOT NULL, CHECK (price >= 0) | Current selling price |
| stock_quantity | INT | NOT NULL, CHECK (stock_quantity >= 0) | Available physical stock |
| material | NVARCHAR(150) | NULL | Product material |
| dimensions | NVARCHAR(200) | NULL | Product dimensions |
| is_active | BOOLEAN | NOT NULL, DEFAULT TRUE | Soft-delete / visibility flag |
| category_id | INT | FK → categories | Product category |
| created_at | DATETIME2 | NOT NULL | Product creation timestamp |

### product_images

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| image_id | INT | PK, AUTO_INCREMENT | Unique image identifier |
| product_id | INT | FK → products | Associated furniture product |
| file_name | NVARCHAR(255) | NOT NULL | Uploaded image file name |
| file_path | NVARCHAR(500) | NOT NULL | Server-side upload path |
| is_primary | BOOLEAN | NOT NULL, DEFAULT FALSE | Indicates the primary product image |
| uploaded_at | DATETIME2 | NOT NULL | Upload timestamp |

Product images are stored through the application's file-upload mechanism and associated with their corresponding product records.

---

## Customer Data

### addresses

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| address_id | INT | PK, AUTO_INCREMENT | Unique address identifier |
| user_id | VARCHAR(450) | FK → application_users | Address owner |
| address_line | NVARCHAR(500) | NOT NULL | Saved shipping address |
| city | NVARCHAR(100) | NOT NULL | Customer city |
| phone_number | VARCHAR(20) | NULL | Contact phone number |
| is_default | BOOLEAN | NOT NULL, DEFAULT FALSE | Default shipping address |

Each customer may maintain multiple saved addresses, subject to the system limit of **five addresses per customer**.

---

## Carts

### carts

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| cart_id | INT | PK, AUTO_INCREMENT | Unique shopping cart identifier |
| user_id | VARCHAR(450) | FK → application_users, UNIQUE | Customer who owns the cart |
| created_at | DATETIME2 | NOT NULL | Cart creation timestamp |
| updated_at | DATETIME2 | NOT NULL | Last cart modification timestamp |

Each customer has exactly **one persistent shopping cart**.

### cart_items

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| cart_item_id | INT | PK, AUTO_INCREMENT | Unique cart item identifier |
| cart_id | INT | FK → carts, ON DELETE CASCADE | Parent shopping cart |
| product_id | INT | FK → products | Selected product |
| quantity | INT | NOT NULL, CHECK (quantity > 0) | Requested quantity |

**Unique Constraint**

```text
UNIQUE(cart_id, product_id)

```
This constraint prevents duplicate entries for the same product in a customer's cart.

---

## Checkout & Ordering Tables

### orders

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| order_id | INT | PK, AUTO_INCREMENT | Unique order identifier |
| user_id | VARCHAR(450) | FK → application_users | Customer who placed the order |
| order_date | DATETIME2 | NOT NULL | Order creation timestamp |
| fulfillment_status | VARCHAR(50) | NOT NULL | Current fulfillment state |
| payment_status | VARCHAR(50) | NOT NULL | Current payment state |
| total_amount | DECIMAL(18,2) | NOT NULL | Final order total |
| shipping_address_snapshot | NVARCHAR(500) | NOT NULL | Address captured at checkout |

### order_items

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| order_item_id | INT | PK, AUTO_INCREMENT | Unique order line identifier |
| order_id | INT | FK → orders, ON DELETE CASCADE | Parent order |
| product_id | INT | FK → products | Purchased product reference |
| product_name_snapshot | NVARCHAR(200) | NOT NULL | Product name at purchase time |
| unit_price | DECIMAL(18,2) | NOT NULL | Immutable price snapshot |
| quantity | INT | NOT NULL, CHECK (quantity > 0) | Purchased quantity |

The `unit_price` and `product_name_snapshot` fields preserve historical order information even when the original product is later updated.

---

## Payment Management

### payments

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| payment_id | INT | PK, AUTO_INCREMENT | Unique payment identifier |
| order_id | INT | FK → orders, UNIQUE | Associated order |
| amount | DECIMAL(18,2) | NOT NULL | Payment amount |
| payment_status | VARCHAR(50) | NOT NULL | Pending, Paid, or Rejected |
| processed_at | DATETIME2 | NULL | Payment processing timestamp |

### payment_logs

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| payment_log_id | INT | PK, AUTO_INCREMENT | Unique payment log identifier |
| order_id | INT | FK → orders | Related order |
| user_id | VARCHAR(450) | FK → application_users | Payment officer responsible for the action |
| action | VARCHAR(250) | NOT NULL | Payment operation performed |
| amount | DECIMAL(18,2) | NULL | Amount involved in the operation |
| timestamp | DATETIME2 | NOT NULL | Action timestamp |

Payment logs are **append-only** and cannot be edited or deleted through normal application operations.

---

## Product Reviews

### product_reviews

| Column | Type | Constraints | Notes |
| :--- | :--- | :--- | :--- |
| review_id | INT | PK, AUTO_INCREMENT | Unique review identifier |
| user_id | VARCHAR(450) | FK → application_users | Customer who submitted the review |
| product_id | INT | FK → products | Reviewed product |
| rating | INT | NOT NULL, CHECK (rating BETWEEN 1 AND 5) | Customer rating |
| comment | NVARCHAR(2000) | NULL | Review text |
| visibility | VARCHAR(50) | NOT NULL | Visible or Hidden |
| created_at | DATETIME2 | NOT NULL | Review submission timestamp |

**Unique Constraint**

```text
UNIQUE(user_id, product_id)
```

# Technical & Data Integrity Rules

Role-based authorization is enforced using ASP.NET Core MVC authorization mechanisms such as:

```csharp
[Authorize(Roles = "Administrator")]
```

---

## Dynamic Shopping Carts & Uniqueness

Each customer is allowed **exactly one persistent shopping cart**.

This rule is enforced through a unique constraint on:

```text
carts.user_id
```

Products placed in the cart are represented through `cart_items`.

The composite constraint:

```sql
UNIQUE(cart_id, product_id)
```

ensures that the same product cannot appear as multiple separate rows within one customer's cart.

---

## The Price Snapshot Pattern

To preserve historical accuracy, the `order_items` table stores:

```text
unit_price
product_name_snapshot
```

During checkout, the system copies the current product price and name into the order item.

Future catalog changes therefore do not modify historical orders.

---

## Atomic Checkout & Inventory Protection

Checkout operations are executed within an **atomic Entity Framework Core database transaction**.

The transaction covers:

1. Stock validation.
2. Inventory deduction.
3. Order creation.
4. Order item creation.
5. Price and product-name snapshot creation.
6. Shopping cart cleanup.

If any operation fails, the transaction is rolled back to prevent inconsistent inventory or incomplete orders.

The system must also prevent:

```text
stock_quantity < 0
```

---

## Cascade and Deletion Rules

### Order Isolation

Deleting an order removes its dependent order items according to the configured relationship.

```text
orders
   |
   └── order_items
```

This prevents orphaned order-item records.

### Catalog Safeguard

Products referenced by historical orders must not be physically deleted.

Instead, the system uses **soft deletion**:

```text
products.is_active = FALSE
```

Inactive products are excluded from normal customer catalog results while historical order records remain intact.

### Cart Cleanup

Cart items are dependent on their parent cart.

```text
carts
   |
   └── cart_items
```

Deleting a cart therefore removes its associated cart items through cascading behavior.

---

## Customer Data Isolation

Customer-owned records must be protected so that a customer can access only their own:

* Shopping cart
* Cart items
* Saved addresses
* Orders
* Payment-related customer information
* Product reviews

Authorization and ownership checks are enforced at the application/service layer.

---

## Payment Data Integrity

Payment status is managed exclusively by the **Payment Officer**.

Store Managers can manage order fulfillment operations but cannot modify payment status.

Payment actions are recorded in the append-only `payment_logs` table for accountability.

---

## Audit Logging

Administrative and other configured system actions are recorded in `audit_logs`.

Each audit entry contains:

* Responsible user
* Action
* Affected entity
* Affected record identifier
* Timestamp

Audit records are append-only and are not editable through normal application functionality.

---

## Development and Production Database Strategy

During development, **SQLite** is used as the development database to simplify local setup and team sharing.

The production environment is planned to use **SQL Server**.

Entity Framework Core migrations are used to maintain the database schema consistently across environments.

---

[← Previous: UML Behavioral Models](./07-uml-behavioral.md) | [Back to Index](./00-index.md) | [Next: Architectural Design →](./09-architecture.md)
