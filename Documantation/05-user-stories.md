# 05. User Stories & Backlog

## Ruqi Store — User Stories & Product Backlog

---

## 5.1 Epic Overview

| Epic | Description | Story Count |
| :--- | :--- | :---: |
| **E1: Identity & Access Management** | Customer registration, secure authentication, role-based access, and profile management. | 3 |
| **E2: Product Catalog & Discovery** | Browse, search, filter, and view detailed furniture products. | 2 |
| **E3: Shopping Cart & Checkout** | Manage the persistent cart and complete atomic order checkout. | 3 |
| **E4: Order & Payment Management** | Track orders and manage payment decisions according to assigned roles. | 3 |
| **E5: Customer Reviews & Address Management** | Submit verified product reviews and manage delivery addresses. | 2 |
| **E6: Store Operations** | Manage products, categories, inventory, and order fulfillment. | 3 |
| **E7: Administration & Reporting** | Manage users, roles, reviews, reports, and immutable audit logs. | 3 |

---

# 5.2 User Stories with Acceptance Criteria

## Epic 1: Identity & Access Management

### US-001: Customer Registration

> As a **guest visitor**, I want to **create a customer account using my personal information and email address** so that I can **place orders and access customer-specific features**.

**Acceptance Criteria:**

- **Given** I provide a valid full name, email, password, and confirmation password, **when** I submit the registration form, **then** the system creates my account successfully.
- **Given** the registration is successful, **when** the account is created, **then** the system assigns the `Customer` role automatically.
- **Given** the email address already exists, **when** I submit the form, **then** the system displays a validation error and prevents duplicate registration.
- **Given** the password does not satisfy the configured Identity requirements, **when** I submit the form, **then** the system displays an appropriate validation error.

**Story Points:** 3 | **Priority:** Must

---

### US-002: Secure Login

> As a **registered user**, I want to **log in securely using my email and password** so that I can **access features according to my assigned role**.

**Acceptance Criteria:**

- **Given** valid credentials, **when** I submit the login form, **then** ASP.NET Core Identity authenticates me and issues a secure authentication cookie.
- **Given** valid credentials, **when** authentication succeeds, **then** I am redirected according to my role.
- **Given** invalid credentials, **when** I attempt to log in, **then** the system displays an invalid-credentials message.
- **Given** 5 consecutive failed login attempts, **when** another login attempt is made, **then** the account is locked for 15 minutes.
- **Given** my account has been deactivated by an Administrator, **when** I attempt to log in, **then** access is denied.

**Story Points:** 3 | **Priority:** Must

---

### US-003: Manage Profile & Addresses

> As an **authenticated customer**, I want to **manage my profile and saved delivery addresses** so that I can **use accurate information during checkout**.

**Acceptance Criteria:**

- **Given** I am authenticated, **when** I open my profile, **then** I can update my permitted profile information.
- **Given** I am authenticated, **when** I open the address book, **then** I can view my saved addresses.
- **Given** I have fewer than 5 saved addresses, **when** I add a new address, **then** the address is saved successfully.
- **Given** I already have 5 saved addresses, **when** I attempt to add another address, **then** the system rejects the operation.
- **Given** I update or delete an address, **then** the operation only affects my own address records.

**Story Points:** 3 | **Priority:** Should

---

# Epic 2: Product Catalog & Discovery

### US-004: Browse and Search Furniture Catalog

> As a **customer or guest**, I want to **browse and search the furniture catalog** so that I can **quickly find products I am interested in**.

**Acceptance Criteria:**

- **Given** I open the Products page, **when** the catalog loads, **then** only active products belonging to active categories are displayed.
- **Given** the catalog contains more than 20 products, **then** products are displayed using pagination with 20 items per page.
- **Given** I enter a product keyword, **when** I perform a search, **then** matching product names are returned.
- **Given** no products match my search, **then** the system displays an appropriate empty-results message.

**Story Points:** 3 | **Priority:** Must

