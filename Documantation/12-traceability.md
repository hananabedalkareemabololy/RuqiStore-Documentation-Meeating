# 12. Requirements Traceability Matrix (RTM)

## 📌 Overview

This document serves as the **Requirements Traceability Matrix (RTM)** for the **Ruqi Store** application. It maps each defined system requirement (Functional and Non-Functional) to its corresponding domain entities, database tables, service operations, and UI pages, ensuring development coverage, consistency, and clear traceability across the system.

---

## 🗺️ Functional Requirements Mapping (FR)

This matrix maps user-facing features to their corresponding database and service-level implementations:

| Req ID | Requirement Description | Domain Entities | Database Tables | Service Method / Application Component | Target UI Page |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **FR-01** | User Authentication & Role Management | `ApplicationUser`, `IdentityRole` | ASP.NET Core Identity Tables | ASP.NET Core Identity, `UserManager`, `SignInManager`, Role Authorization | Login / Register Pages |
| **FR-02** | Product Catalog Browsing | `Product`, `Category`, `ProductImage` | `Products`, `Categories`, `ProductImages` | `IProductService.GetActiveProductsAsync()` | Home / Products / Collections Pages |
| **FR-03** | Product Search & Filtering | `Product`, `Category` | `Products`, `Categories` | `IProductService.SearchAsync()` / Filtering Logic | Products Page |
| **FR-04** | Manage Shopping Cart | `Cart`, `CartItem` | `Carts`, `CartItems` | `ICartService.AddToCartAsync()` / `UpdateCartItemQuantityAsync()` / `RemoveFromCartAsync()` | Shopping Cart Page |
| **FR-05** | Saved Customer Addresses | `Address` | `Addresses` | `IAddressService.AddAsync()` / `UpdateAsync()` / `DeleteAsync()` | Address Book / Checkout |
| **FR-06** | Checkout & Order Placement | `Order`, `OrderItem` | `Orders`, `OrderItems` | `IOrderService.PlaceOrderAsync()` | Checkout Page |
| **FR-07** | Inventory & Stock Management | `Product` | `Products` | `IInventoryService.ValidateStockAsync()` / Inventory Update Logic | Store Manager Dashboard |
| **FR-08** | Order Tracking & Fulfillment | `Order`, `OrderItem` | `Orders`, `OrderItems` | `IOrderService.GetOrderStatusAsync()` / Fulfillment Management | My Orders / Manager Dashboard |
| **FR-09** | Payment Management | `Payment`, `PaymentLog` | `Payments`, `PaymentLogs` | `IPaymentService.ProcessPaymentAsync()` | Payment Officer Dashboard |
| **FR-10** | Product Reviews | `ProductReview` | `ProductReviews` | `IReviewService.AddReviewAsync()` / Review Moderation | Product Details / Admin Reviews |
| **FR-11** | Store Manager Dashboard | `Product`, `Order`, `Category` | `Products`, `Orders`, `Categories` | Manager Services | Store Manager Dashboard |
| **FR-12** | Administrator Management | `ApplicationUser`, `AuditLog` | Identity Tables, `AuditLogs` | User Management / Role Management / Audit Services | Admin Dashboard |
| **FR-13** | Audit Logging | `AuditLog` | `AuditLogs` | Audit Logging Middleware / Service | Admin Audit Logs |
| **FR-14** | Product Image Uploads | `ProductImage` | `ProductImages` | File Upload Service | Product Management |
| **FR-15** | Bilingual User Interface | `Product`, Localization Resources | Product Language Fields / Localization Resources | `LanguageController` / Culture Cookie | Arabic RTL / English LTR Views |

---

## ⚙️ Non-Functional Requirements Mapping (NFR)

This matrix maps system constraints, performance, security, usability, and maintainability requirements to their architectural implementations:

