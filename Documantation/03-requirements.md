# 03. System Requirements Specification

## 3.1 Functional Requirements

### User Identity & Access Management

| ID         | Requirement                                                                                                                                                      | Priority |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-001** | The system shall allow customers to register an account and log in securely using their email and password through ASP.NET Core Identity.                        |   Must   |
| **FR-002** | The system shall enforce Role-Based Access Control (RBAC) with four defined roles: Customer, Store Manager, Payment Officer, and Administrator.                  |   Must   |
| **FR-003** | The system shall allow authenticated users to manage their profile information and saved delivery addresses.                                                     |   Must   |
| **FR-004** | The system shall assign the Customer role to newly registered customers and allow the Administrator to assign or revoke Store Manager and Payment Officer roles. |   Must   |
| **FR-005** | The system shall maintain authenticated sessions using encrypted, HttpOnly, Secure, SameSite=Strict cookies managed by ASP.NET Core Identity.                    |   Must   |
| **FR-006** | The system shall lock a user account after 5 consecutive failed login attempts for a period of 15 minutes.                                                       |   Must   |

### Dynamic Furniture Product Catalog

| ID         | Requirement                                                                                                                                                 | Priority |
| :--------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-007** | The system shall display an active, categorized furniture product catalog accessible to guests and authenticated users.                                     |   Must   |
| **FR-008** | The system shall allow users to search products by keyword and filter products by category, price range, material, and stock availability.                  |   Must   |
| **FR-009** | The system shall display detailed product information including name, SKU, price, material, dimensions, weight, stock status, images, and verified reviews. |   Must   |
| **FR-010** | The system shall allow the Store Manager to create and update furniture products and manage their active status.                                            |   Must   |
| **FR-011** | The system shall allow the Store Manager to create, rename, reorder, activate, and deactivate furniture categories.                                         |   Must   |
| **FR-012** | The system shall enforce unique SKUs and reject products with a price less than or equal to zero.                                                           |   Must   |
| **FR-013** | The system shall support up to 8 images per product and validate uploaded images by extension, magic bytes, and a maximum size of 5 MB per image.           |   Must   |

### Shopping Cart & Checkout

| ID         | Requirement                                                                                                                                     | Priority |
| :--------- | :---------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-014** | The system shall provide a persistent database-backed shopping cart with one cart per customer.                                                 |   Must   |
| **FR-015** | The system shall allow customers to add products, update quantities, and remove products from their cart.                                       |   Must   |
| **FR-016** | The system shall prevent cart quantities from exceeding the product's available stock.                                                          |   Must   |
| **FR-017** | The system shall allow customers to select a saved delivery address or enter a new address during checkout.                                     |   Must   |
| **FR-018** | The system shall support Cash on Delivery and Bank Transfer as the current payment methods.                                                     |   Must   |
| **FR-019** | The system shall validate stock availability and create the order within a single atomic EF Core transaction.                                   |   Must   |
| **FR-020** | The system shall capture the product name and unit price as immutable snapshots in `OrderItems` when an order is created.                       |   Must   |
| **FR-021** | The system shall deduct ordered quantities from product stock during successful order placement and preserve the cart if the transaction fails. |   Must   |
| **FR-022** | The system shall clear the customer's cart after a successful order transaction.                                                                |   Must   |

### Order Management & Tracking

| ID         | Requirement                                                                                                                                  | Priority |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-023** | The system shall allow customers to view their order history and order details.                                                              |   Must   |
| **FR-024** | The system shall track fulfillment through the predefined status sequence: `Pending` → `Processing` → `Shipped` → `Delivered` → `Cancelled`. |   Must   |
| **FR-025** | The system shall allow the Store Manager to update the fulfillment status of customer orders according to valid status transitions.          |   Must   |
| **FR-026** | The system shall maintain `FulfillmentStatus` and `PaymentStatus` as independent order status values managed by their respective roles.      |   Must   |
| **FR-027** | The system shall prevent customers from accessing orders belonging to other customers.                                                       |   Must   |

### Payment Management

| ID         | Requirement                                                                                                                                | Priority |
| :--------- | :----------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-028** | The system shall allow the Payment Officer to review customer orders and payment information.                                              |   Must   |
| **FR-029** | The system shall allow the Payment Officer to mark an order as `Paid` or `Rejected`.                                                       |   Must   |
| **FR-030** | The system shall require a non-empty rejection reason when a payment is rejected.                                                          |   Must   |
| **FR-031** | The system shall record every payment status decision in the append-only `PaymentLogs` table.                                              |   Must   |
| **FR-032** | The system shall provide the Payment Officer with access to payment history while preventing unauthorized modification of payment records. |   Must   |

### Product Reviews

