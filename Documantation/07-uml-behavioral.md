# 07. UML Behavioral Models

## Ruqi Store — Behavioral System Models

---

## 7.1 Sequence Diagram: Checkout & Place Order

This diagram illustrates the interaction between the customer, MVC controllers, service layer, repository/data access layer, and database during the checkout process.

The checkout operation is executed as an atomic transaction to ensure stock consistency, immutable price snapshots, order creation, and cart clearance.

```mermaid
sequenceDiagram
    autonumber

    actor C as Customer
    participant UI as Razor View
    participant CC as CartController
    participant OC as OrdersController
    participant OS as OrderService
    participant IS as InventoryService
    participant DB as EF Core / Database

    C->>UI: Review shopping cart
    UI->>CC: GET /Cart

    CC->>OS: GetCartAsync(currentUserId)
    OS->>DB: Load Cart and CartItems
    DB-->>OS: Cart data
    OS-->>CC: Cart data
    CC-->>UI: Render cart

    C->>UI: Proceed to Checkout
    UI->>OC: GET /Orders/Checkout

    OC->>OS: ValidateCheckoutAsync(currentUserId)
    OS->>IS: ValidateStockAsync(cartItems)
    IS->>DB: Read current stock
    DB-->>IS: Stock quantities
    IS-->>OS: Stock validation result

    alt Stock insufficient
        OS-->>OC: InsufficientStockException
        OC-->>UI: Display stock error
        UI-->>C: Update cart and retry
    else Stock available
        OS-->>OC: Checkout valid

        C->>UI: Confirm order
        UI->>OC: POST /Orders/PlaceOrder

        OC->>OS: PlaceOrderAsync(currentUserId, addressId)

        OS->>DB: BEGIN TRANSACTION

        loop For each CartItem
            OS->>DB: Validate current StockQuantity
            OS->>DB: Create OrderItem
            Note over OS,DB: Save UnitPrice and ProductNameSnapshot
            OS->>DB: Decrement StockQuantity
        end

        OS->>DB: Create Order
        OS->>DB: Clear CartItems
        OS->>DB: COMMIT TRANSACTION

        DB-->>OS: Transaction committed
        OS-->>OC: OrderId
        OC-->>UI: Redirect to Confirmation
        UI-->>C: Display order confirmation
    end
```

### Behavioral Notes

- Checkout is handled through **ASP.NET Core MVC controllers and the Service Layer**.
- Business rules are enforced inside the Service Layer.
- EF Core manages database operations and transactions.
- `OrderItem.UnitPrice` is captured as an immutable price snapshot.
- `OrderItem.ProductNameSnapshot` preserves the product name at the time of purchase.
- Stock cannot become negative.
- The shopping cart is cleared only after successful order creation.
- If any transaction operation fails, the transaction is rolled back.

---

## 7.2 Sequence Diagram: Store Manager Updates Fulfillment Status

This diagram illustrates how a Store Manager updates an order's fulfillment status while payment status remains controlled exclusively by the Payment Officer.

```mermaid
sequenceDiagram
    autonumber

    actor M as Store Manager
    participant UI as Razor View
    participant OC as OrdersController
    participant OS as OrderService
    participant DB as EF Core / Database

    M->>UI: Open Manager Orders
    UI->>OC: GET /Manager/Orders

    OC->>OS: GetOrdersAsync()
    OS->>DB: Load orders
    DB-->>OS: Order records
    OS-->>OC: Orders
    OC-->>UI: Render order list

    M->>UI: Select new FulfillmentStatus
    UI->>OC: POST /Manager/Orders/UpdateStatus

    OC->>OS: UpdateFulfillmentStatusAsync(orderId, newStatus)

    OS->>DB: Load current order status
    DB-->>OS: Current FulfillmentStatus

    alt Invalid status transition
        OS-->>OC: InvalidStatusTransitionException
        OC-->>UI: Display "Invalid status transition."
        UI-->>M: Show validation error
    else Valid transition
        OS->>DB: Update FulfillmentStatus
        OS->>DB: Update UpdatedAt
        DB-->>OS: Update successful
        OS-->>OC: Success
        OC-->>UI: Redirect to Orders
        UI-->>M: Display updated status
    end
```

### Fulfillment Status Rules

| Current Status | Allowed Next Status |
| :--- | :--- |
| **Pending** | Processing, Cancelled |
| **Processing** | Shipped, Cancelled |
| **Shipped** | Delivered |
| **Delivered** | None |
| **Cancelled** | None |

> **Important:** Store Managers can update `FulfillmentStatus` only. `PaymentStatus` is managed exclusively by the Payment Officer.

---

## 7.3 Sequence Diagram: Payment Officer Processes Payment

This diagram illustrates the payment processing workflow performed by the Payment Officer.

