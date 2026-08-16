# 04. Use Case Model

## Ruqi Store — Use Case Model

---

## 4.1 Actor Catalog

| Actor | Type | Description | Key Goals |
|-------|------|-------------|-----------|
| **Customer** | Primary | Registered customer who browses furniture products and places online orders | Browse products, search and filter catalog, manage cart, place orders, track orders, manage addresses, submit verified reviews |
| **Guest** | Primary | Unauthenticated visitor who can browse the public product catalog and access authentication pages | Browse products, search and filter catalog, view product details, register, log in |
| **Store Manager** | Primary | Employee responsible for product catalog, inventory, and order fulfillment | Manage products, categories, inventory, and fulfillment status |
| **Payment Officer** | Primary | Employee responsible for reviewing and managing payment status | Review payments, mark payments as Paid or Rejected, record payment decisions |
| **Administrator** | Primary | System administrator responsible for system-wide user and security management | Manage users, assign/revoke roles, moderate reviews, view reports, view payment history, and audit system actions |

---

## Authentication and Common User Capabilities

All official system roles are authenticated through **ASP.NET Core Identity**.

Common authentication and account capabilities such as registration, login, logout, profile management, and account security are provided through the shared Identity infrastructure.

Role-specific authorization is enforced through ASP.NET Core Identity roles and `[Authorize(Roles = "...")]`.

> **Note:** The system has four official authenticated roles: Customer, Store Manager, Payment Officer, and Administrator. Guest is an unauthenticated actor, not an authenticated system role. Guest can access public catalog functionality and authentication-related operations. Common authentication capabilities are shared infrastructure and are not represented as a separate system actor.

---

# 4.2 Use Case Diagram

```mermaid
graph TB

    subgraph RuqiStore["Ruqi Store E-Commerce System"]

        UC1((Customer Registration))
        UC2((Customer Login))
        UC3((Browse & Filter Product Catalog))
        UC4((View Product Details))
        UC5((Manage Shopping Cart))
        UC6((Checkout & Place Order))
        UC7((Track Order))
        UC8((Submit Verified Product Review))
        UC9((Manage Addresses))

        UC10((Manage Products & Categories))
        UC11((Manage Inventory))
        UC12((Update Order Fulfillment Status))

        UC13((Manage Payment Status))
        UC14((View Payment History))

        UC15((Manage Users & Roles))
        UC16((Moderate Product Reviews))
        UC17((View Audit Logs))
        UC18((Export Reports))

    end

    Customer[Customer]
    Guest[Guest]
    Manager[Store Manager]
    Payment[Payment Officer]
    Admin[Administrator]

    Guest --> UC1
    Guest --> UC2
    Guest --> UC3
    Guest --> UC4

    Customer --> UC2
    Customer --> UC3
    Customer --> UC4
    Customer --> UC5
    Customer --> UC6
    Customer --> UC7
    Customer --> UC8
    Customer --> UC9

    Manager --> UC2
    Manager --> UC10
    Manager --> UC11
    Manager --> UC12

    Payment --> UC2
    Payment --> UC13
    Payment --> UC14

    Admin --> UC2
    Admin --> UC14
    Admin --> UC15
    Admin --> UC16
    Admin --> UC17
    Admin --> UC18

    UC5 -.->|requires authentication| UC2
    UC6 -.->|requires authentication| UC2
    UC7 -.->|requires authentication| UC2
    UC8 -.->|requires authentication| UC2
    UC9 -.->|requires authentication| UC2

```

---

# 4.3 Use Case Relationships

## Authentication Dependency

Customer-specific operations require an authenticated session.

The following use cases require the customer to be logged in:

* Manage Shopping Cart
* Checkout and Place Order
* Track Order
* Manage Addresses
* Submit Verified Product Review

ASP.NET Core Identity manages authentication using secure authentication cookies. Authorization is enforced through role-based policies.

---

## Role-Based Authorization

Each protected use case is restricted to the appropriate system role.

| Use Case | Required Role |
| --- | --- |
| Customer Registration | Guest |
| Customer Login | Guest / Customer / Store Manager / Payment Officer / Administrator |
| Browse Product Catalog | Guest / Authenticated |
| View Product Details | Guest / Authenticated |
| Manage Shopping Cart | Customer |
| Checkout & Place Order | Customer |
| Track Order | Customer |
| Manage Addresses | Customer |
| Submit Verified Product Review | Customer |
| Manage Products & Categories | Store Manager |
| Manage Inventory | Store Manager |
| Update Order Fulfillment Status | Store Manager |
| Manage Payment Status | Payment Officer |
| View Payment History | Payment Officer / Administrator |
| Manage Users & Roles | Administrator |
| Moderate Product Reviews | Administrator |
| View Audit Logs | Administrator |
| Export Reports | Administrator |

---
# 4.4 Core Use Case List