| Req ID | NFR Category | Technical Implementation & Enforcement | Architectural File Reference |
| :--- | :--- | :--- | :--- |
| **NFR-U1** | **Responsive Design** | Responsive layouts using HTML5, CSS3, Flexbox/Grid, and mobile-first design principles across Razor Views. | `11-ui-ux-design.md` |
| **NFR-U2** | **Accessibility** | Semantic HTML, keyboard navigation, accessible labels, readable contrast, and appropriate ARIA attributes. | `11-ui-ux-design.md` |
| **NFR-U3** | **Localization (RTL/LTR)** | Arabic and English interfaces using `.AspNetCore.Culture` cookie, `LanguageController`, and RTL/LTR Razor layouts. | `11-ui-ux-design.md` |
| **NFR-U4** | **User Feedback & Loading States** | Toast notifications, validation messages, disabled transaction buttons, and loading indicators provide clear feedback during operations. | `11-ui-ux-design.md`, `07-uml-behavioral.md` |
| **NFR-S1** | **Authentication Security** | ASP.NET Core Identity with secure authentication cookies, password hashing, account security policies, and role-based authorization. | `03-authentication-role-management.md`, `09-architecture.md` |
| **NFR-S2** | **CSRF Protection** | ASP.NET Core MVC anti-forgery tokens protect state-changing form submissions. | `03-authentication-role-management.md`, `09-architecture.md` |
| **NFR-S3** | **Authorization & Access Control** | `[Authorize]` and `[Authorize(Roles = "...")]` restrict protected controllers and actions according to system roles. | `03-authentication-role-management.md`, `10-detailed-design.md` |
| **NFR-S4** | **Data Protection** | EF Core parameterized queries, Razor automatic encoding, HTTPS enforcement, and secure authentication cookies protect application data. | `09-architecture.md`, `03-authentication-role-management.md` |
| **NFR-S5** | **Customer Data Isolation** | Ownership checks ensure customers can access only their own carts, addresses, orders, payment-related information, and reviews. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-C1** | **Concurrency Control** | Atomic Entity Framework Core database transactions validate stock, deduct inventory, create orders, and clear carts as one transaction. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-C2** | **Inventory Consistency** | Stock validation and database constraints prevent inventory quantities from becoming negative during checkout. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-D1** | **Price Consistency** | `OrderItem.unit_price` stores an immutable price snapshot captured during checkout. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-D2** | **Historical Data Preservation** | Product soft deletion and order-item snapshots preserve historical transaction information. | `08-database-design.md` |
| **NFR-D3** | **Referential Integrity** | Entity Framework Core foreign keys and configured relationships maintain valid relationships between database entities. | `08-database-design.md` |
| **NFR-A1** | **Auditability** | Administrative and configured system actions are recorded in the append-only `AuditLogs` table. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-A2** | **Payment Accountability** | Payment decisions are recorded in the append-only `PaymentLogs` table and are associated with the responsible Payment Officer. | `08-database-design.md` |
| **NFR-P1** | **Performance** | Server-rendered Razor Views, optimized EF Core queries, indexed relational data, and efficient service/repository operations support normal store traffic. | `09-architecture.md`, `10-detailed-design.md` |
| **NFR-M1** | **Maintainability** | Monolithic ASP.NET Core MVC separates Controllers, Services, Repositories, Models, and Data access responsibilities. | `09-architecture.md`, `10-detailed-design.md` |
| **NFR-M2** | **Database Portability** | Entity Framework Core Code-First migrations support SQLite during development and SQL Server in production. | `08-database-design.md`, `09-architecture.md` |
| **NFR-R1** | **Reliability** | Atomic checkout transactions roll back failed operations to prevent incomplete orders, inconsistent inventory, or partially processed transactions. | `08-database-design.md`, `10-detailed-design.md` |

---

## 📑 Traceability Rules & Verification Checklist

* **Requirement Coverage:** Every implemented feature should map to at least one **Req ID** in this matrix.

* **Database Traceability:** Each major domain entity must have a corresponding database representation or ASP.NET Core Identity representation.

* **Service Traceability:** Business operations should be implemented through the appropriate service layer rather than placing business rules directly inside Razor Views.

* **Authorization Verification:** Every protected operation must be checked against the appropriate authenticated user and system role.

* **Database Integrity:** Foreign keys and configured Entity Framework Core relationships must be consistent with the domain relationships defined in `06-domain-model.md` and `08-database-design.md`.

* **Checkout Verification:** Stock validation, inventory deduction, order creation, price snapshots, and cart cleanup must execute within the defined atomic transaction workflow.

* **Payment Verification:** Payment status changes must be restricted to the **Payment Officer** role and recorded in `PaymentLogs`.

* **Audit Verification:** Administrative and other configured auditable operations must generate append-only entries in `AuditLogs`.

* **UX Continuity:** Loading states, validation messages, and user feedback mechanisms must correspond to the asynchronous or transactional operations documented in `07-uml-behavioral.md`.

* **Documentation Consistency:** Architecture, database, detailed design, and UI/UX documentation must describe the same **Monolithic ASP.NET Core MVC** architecture and must not introduce technologies that are outside the approved project scope.

---

## 🔗 Cross-Document Traceability

The requirements in this matrix are implemented and documented across the following project documents:

| Document | Traceability Scope |
| :--- | :--- |
| `03-authentication-role-management.md` | Authentication, Identity, roles, authorization, and security |
| `06-domain-model.md` | Domain entities and relationships |
| `07-uml-behavioral.md` | System workflows and behavioral interactions |
| `08-database-design.md` | Database entities, constraints, integrity, and transactions |
| `09-architecture.md` | System architecture, technology stack, and component structure |
| `10-detailed-design.md` | Services, business rules, validation, and application workflows |
| `11-ui-ux-design.md` | User interface, responsive design, localization, and accessibility |
| `12-traceability.md` | Requirements-to-implementation traceability |

---

[← Previous: UI/UX Design](./11-ui-ux-design.md) | [Back to Index](./00-index.md) | [Next: Appendices →](./13-appendices.md)