```mermaid
sequenceDiagram
    autonumber

    actor P as Payment Officer
    participant UI as Razor View
    participant PC as PaymentController
    participant PS as PaymentService
    participant DB as EF Core / Database

    P->>UI: Open Payment Dashboard
    UI->>PC: GET /Payment/Dashboard

    PC->>PS: GetPendingPaymentsAsync()
    PS->>DB: Load orders and payment records
    DB-->>PS: Pending payment records
    PS-->>PC: Payment records
    PC-->>UI: Render payment dashboard

    P->>UI: Select order
    UI->>PC: GET /Payment/OrderDetail/{id}

    PC->>PS: GetPaymentDetailsAsync(orderId)
    PS->>DB: Load order and payment data
    DB-->>PS: Payment details
    PS-->>PC: Payment details
    PC-->>UI: Render payment details

    alt Mark as Paid
        P->>UI: Click Mark Paid
        UI->>PC: POST /Payment/MarkPaid
        PC->>PS: MarkPaidAsync(orderId)

        PS->>DB: Update PaymentStatus = Paid
        PS->>DB: Insert PaymentLog
        DB-->>PS: Payment updated
        PS-->>PC: Success
        PC-->>UI: Display success message

    else Reject Payment
        P->>UI: Click Reject
        UI-->>P: Request rejection reason
        P->>UI: Submit reason
        UI->>PC: POST /Payment/MarkRejected

        PC->>PS: MarkRejectedAsync(orderId, reason)

        alt Empty rejection reason
            PS-->>PC: PaymentRejectionReasonRequiredException
            PC-->>UI: Display validation error
        else Valid reason
            PS->>DB: Update PaymentStatus = Rejected
            PS->>DB: Insert PaymentLog
            DB-->>PS: Payment updated
            PS-->>PC: Success
            PC-->>UI: Display success message
        end
    end
```

### Payment Processing Rules

- Only users with the `PaymentOfficer` role can process payments.
- `PaymentStatus` can be changed by the Payment Officer only.
- A rejected payment requires a non-empty rejection reason.
- Every payment action creates a `PaymentLog`.
- `PaymentLog` is append-only and cannot be updated or deleted.

---

## 7.4 Activity Diagram: Order Processing & Checkout

This activity diagram describes the complete customer checkout workflow.

```mermaid
flowchart TD

    Start([Start Checkout]) --> A[Load Active Cart]

    A --> B{Cart Empty?}

    B -->|Yes| C[Display Empty Cart Error]
    C --> End1([End])

    B -->|No| D[Validate Product Availability]

    D --> E{Sufficient Stock?}

    E -->|No| F[Display Insufficient Stock Error]
    F --> G[Return to Cart]
    G --> End2([End])

    E -->|Yes| H[Start EF Core Transaction]

    H --> I[Create Order]

    I --> J[Create OrderItems]

    J --> K[Store UnitPrice Snapshot]

    K --> L[Store ProductNameSnapshot]

    L --> M[Decrement Stock]

    M --> N[Clear Cart Items]

    N --> O{Transaction Successful?}

    O -->|No| P[Rollback Transaction]
    P --> Q[Display Checkout Error]
    Q --> End3([End])

    O -->|Yes| R[Commit Transaction]

    R --> S[Display Order Confirmation]

    S --> End4([End])
```

### Checkout Rules

| Rule | Description |
| :--- | :--- |
| **Atomic Transaction** | Order creation, stock deduction, and cart clearance are committed as one transaction. |
| **Stock Validation** | Stock is validated before and during order creation. |
| **Price Snapshot** | Current product price is stored in `OrderItem.UnitPrice`. |
| **Product Snapshot** | Product name is stored in `OrderItem.ProductNameSnapshot`. |
| **Cart Clearance** | Cart items are removed after successful order creation. |
| **Rollback** | Any failure causes the transaction to roll back. |

---

## 7.5 Activity Diagram: Product Management

This activity diagram describes the Store Manager workflow for creating and maintaining furniture products.

```mermaid
flowchart TD

    Start([Open Product Management]) --> A[Display Product Catalog]

    A --> B{Select Action?}

    B -->|Create| C[Open Create Product Form]
    B -->|Edit| D[Open Edit Product Form]
    B -->|Update Stock| E[Open Inventory]
    B -->|Deactivate| F[Select Product]

    C --> G[Validate Product Data]

    G --> H{Valid Data?}

    H -->|No| I[Display Validation Errors]
    I --> C

    H -->|Yes| J{SKU Already Exists?}

    J -->|Yes| K[Display Duplicate SKU Error]
    K --> C

    J -->|No| L[Create Product]

    L --> M[Upload Product Images]
    M --> N[Save Product]
    N --> O[Return to Product Catalog]

    D --> P[Update Product Fields]
    P --> Q[Validate Changes]
    Q --> R[Save Product Changes]
    R --> O

    E --> S[Enter New Stock Quantity]
    S --> T{Stock >= 0?}

    T -->|No| U[Display Stock Validation Error]
    U --> E

    T -->|Yes| V[Update Stock]
    V --> O

    F --> W[Set IsActive = false]
    W --> O

    O --> End([End])
```

### Product Management Rules