---

### US-005: Filter and View Product Details

> As a **customer or guest**, I want to **filter furniture products and view detailed product information** so that I can **make an informed purchasing decision**.

**Acceptance Criteria:**

- **Given** I select a category, price range, material, or availability filter, **when** the catalog is refreshed, **then** only matching products are displayed.
- **Given** I open a product detail page, **then** the system displays the product name, SKU, price, stock status, dimensions, weight, material, and description.
- **Given** a product has multiple images, **then** the system displays its image gallery.
- **Given** a product is out of stock, **then** the system clearly displays its stock status and prevents an invalid purchase quantity.

**Story Points:** 3 | **Priority:** Must

---

# Epic 3: Shopping Cart & Checkout

### US-006: Manage Shopping Cart

> As a **customer**, I want to **add, update, and remove furniture products from my shopping cart** so that I can **prepare my order before checkout**.

**Acceptance Criteria:**

- **Given** I am authenticated and a product has available stock, **when** I add it to my cart, **then** the product is added to my persistent cart.
- **Given** a product already exists in my cart, **when** I add more quantity, **then** the cart quantity is updated.
- **Given** I increase an item's quantity beyond available stock, **then** the system rejects the operation.
- **Given** I remove a cart item, **then** it is removed from my cart.
- **Given** I access my cart again after logging in, **then** my saved cart items remain available.

**Story Points:** 5 | **Priority:** Must

---

### US-007: Checkout and Place Order

> As a **customer**, I want to **complete checkout using my delivery address and payment method** so that I can **place a furniture order securely**.

**Acceptance Criteria:**

- **Given** my cart contains items, **when** I proceed to checkout, **then** I can select a saved address or enter a new delivery address.
- **Given** I select a supported payment method, **then** the system includes it in the order.
- **Given** I confirm the order, **then** the system creates the order inside an atomic EF Core transaction.
- **Given** checkout succeeds, **then** product stock is deducted, order items are created, and my cart is cleared.
- **Given** any operation inside the transaction fails, **then** the transaction is rolled back and the cart and stock remain unchanged.
- **Given** the order is created, **then** its initial `FulfillmentStatus` is `Pending` and its initial `PaymentStatus` is `Unpaid`.

**Story Points:** 8 | **Priority:** Must

---

### US-008: Preserve Order Price and Product Information

> As a **customer**, I want **my order to preserve the product price and name at the time of purchase** so that **future catalog changes do not alter my historical order**.

**Acceptance Criteria:**

- **Given** I place an order, **then** the current product price is stored in `OrderItem.UnitPrice`.
- **Given** the product price changes later, **then** the historical `OrderItem.UnitPrice` remains unchanged.
- **Given** the product name changes later, **then** `OrderItem.ProductNameSnapshot` remains unchanged.
- **Given** a product is soft-deleted after purchase, **then** its historical order items remain intact.

**Story Points:** 3 | **Priority:** Must

---

# Epic 4: Order & Payment Management

### US-009: Track Order Status

> As a **customer**, I want to **view the status of my orders** so that I can **follow the progress of my purchase**.

**Acceptance Criteria:**

- **Given** I open my order history, **then** I can view all orders belonging to my account.
- **Given** I open an order, **then** I can view its fulfillment status, payment status, items, delivery address, and total.
- **Given** the Store Manager changes the fulfillment status, **then** the updated status is displayed when I next view the order.
- **Given** I attempt to access another customer's order, **then** the system denies access.
- **Given** an order does not exist, **then** the system returns an appropriate not-found response.

**Story Points:** 3 | **Priority:** Must

---

### US-010: Payment Officer Reviews and Updates Payment Status

> As a **Payment Officer**, I want to **review and update payment statuses** so that I can **accurately maintain payment records for customer orders**.

**Acceptance Criteria:**