| ID         | Requirement                                                                                                                       | Priority |
| :--------- | :-------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-033** | The system shall allow customers to submit product reviews only when they have a delivered order containing the reviewed product. |   Must   |
| **FR-034** | The system shall allow a customer to submit only one review per product.                                                          |   Must   |
| **FR-035** | The system shall support ratings from 1 to 5 stars with an optional review title and comment.                                     |   Must   |
| **FR-036** | The system shall mark qualifying reviews as verified purchases and recalculate the product's average rating after submission.     |   Must   |
| **FR-037** | The system shall allow the Administrator to moderate product reviews by hiding inappropriate reviews.                             |   Must   |

### Store Manager Management

| ID         | Requirement                                                                                                                      | Priority |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-038** | The system shall provide the Store Manager with a dashboard containing product, inventory, pending order, and sales information. |   Must   |
| **FR-039** | The system shall allow the Store Manager to update product stock while preventing stock quantities from becoming negative.       |   Must   |
| **FR-040** | The system shall provide low-stock indicators for products below the configured stock threshold.                                 |  Should  |
| **FR-041** | The system shall allow the Store Manager to view and filter orders by fulfillment status.                                        |   Must   |
| **FR-042** | The system shall provide sales analytics including revenue by period, units sold by category, top products, and inventory value. |  Should  |

### Administrator Management & Audit

| ID         | Requirement                                                                                                                                                    | Priority |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| **FR-043** | The system shall provide the Administrator with a centralized management panel for system-wide oversight.                                                      |   Must   |
| **FR-044** | The system shall allow the Administrator to view, search, and filter registered users.                                                                         |   Must   |
| **FR-045** | The system shall allow the Administrator to activate or deactivate user accounts while preserving their data.                                                  |   Must   |
| **FR-046** | The system shall prevent an Administrator from deactivating their own account.                                                                                 |   Must   |
| **FR-047** | The system shall allow the Administrator to assign or revoke Store Manager and Payment Officer roles.                                                          |   Must   |
| **FR-048** | The system shall prevent an Administrator from modifying their own permissions.                                                                                |   Must   |
| **FR-049** | The system shall allow the Administrator to view platform-wide orders in read-only mode.                                                                       |   Must   |
| **FR-050** | The system shall allow the Administrator to generate CSV reports for revenue, orders, top products, top customers, and inventory.                              |  Should  |
| **FR-051** | The system shall record administrative actions such as user activation/deactivation, role changes, and review moderation in the append-only `AuditLogs` table. |   Must   |
| **FR-052** | The system shall provide the Administrator with a read-only audit log without update or delete operations.                                                     |   Must   |

### Localization & User Interface

| ID         | Requirement                                                                                         | Priority |
| :--------- | :-------------------------------------------------------------------------------------------------- | :------: |
| **FR-053** | The system shall support English and Arabic interfaces.                                             |   Must   |
| **FR-054** | The system shall render English content using an LTR layout and Arabic content using an RTL layout. |   Must   |
| **FR-055** | The system shall store the selected language preference using the ASP.NET Core culture cookie.      |   Must   |
| **FR-056** | The system shall support responsive layouts for mobile, tablet, and desktop screen sizes.           |   Must   |

### Future Features

| ID         | Requirement                                                                                                | Priority |
| :--------- | :--------------------------------------------------------------------------------------------------------- | :------: |
| **FR-057** | The system may support a Wishlist feature in a future version for saving favorite furniture products.      |   Could  |
| **FR-058** | The system may support Redis caching in a future version to improve performance at higher traffic volumes. |   Could  |
| **FR-059** | The system may support an online payment gateway such as Stripe in a future version.                       |   Could  |
| **FR-060** | The system may support AR furniture visualization and native mobile applications in future releases.       |   Could  |

---

## 3.2 Non-Functional Requirements

### Performance & Scalability

| ID          | Requirement                                                                                                                                     | Metric                         |
| :---------- | :---------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------- |
| **NFR-001** | The system shall process controller actions within the defined performance target under normal operating conditions.                            | 95th percentile < 500 ms       |
| **NFR-002** | The system shall render fully styled application pages within the defined performance target under normal network conditions.                   | Page render < 2 seconds        |
| **NFR-003** | The system shall support up to 200 concurrent users without significant service degradation.                                                    | Target: 200 concurrent users   |
| **NFR-004** | The architecture shall use a Service Layer and Repository Pattern to maintain clean separation of concerns and support future scalability.      | Architectural compliance       |
| **NFR-005** | The system shall use SQLite during development and support SQL Server as the production database without changing the application architecture. | EF Core provider configuration |

### Security & Privacy