| ID | Use Case | Primary Actor |
| --- | --- | --- |
| **UC-001** | Customer Registration | Guest |
| **UC-002** | Customer Login | Guest / Customer |
| **UC-003** | Browse & Filter Product Catalog | Customer / Guest |
| **UC-004** | View Product Details | Customer / Guest |
| **UC-005** | Manage Shopping Cart | Customer |
| **UC-006** | Checkout & Place Order | Customer |
| **UC-007** | Track Order | Customer |
| **UC-008** | Submit Verified Product Review | Customer |
| **UC-009** | Manage Addresses | Customer |
| **UC-010** | Manage Products & Categories | Store Manager |
| **UC-011** | Manage Inventory | Store Manager |
| **UC-012** | Update Order Fulfillment Status | Store Manager |
| **UC-013** | Manage Payment Status | Payment Officer |
| **UC-014** | Manage Users & Roles | Administrator |
| **UC-015** | Moderate Product Reviews | Administrator |
| **UC-016** | Export Reports | Administrator |
| **UC-017** | View Audit Logs | Administrator |
| **UC-018** | View Payment History | Payment Officer / Administrator |

> **Note:** Authentication is represented through the Customer Registration and Customer Login use cases and is implemented using ASP.NET Core Identity.

---

# 4.5 Fully Dressed Use Case — UC-006: Checkout & Place Order

| Field | Detail |
| --- | --- |
| **Use Case ID** | UC-006 |
| **Name** | Checkout & Place Order |
| **Primary Actor** | Customer |
| **Controller** | `OrdersController` |
| **Service** | `OrderService` |
| **Description** | A customer reviews their cart, selects or enters a delivery address, selects a supported payment method, and places an order. |
| **Preconditions** | Customer is authenticated; cart contains at least one item; products are active; sufficient stock is available. |
| **Postconditions** | Order is created; stock is deducted; order item prices and names are snapshotted; cart is cleared; order confirmation is displayed. |
| **Trigger** | Customer selects **"Proceed to Checkout"** and confirms the order. |

---

## Main Success Scenario

| Step | Action |
| --- | --- |
| 1 | Customer opens the shopping cart. |
| 2 | Customer selects **"Proceed to Checkout."** |
| 3 | System displays saved delivery addresses. |
| 4 | Customer selects an existing address or enters a new address. |
| 5 | Customer selects a payment method: **Cash on Delivery** or **Bank Transfer**. |
| 6 | System displays the order summary, including items, shipping, tax, and total amount. |
| 7 | Customer confirms the order. |
| 8 | `OrderService.PlaceOrderAsync()` starts an EF Core database transaction. |
| 9 | System validates that all requested quantities are still available. |
| 10 | System creates the `Order` with `FulfillmentStatus = Pending` and `PaymentStatus = Unpaid`. |
| 11 | System creates `OrderItems` using the current product price as `UnitPrice` and the current product name as `ProductNameSnapshot`. |
| 12 | System deducts the purchased quantities from product inventory. |
| 13 | System clears the customer's cart items. |
| 14 | System commits the transaction. |
| 15 | System displays the order confirmation page with the generated `OrderId`. |

---

## Alternative Flows

| ID | Condition | Steps |
| --- | --- | --- |
| **A1** | Customer has no saved address | Customer enters a new delivery address. The system validates and uses it for the order. |
| **A2** | Customer changes cart quantity before checkout | System recalculates totals and validates the new quantity against current stock. |
| **A3** | Customer selects a different saved address | System uses the selected address and creates an immutable delivery address snapshot for the order. |

---

## Exception Flows

| ID | Condition | Steps |
| --- | --- | --- |
| **E1** | Empty cart | System rejects checkout and displays an `EmptyCartException` message. |
| **E2** | Insufficient stock | System rejects the order and displays an `InsufficientStockException` message. |
| **E3** | Product becomes unavailable | System prevents checkout for the inactive or unavailable product. |
| **E4** | Database transaction failure | System rolls back the transaction. Stock and cart data remain unchanged. |

---

## Business Rules

* Checkout must be executed inside an atomic EF Core transaction.
* Product stock cannot become negative.
* `OrderItem.UnitPrice` is a permanent price snapshot.
* `OrderItem.ProductNameSnapshot` is a permanent product-name snapshot.
* Historical order prices must never change when product prices are updated.
* Cart items are removed only after successful order creation.
* The order starts with `FulfillmentStatus = Pending`.
* The order starts with `PaymentStatus = Unpaid`.
* Supported payment methods are:
  * `CashOnDelivery`
  * `BankTransfer`

---


# 4.6 Fully Dressed Use Case — UC-012: Update Order Fulfillment Status

| Field | Detail |
| --- | --- |
| **Use Case ID** | UC-012 |
| **Name** | Update Order Fulfillment Status |
| **Primary Actor** | Store Manager |
| **Controller** | `StoreManagerController` |
| **Service** | `OrderService` |
| **Description** | Store Manager updates the fulfillment progress of a customer order. |
| **Preconditions** | Store Manager is authenticated and authorized; target order exists. |
| **Postconditions** | Valid fulfillment status transition is saved with an updated timestamp. |
| **Trigger** | Store Manager selects a new fulfillment status for an order. |

---

## Main Success Scenario