- **Given** I am authenticated as a Payment Officer, **when** I view payment orders, **then** I can review the payment status of orders.
- **Given** an order has an `Unpaid` status, **when** I mark it as paid, **then** its `PaymentStatus` changes to `Paid`.
- **Given** I reject a payment, **then** the system requires a non-empty rejection reason.
- **Given** a payment status changes, **then** a corresponding immutable `PaymentLog` record is created.
- **Given** I am not a Payment Officer, **then** I cannot perform payment status operations.

**Story Points:** 5 | **Priority:** Must

---

### US-011: Maintain Payment History

> As a **Payment Officer**, I want to **view the payment history log** so that I can **review previous payment decisions and maintain accountability**.

**Acceptance Criteria:**

- **Given** I am a Payment Officer, **when** I open payment history, **then** I can view recorded payment status changes.
- **Given** a payment log exists, **then** it displays the previous status, new status, actor, rejection reason when applicable, and timestamp.
- **Given** a PaymentLog record exists, **then** it cannot be modified or deleted through the application.

**Story Points:** 3 | **Priority:** Must

---

# Epic 5: Customer Reviews & Address Management

### US-012: Submit Verified Product Review

> As a **customer**, I want to **review furniture products I have purchased and received** so that I can **share my experience with other customers**.

**Acceptance Criteria:**

- **Given** I have a Delivered order containing the product, **when** I submit a review, **then** the system accepts the review.
- **Given** I have not purchased the product through a Delivered order, **when** I attempt to submit a review, **then** the system rejects the submission.
- **Given** I have already reviewed the product, **when** I attempt to submit another review, **then** the system rejects the duplicate review.
- **Given** a valid review is submitted, **then** `IsVerifiedPurchase` is set to `true`.
- **Given** a valid review is saved, **then** the product's average rating is recalculated.

**Story Points:** 5 | **Priority:** Must

---

### US-013: Manage Delivery Addresses

> As a **customer**, I want to **add, edit, delete, and select a default delivery address** so that I can **use the correct delivery information when placing an order**.

**Acceptance Criteria:**

- **Given** I have fewer than 5 addresses, **when** I add a valid address, **then** the address is stored.
- **Given** I select an address as default, **then** it becomes my default delivery address.
- **Given** I edit an address, **then** only my selected address is updated.
- **Given** I delete an address, **then** it is removed from my address book.
- **Given** I already have 5 addresses, **then** the system prevents creating a sixth address.

**Story Points:** 3 | **Priority:** Should

---

# Epic 6: Store Operations

### US-014: Manage Product Catalog

> As a **Store Manager**, I want to **create, edit, activate, deactivate, and manage furniture products** so that I can **keep the store catalog accurate and up to date**.

**Acceptance Criteria:**

- **Given** I am a Store Manager, **when** I create a product with valid information, **then** the product is saved successfully.
- **Given** a SKU already exists, **then** the system rejects the new product.
- **Given** I upload product images, **then** the system validates their type and size and allows a maximum of 8 images per product.
- **Given** I deactivate a product, **then** its `IsActive` value becomes `false` and it disappears from the customer catalog.
- **Given** a product has historical orders, **then** the product is never hard-deleted.

**Story Points:** 8 | **Priority:** Must

---

### US-015: Manage Inventory and Categories

> As a **Store Manager**, I want to **manage product stock and furniture categories** so that I can **maintain accurate inventory and catalog organization**.

**Acceptance Criteria:**

- **Given** I update product stock, **then** `StockQuantity` cannot become negative.
- **Given** stock is below the configured threshold, **then** the product is identified as low stock in the manager dashboard.
- **Given** I create or rename a category, **then** the category information is updated in the catalog.
- **Given** I deactivate a category, **then** its associated products are hidden from the customer catalog.
- **Given** I reorder categories, **then** their `SortOrder` values determine their display order.

**Story Points:** 5 | **Priority:** Must

---

### US-016: Manage Order Fulfillment

> As a **Store Manager**, I want to **update the fulfillment status of customer orders** so that I can **manage the delivery workflow**.