- Only the Store Manager can manage products and inventory.
- SKU values must be unique.
- Product price must be greater than zero.
- Stock quantity cannot be negative.
- A maximum of 8 product images can be uploaded.
- Accepted image formats are JPEG, PNG, and WebP.
- Each image must be under 5 MB.
- Uploaded filenames are generated by the server using GUIDs.
- Products are soft-deleted by setting `IsActive = false`.
- Historical orders and order items remain intact after product deactivation.

---

## 7.6 State Machine Diagram: Order Fulfillment Lifecycle

The following state machine represents the valid fulfillment lifecycle of an order.

```mermaid
stateDiagram-v2

    [*] --> Pending

    Pending --> Processing : Manager starts processing
    Pending --> Cancelled : Cancellation

    Processing --> Shipped : Order dispatched
    Processing --> Cancelled : Cancellation

    Shipped --> Delivered : Customer receives order

    Delivered --> [*]
    Cancelled --> [*]
```

### State Descriptions & Rules

| State | Description | Allowed Transitions |
| :--- | :--- | :--- |
| **Pending** | Order has been created and is awaiting processing. | Processing, Cancelled |
| **Processing** | Store Manager is preparing the order for shipment. | Shipped, Cancelled |
| **Shipped** | Order has been dispatched for delivery. | Delivered |
| **Delivered** | Customer has received the order. | None |
| **Cancelled** | Order has been cancelled and cannot continue through fulfillment. | None |

> Payment status is intentionally separated from fulfillment status. Payment processing is handled by the Payment Officer.

---

## 7.7 Activity Diagram: Product Review Submission

This activity diagram describes how a customer submits a verified product review.

```mermaid
flowchart TD

    Start([Open Product Details]) --> A{Customer Logged In?}

    A -->|No| B[Redirect to Login]
    B --> End1([End])

    A -->|Yes| C[Check Delivered Orders]

    C --> D{Delivered Order Contains Product?}

    D -->|No| E[Hide Review Form]
    E --> End2([End])

    D -->|Yes| F{Already Reviewed?}

    F -->|Yes| G[Display Existing Review / Error]
    G --> End3([End])

    F -->|No| H[Display Review Form]

    H --> I[Enter Rating and Comment]

    I --> J{Valid Review?}

    J -->|No| K[Display Validation Errors]
    K --> H

    J -->|Yes| L[Create ProductReview]

    L --> M[Set IsVisible = true]

    M --> N[Save Review]

    N --> O[Display Success Message]

    O --> End4([End])
```

### Review Rules

- Only authenticated customers can submit reviews.
- A review is allowed only when the customer has a delivered order containing the product.
- Each customer can submit only one review per product.
- Review rating must be within the configured 1–5 range.
- Review records are associated with both the customer and product.
- Review moderation is handled by the Administrator.

---

## 7.8 Activity Diagram: Administrator User Management

This activity diagram illustrates how an Administrator manages customer and staff accounts.

```mermaid
flowchart TD

    Start([Open Admin User Management]) --> A[Load Users]

    A --> B{Select Action?}

    B -->|Activate / Deactivate| C[Select User]

    B -->|Assign Role| D[Select User]

    B -->|Revoke Role| E[Select User]

    C --> F{Target is Current Admin?}

    F -->|Yes| G[Reject Action]
    G --> End1([End])

    F -->|No| H[Update IsActive]

    H --> I[Create AuditLog Entry]
    I --> End2([End])

    D --> J{Target is Current Admin?}

    J -->|Yes| K[Reject Permission Change]
    K --> End3([End])

    J -->|No| L[Assign Selected Role]
    L --> M[Create AuditLog Entry]
    M --> End4([End])

    E --> N{Target is Current Admin?}

    N -->|Yes| O[Reject Permission Change]
    O --> End5([End])

    N -->|No| P[Revoke Selected Role]
    P --> Q[Create AuditLog Entry]
    Q --> End6([End])
```

### Administrator Rules

- All Admin pages are protected by `[Authorize(Roles = "Admin")]`.
- Administrators cannot deactivate their own account.
- Administrators cannot modify their own permissions.
- User deactivation is soft deactivation; user data is preserved.
- Role changes are recorded in `AuditLog`.
- Account activation/deactivation is recorded in `AuditLog`.
- Audit logs are append-only.
- No Admin action can update or delete existing audit log records.

---

## 7.9 Behavioral Model Summary

| Behavioral Model | Main Purpose |
| :--- | :--- |
| **Checkout Sequence** | Shows the interaction between customer, MVC controllers, services, EF Core, and database during order placement. |
| **Fulfillment Sequence** | Shows how Store Managers update order fulfillment status. |
| **Payment Sequence** | Shows how Payment Officers process and log payment actions. |
| **Checkout Activity** | Represents the complete checkout decision flow and transaction handling. |
| **Product Management Activity** | Represents product creation, editing, inventory updates, and soft deletion. |
| **Order State Machine** | Defines valid fulfillment status transitions. |
| **Review Activity** | Represents verified purchase review submission and validation. |
| **Admin Activity** | Represents user activation, deactivation, role assignment, and role revocation. |

---

[← Previous: Domain Model](./06-domain-model.md) | [Back to Index](./00-index.md) | [Next: Database Design →](./08-database-design.md)