| Step | Action |
| --- | --- |
| 1 | Store Manager opens `/Manager/Orders`. |
| 2 | System displays available orders. |
| 3 | Store Manager selects an order. |
| 4 | Store Manager selects a new fulfillment status. |
| 5 | `OrderService` validates the status transition. |
| 6 | System updates `FulfillmentStatus`. |
| 7 | System updates `UpdatedAt`. |
| 8 | System saves the changes through EF Core. |
| 9 | Customer sees the updated status on the next order-tracking request. |

---

## Valid Status Transitions

```text
Pending
   ↓
Processing
   ↓
Shipped
   ↓
Delivered
```

Cancellation is allowed from any status except `Delivered`:

```text
Pending ────────┐
Processing ─────┤
Shipped ────────┤
                ↓
            Cancelled
```

---

## Exception Flows

| ID | Condition | Result |
| --- | --- | --- |
| **E1** | Invalid transition | System rejects the request with `InvalidStatusTransitionException`. |
| **E2** | Order not found | System returns a not-found result. |
| **E3** | Unauthorized user | Access is denied by ASP.NET Core authorization. |

> **Important:** Store Manager can update `FulfillmentStatus` only. `PaymentStatus` is managed exclusively by the Payment Officer.

---

# 4.7 Fully Dressed Use Case — UC-013: Manage Payment Status

| Field | Detail |
| --- | --- |
| **Use Case ID** | UC-013 |
| **Name** | Manage Payment Status |
| **Primary Actor** | Payment Officer |
| **Controller** | `PaymentController` |
| **Service** | `PaymentService` |
| **Description** | Payment Officer reviews payment information and marks an order as Paid or Rejected. |
| **Preconditions** | Payment Officer is authenticated and authorized; target order exists. |
| **Postconditions** | Payment status is updated and an immutable `PaymentLog` record is created. |
| **Trigger** | Payment Officer selects an unpaid order for payment processing. |

---

## Main Success Scenario — Mark Paid

| Step | Action |
| --- | --- |
| 1 | Payment Officer opens the payment dashboard. |
| 2 | System displays orders requiring payment review. |
| 3 | Payment Officer selects an order. |
| 4 | Payment Officer selects **Mark Paid**. |
| 5 | `PaymentService` validates the current payment status. |
| 6 | System changes `PaymentStatus` from `Unpaid` to `Paid`. |
| 7 | System creates a `PaymentLog` containing the previous and new status. |
| 8 | System saves the changes. |
| 9 | Payment history reflects the new payment decision. |

---

## Alternative Flow — Reject Payment

| Step | Action |
| --- | --- |
| 1 | Payment Officer selects **Reject**. |
| 2 | System displays a rejection-reason field. |
| 3 | Payment Officer enters the reason. |
| 4 | `PaymentService` validates that the reason is not empty. |
| 5 | System changes `PaymentStatus` to `Rejected`. |
| 6 | System stores the rejection reason. |
| 7 | System creates an immutable `PaymentLog`. |

---

## Business Rules

* Only `PaymentOfficer` can change payment status.
* `PaymentStatus` values are:
  * `Unpaid`
  * `Paid`
  * `Rejected`
* A rejection requires a non-empty rejection reason.
* Every payment decision creates a `PaymentLog`.
* `PaymentLog` is append-only.
* Payment logs cannot be updated or deleted.

---

# 4.8 Fully Dressed Use Case — UC-008: Submit Verified Product Review

| Field | Detail |
| --- | --- |
| **Use Case ID** | UC-008 |
| **Name** | Submit Verified Product Review |
| **Primary Actor** | Customer |
| **Controller** | `ProductsController` |
| **Service** | `ReviewService` |
| **Description** | A customer submits a review for a product they previously purchased and received. |
| **Preconditions** | Customer is authenticated; customer has a Delivered order containing the product; customer has not already reviewed the product. |
| **Postconditions** | Review is stored with `IsVerifiedPurchase = true` and becomes visible according to the system moderation state. |
| **Trigger** | Customer selects the review option for an eligible purchased product. |

---

## Main Flow

| Step | Action |
| --- | --- |
| 1 | Customer opens the product details page. |
| 2 | System checks whether the customer has a Delivered order containing the product. |
| 3 | System checks whether the customer has already reviewed the product. |
| 4 | Customer enters a rating from 1 to 5 stars. |
| 5 | Customer optionally enters a title and comment. |
| 6 | Customer submits the review. |
| 7 | `ReviewService` validates the review rules. |
| 8 | System saves the review with `IsVerifiedPurchase = true`. |
| 9 | System recalculates the product's average rating. |

---

## Exception Flows

| ID | Condition | Result |
| --- | --- | --- |
| **E1** | No Delivered purchase | System rejects the review with `NoPurchaseFoundException`. |
| **E2** | Customer already reviewed product | System rejects the review with `DuplicateReviewException`. |
| **E3** | Invalid rating | System rejects the submitted rating. |

---

## Business Rules

* Only authenticated customers can submit reviews.
* A review requires a Delivered order containing the reviewed product.
* Each customer can submit only one review per product.
* Rating must be between 1 and 5.
* Verified purchases are marked using `IsVerifiedPurchase = true`.
* Review visibility is controlled through `IsVisible`.
* Administrators can moderate reviews.

---