**Acceptance Criteria:**

- **Given** an order is `Pending`, **when** I update it, **then** it may move to `Processing`.
- **Given** an order is `Processing`, **then** it may move to `Shipped`.
- **Given** an order is `Shipped`, **then** it may move to `Delivered`.
- **Given** an order is not yet Delivered, **then** it may be cancelled according to the defined business rules.
- **Given** I attempt an invalid transition, **then** the system rejects the operation with an `InvalidStatusTransitionException`.
- **Given** I am a Store Manager, **then** I can modify `FulfillmentStatus` but not `PaymentStatus`.

**Story Points:** 5 | **Priority:** Must

---

# Epic 7: Administration & Reporting

### US-017: Manage Users and Roles

> As an **Administrator**, I want to **manage user accounts and assigned roles** so that I can **control system access and permissions**.

**Acceptance Criteria:**

- **Given** I am an Administrator, **when** I view users, **then** I can search and filter users.
- **Given** I deactivate a user, **then** the user's account becomes inactive while their data remains preserved.
- **Given** I activate a previously deactivated user, **then** the account becomes active again.
- **Given** I assign or revoke an allowed role, **then** the role change is recorded and applied according to the authentication rules.
- **Given** I attempt to deactivate my own account, **then** the system rejects the operation.
- **Given** I attempt to modify my own permissions, **then** the system rejects the operation.

**Story Points:** 5 | **Priority:** Must

---

### US-018: Moderate Product Reviews

> As an **Administrator**, I want to **moderate customer reviews** so that I can **maintain appropriate and trustworthy product content**.

**Acceptance Criteria:**

- **Given** I am an Administrator, **when** I view reviews, **then** I can filter and review customer submissions.
- **Given** a review violates store policies, **when** I moderate it, **then** its `IsVisible` value can be set to `false`.
- **Given** I hide a review, **then** the review is no longer displayed publicly.
- **Given** I perform a review moderation action, **then** an `AuditLog` entry is created.

**Story Points:** 3 | **Priority:** Must

---

### US-019: View Reports and Audit Logs

> As an **Administrator**, I want to **view system reports and immutable audit logs** so that I can **monitor store activity and maintain accountability**.

**Acceptance Criteria:**

- **Given** I am an Administrator, **when** I open Reports, **then** I can generate supported CSV reports.
- **Given** I request a revenue report, **then** the system can generate revenue data by the selected period.
- **Given** I request an inventory report, **then** the system provides the current inventory summary.
- **Given** I open Audit Logs, **then** I can view administrative actions with actor, action type, target entity, target ID, description, and timestamp.
- **Given** an AuditLog exists, **then** it cannot be edited or deleted through the application.

**Story Points:** 5 | **Priority:** Must

---

# 5.3 Story Map Summary

```mermaid
block-beta
    columns 5

    block:header:5
        A["User Activities"]
    end

    B1["Identity & Access"]
    B2["Catalog & Discovery"]
    B3["Shopping & Checkout"]
    B4["Orders & Payments"]
    B5["Store & Administration"]

    C1["US-001 Registration"]
    C2["US-004 Browse & Search"]
    C3["US-006 Shopping Cart"]
    C4["US-009 Order Tracking"]
    C5["US-014 Product Management"]

    D1["US-002 Login"]
    D2["US-005 Product Details"]
    D3["US-007 Checkout"]
    D4["US-010 Payment Management"]
    D5["US-015 Inventory & Categories"]

    E1["US-003 Profile & Addresses"]
    E2["US-012 Reviews"]
    E3["US-008 Price Snapshots"]
    E4["US-011 Payment History"]
    E5["US-017 User & Role Management"]

    F1[" "]
    F2["US-013 Address Management"]
    F3[" "]
    F4[" "]
    F5["US-018 Reviews & Audit"]

    G1[" "]
    G2[" "]
    G3[" "]
    G4[" "]
    G5["US-019 Reports & Audit Logs"]