| ID          | Requirement                                                                                                                          | Metric                                |
| :---------- | :----------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------ |
| **NFR-006** | The system shall prevent SQL injection by using parameterized queries generated by Entity Framework Core.                            | No unsafe query concatenation         |
| **NFR-007** | The system shall enforce HTTPS for application communication.                                                                        | `UseHttpsRedirection()`               |
| **NFR-008** | The system shall protect all state-changing POST requests against CSRF attacks using ASP.NET Core anti-forgery tokens.               | `[ValidateAntiForgeryToken]`          |
| **NFR-009** | The system shall use ASP.NET Core Identity with PBKDF2 password hashing and shall never store plaintext passwords.                   | Identity security configuration       |
| **NFR-010** | The system shall protect authentication cookies using HttpOnly, Secure, and SameSite=Strict settings.                                | Secure cookie configuration           |
| **NFR-011** | The system shall enforce role-based authorization for protected controllers and actions.                                             | `[Authorize(Roles="...")]`            |
| **NFR-012** | The system shall validate uploaded product images using file extension, magic bytes, file size, and server-generated GUID filenames. | Maximum 5 MB; JPEG/PNG/WebP           |
| **NFR-013** | The system shall prevent unauthorized users from accessing other customers' carts, orders, addresses, and reviews.                   | Service-layer ownership validation    |
| **NFR-014** | The system shall apply configurable password complexity and account lockout policies through ASP.NET Core Identity.                  | 5 failed attempts / 15-minute lockout |

### Usability & Accessibility

| ID          | Requirement                                                                                                                                                   | Metric                       |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------- |
| **NFR-015** | The system shall support responsive interfaces across mobile, tablet, and desktop screen sizes.                                                               | 320px–2560px target range    |
| **NFR-016** | The system shall support Arabic RTL layouts using logical CSS properties instead of fixed directional properties.                                             | `dir="rtl"` support          |
| **NFR-017** | The system shall provide clearly visible keyboard focus states and keyboard navigation for interactive elements.                                              | WCAG 2.1 Level AA target     |
| **NFR-018** | The system shall provide sufficient text contrast and accessible labels for interactive controls and visual components.                                       | WCAG 2.1 Level AA target     |
| **NFR-019** | The system shall display validation errors inline and shall not expose raw system exceptions to users.                                                        | User-friendly error handling |
| **NFR-020** | The system shall prevent duplicate form submissions by disabling submit buttons after the first valid submission and displaying an appropriate loading state. | UI interaction rule          |

### Reliability & Data Integrity

| ID          | Requirement                                                                                                                                       | Metric                                    |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------- |
| **NFR-021** | The system shall execute order creation, stock deduction, and cart clearing within an atomic EF Core transaction.                                 | Commit or complete rollback               |
| **NFR-022** | The system shall preserve historical order information even when a product is deactivated.                                                        | Soft-delete / restricted product deletion |
| **NFR-023** | The system shall preserve immutable product price and name snapshots in historical order items.                                                   | `UnitPrice` and `ProductNameSnapshot`     |
| **NFR-024** | The system shall enforce database-level uniqueness for one cart per customer, one cart item per product, and one review per customer per product. | EF Core unique constraints                |
| **NFR-025** | The system shall maintain PaymentLogs and AuditLogs as append-only records without update or delete operations.                                   | Repository-level enforcement              |
| **NFR-026** | The system shall maintain data consistency through EF Core Code-First migrations and database constraints.                                        | Version-controlled schema                 |
| **NFR-027** | The system shall use a global exception middleware to handle unexpected application errors without exposing internal implementation details.      | `GlobalExceptionMiddleware`               |

### Maintainability & Architecture

| ID          | Requirement                                                                                                                                                    | Metric                         |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------- |
| **NFR-028** | The system shall maintain separation between Controllers, Services, Repositories, Models, and Data Access components.                                          | Monolithic 3-Tier architecture |
| **NFR-029** | Controllers shall not directly access Entity Framework Core or the database; database operations shall be performed through the Service and Repository layers. | Architectural rule             |
| **NFR-030** | The system shall use Dependency Injection for services, repositories, and database components.                                                                 | ASP.NET Core DI                |
| **NFR-031** | The system shall use Razor Views with strongly typed ViewModels for server-rendered pages.                                                                     | `.cshtml` / ViewModels         |
| **NFR-032** | The system shall use version-controlled EF Core migrations for database schema evolution.                                                                      | Code-First migrations          |

---

## 3.3 Prioritization Summary (MoSCoW)

| Priority        | Count | Examples                                                                                                                                                                                    |
| :-------------- | :---: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Must Have**   |   53  | Authentication, RBAC, product catalog, cart, atomic checkout, order tracking, payment management, verified reviews, inventory, administration, audit logs, bilingual UI, security controls. |
| **Should Have** |   4   | Low-stock indicators, sales analytics, CSV reports, selected performance and usability enhancements.                                                                                        |
| **Could Have**  |   4   | Wishlist, Redis caching, online payment gateway, AR visualization and native mobile applications as future enhancements.                                                                    |
| **Won't Have**  |   1   | Showroom / appointment booking is excluded because Ruqi Store is an online-only furniture e-commerce system.                                                                                |

---

[← Previous: Stakeholder Analysis](./02-stakeholder-analysis.md) | [Back to Index](./00-index.md) | [Next: Use Case Model & Descriptions →](./04-use-case-model.md)
