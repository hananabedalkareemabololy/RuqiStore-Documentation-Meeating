# 12 — Requirements Traceability Matrix (RTM)

## 📌 Overview

This document serves as the **Requirements Traceability Matrix (RTM)** for the **Ruqi Store** application.

It maps the defined system requirements to their corresponding domain entities, database representations, service/application components, and documented UI pages.

The matrix is based on the project's documented architecture, domain model, behavioral workflows, database design, detailed design, and current UI/UX scope.

The project follows a **Monolithic ASP.NET Core MVC architecture** using Razor Views, ASP.NET Core Identity, application services, Entity Framework Core, and a relational database.

---

## 🗺️ Functional Requirements Mapping (FR)

This matrix maps the functional requirements to the documented implementation components and current user interface.

| Req ID | Requirement Description | Domain Entities | Database Tables / Representation | Service Method / Application Component | Target UI / Interface |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **FR-01** | User Authentication & Role Management | `ApplicationUser`, `IdentityRole` | ASP.NET Core Identity Tables | ASP.NET Core Identity, `UserManager`, `SignInManager`, Role Authorization | Login / Register Pages |
| **FR-02** | Product Catalog Browsing | `Product`, `Category`, `ProductImage` | `Products`, `Categories`, `ProductImages` | `IProductService.GetActiveProductsAsync()` | Home / Products / Collections |
| **FR-03** | Product Search & Filtering | `Product`, `Category` | `Products`, `Categories` | `IProductService.SearchAsync()` / Filtering Logic | Products Page |
| **FR-04** | Shopping Cart Management | `Cart`, `CartItem` | `Carts`, `CartItems` | `ICartService.AddToCartAsync()` / `UpdateCartItemQuantityAsync()` / `RemoveFromCartAsync()` | Shopping Cart |
| **FR-05** | Customer Address Management | `Address` | `Addresses` | `IAddressService.AddAsync()` / `UpdateAsync()` / `DeleteAsync()` | Address Management / Checkout |
| **FR-06** | Checkout & Order Placement | `Order`, `OrderItem` | `Orders`, `OrderItems` | `IOrderService.PlaceOrderAsync()` | Checkout |
| **FR-07** | Inventory & Stock Management | `Product` | `Products` | `IInventoryService.ValidateStockAsync()` / Inventory Update Logic | Store Manager Functions |
| **FR-08** | Order Tracking & Fulfillment | `Order`, `OrderItem` | `Orders`, `OrderItems` | `IOrderService.GetOrderStatusAsync()` / Fulfillment Management | Customer Order Functions / Manager Functions |
| **FR-09** | Payment Management | `Payment`, `PaymentLog` | `Payments`, `PaymentLogs` | `IPaymentService.ProcessPaymentAsync()` / Payment Management Logic | Payment Officer Functions |
| **FR-10** | Product Reviews | `ProductReview` | `ProductReviews` | `IReviewService.AddReviewAsync()` / Review Moderation | Product Details / Review Management |
| **FR-11** | Store Manager Management Functions | `Product`, `Order`, `Category` | `Products`, `Orders`, `Categories` | Manager Services / Authorization | Store Manager Functions |
| **FR-12** | Administrator Management | `ApplicationUser`, `AuditLog` | Identity Tables, `AuditLogs` | User Management / Role Management / Audit Services | Administrator Functions |
| **FR-13** | Audit Logging | `AuditLog` | `AuditLogs` | Audit Logging Service / Configured Auditable Operations | Administrator Audit Functions |
| **FR-14** | Product Image Management | `ProductImage` | `ProductImages` | File Upload / Product Image Management Component | Product Management |
| **FR-15** | Bilingual User Interface | Localization Resources, `Product` | Localization Resources / Product Language Data | ASP.NET Core Localization, `LanguageController`, Culture Cookie | English LTR / Arabic RTL UI |

> **Note:** The current UI documentation in `11-ui-ux-design.md` defines the public screens as Home, Product Catalog, Collections, About Us, Login, and Register, together with shared Header, Footer, Language Toggle, and Shopping Cart Access. Administrative, payment, order, address, and management functionality belongs to the application's functional scope but is not represented as a public UI wireframe unless explicitly defined elsewhere in the project documentation.

---

## ⚙️ Non-Functional Requirements Mapping (NFR)

This matrix maps the documented non-functional requirements to their architectural and implementation responsibilities.

| Req ID | NFR Category | Technical Implementation & Enforcement | Architectural File Reference |
| :--- | :--- | :--- | :--- |
| **NFR-U1** | **Responsive Design** | Responsive HTML5/CSS3 layouts using Flexbox/Grid and responsive breakpoints across Razor Views. | `11-ui-ux-design.md` |
| **NFR-U2** | **Accessibility** | Semantic HTML, keyboard navigation, visible focus states, accessible form labels, meaningful alternative text, and readable content. | `11-ui-ux-design.md` |
| **NFR-U3** | **Localization (RTL/LTR)** | English and Arabic cultures using ASP.NET Core Localization, `.AspNetCore.Culture`, language switching, and RTL/LTR layouts. | `11-ui-ux-design.md` |
| **NFR-U4** | **User Feedback & Validation** | Validation messages, authentication feedback, form validation, and clear responses to user actions. | `11-ui-ux-design.md`, `07-uml-behavioral.md` |
| **NFR-S1** | **Authentication Security** | ASP.NET Core Identity, secure authentication cookies, password hashing, account security policies, and role-based authorization. | `03-authentication-role-management.md`, `09-architecture.md` |
| **NFR-S2** | **CSRF Protection** | ASP.NET Core MVC anti-forgery protection for protected state-changing form submissions. | `03-authentication-role-management.md`, `09-architecture.md` |
| **NFR-S3** | **Authorization & Access Control** | `[Authorize]` and role-based authorization restrict protected functionality according to authenticated user roles. | `03-authentication-role-management.md`, `10-detailed-design.md` |
| **NFR-S4** | **Data Protection** | Entity Framework Core parameterized queries, Razor automatic encoding, HTTPS configuration, and secure authentication cookies. | `09-architecture.md`, `03-authentication-role-management.md` |
| **NFR-S5** | **Customer Data Isolation** | Server-side ownership checks ensure customers can access only their own customer-specific resources and operations. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-C1** | **Concurrency Control** | Transactional checkout workflow validates stock, updates inventory, creates orders, and clears cart data as an atomic operation. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-C2** | **Inventory Consistency** | Stock validation and database constraints prevent invalid inventory quantities during order processing. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-D1** | **Price Consistency** | `OrderItem.unit_price` stores the product price snapshot associated with the order item. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-D2** | **Historical Data Preservation** | Product soft deletion and order-item price/product snapshots preserve historical transaction information. | `08-database-design.md` |
| **NFR-D3** | **Referential Integrity** | Entity Framework Core foreign keys and configured relationships maintain valid relationships between related entities. | `08-database-design.md` |
| **NFR-A1** | **Auditability** | Configured administrative and auditable operations are recorded in the append-only `AuditLogs` table. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-A2** | **Payment Accountability** | Payment decisions and related payment actions are recorded through `PaymentLogs` and associated with the responsible Payment Officer. | `08-database-design.md`, `10-detailed-design.md` |
| **NFR-P1** | **Performance** | Server-rendered Razor Views, optimized EF Core operations, relational indexes, and service-layer operations support normal application traffic. | `09-architecture.md`, `10-detailed-design.md` |
| **NFR-M1** | **Maintainability** | The monolithic ASP.NET Core MVC structure separates Controllers, Services, Repositories, Models, and Data responsibilities. | `09-architecture.md`, `10-detailed-design.md` |
| **NFR-M2** | **Database Portability** | Entity Framework Core Code-First migrations support the documented development and production database environments. | `08-database-design.md`, `09-architecture.md` |
| **NFR-R1** | **Reliability** | Transactional application workflows roll back failed operations to avoid incomplete or inconsistent data states. | `08-database-design.md`, `10-detailed-design.md` |

---

## 📑 Traceability Rules & Verification Checklist

The following rules are used to verify consistency between requirements and implementation documentation.

### Requirement Coverage

Every implemented functional requirement should be represented by at least one **Req ID** in this matrix.

### Domain Traceability

Each major domain entity documented in the project should have a corresponding database representation or ASP.NET Core Identity representation.

### Service Traceability

Business operations should be handled through the documented application/service layer rather than placing business rules directly inside Razor Views.

### Authorization Verification

Protected operations must be checked against the authenticated user and the appropriate system role.

Customer-specific operations must also verify ownership against the current authenticated Identity user.

### Database Integrity

Foreign keys, constraints, and Entity Framework Core relationships must remain consistent with the domain relationships documented in:

- `06-domain-model.md`
- `08-database-design.md`

### Checkout Verification

The checkout workflow must maintain consistency between:

1. Stock validation.
2. Inventory deduction.
3. Order creation.
4. Order-item creation.
5. Price snapshot storage.
6. Cart cleanup.

These operations should follow the transactional workflow documented in the system design.

### Payment Verification

Payment status and payment-related decisions must be restricted to the appropriate **Payment Officer** role and recorded through the documented payment logging mechanism.

### Audit Verification

Administrative and configured auditable operations must generate entries in the `AuditLogs` structure according to the documented audit requirements.
### UX Continuity

Validation messages, user feedback, navigation behavior, localization, and responsive behavior must remain consistent with:

- `07-uml-behavioral.md`
- `11-ui-ux-design.md`

### Documentation Consistency

All documentation must describe the same:

**Monolithic ASP.NET Core MVC architecture**

The documentation must not introduce technologies outside the approved project scope, such as:

- React
- Angular
- Vue
- Node.js frontend
- Separate SPA architecture
- Separate REST API frontend

---

## 🔗 Cross-Document Traceability

The requirements in this matrix are implemented and documented across the following project documents:

| Document | Traceability Scope |
| :--- | :--- |
| `03-authentication-role-management.md` | Authentication, Identity, roles, authorization, and security |
| `06-domain-model.md` | Domain entities, attributes, and relationships |
| `07-uml-behavioral.md` | System workflows, use cases, and behavioral interactions |
| `08-database-design.md` | Database entities, relationships, constraints, integrity, and transaction behavior |
| `09-architecture.md` | System architecture, technology stack, layers, and component structure |
| `10-detailed-design.md` | Services, business rules, validation, authorization, and application workflows |
| `11-ui-ux-design.md` | User interface, wireframes, responsive design, localization, and accessibility |
| `12-traceability.md` | Requirements-to-domain, database, service, architecture, and UI traceability |

---

## 📊 Requirements Coverage Summary

The RTM provides traceability across the following major system areas:

| Area | Covered |
| :--- | :---: |
| Authentication & Authorization | ✅ |
| User & Role Management | ✅ |
| Product Catalog | ✅ |
| Product Search | ✅ |
| Shopping Cart | ✅ |
| Customer Addresses | ✅ |
| Checkout & Orders | ✅ |
| Inventory Management | ✅ |
| Order Tracking | ✅ |
| Payment Management | ✅ |
| Product Reviews | ✅ |
| Product Management | ✅ |
| Audit Logging | ✅ |
| Localization | ✅ |
| Responsive UI | ✅ |
| Accessibility | ✅ |
| Security & Data Isolation | ✅ |
| Database Integrity | ✅ |
| Transaction Reliability | ✅ |
| Maintainable MVC Architecture | ✅ |
---

## 📌 UI Scope Clarification

The RTM distinguishes between **functional system requirements** and **currently documented public UI screens**.

The current public UI screens documented in `11-ui-ux-design.md` are:

- Home Page
- Product Catalog Page
- Collections Page
- About Us Page
- Login Page
- Register / Create Account Page

The shared public interface includes:

- Header Navigation
- Footer
- Language Toggle
- Shopping Cart Access

Other application functions such as customer orders, addresses, checkout, payments, reviews, inventory management, and administrative operations are traced at the system and application level but are not represented as public UI wireframes in the current UI/UX document unless separately defined.

The following are explicitly outside the current UI scope:

- Showroom Appointment
- Wishlist
- Separate React frontend
- REST API frontend
- Node.js frontend

The **showroom/appointment functionality is outside the current Ruqi Store scope**.

The **Wishlist is a future feature and is not part of the current UI implementation**.

---

## 🏗️ Architecture Traceability Summary

Ruqi Store follows a monolithic server-rendered architecture:

```text
User
  │
  ▼
Razor Views (.cshtml)
  │
  ▼
ASP.NET Core MVC Controllers
  │
  ▼
Application / Service Layer
  │
  ▼
Repository / EF Core
  │
  ▼
Database
```
## Authentication and authorization are provided through:

ASP.NET Core Identity

        │

        ├── ApplicationUser

        ├── IdentityRole

        ├── UserManager

        └── SignInManager

The RTM therefore maintains traceability between:

Requirements

     ↓

Domain Model

     ↓

Database Design

     ↓

Service / Detailed Design

     ↓

MVC Architecture

     ↓

Razor UI

---

## 🔍 Final Verification

Before considering the requirements traceability complete, verify that:

-  Functional requirements are assigned Req IDs.
-  Non-functional requirements are assigned Req IDs.
-  Domain entities are connected to requirements.
-  Database representations are connected to requirements.
-  Service/application components are connected to requirements.
-  UI pages are connected where applicable.
-  Authentication and role authorization are traceable.
-  Customer data isolation is traceable.
-  Database integrity is traceable.
-  Transactional checkout behavior is traceable.
-  Payment accountability is traceable.
-  Auditability is traceable.
-  Localization and RTL/LTR support are traceable.
-  Responsive and accessible UI requirements are traceable.
-  The documented architecture remains Monolithic ASP.NET Core MVC.
-  Future/out-of-scope features are not presented as current UI screens.

---

[← Previous: UI/UX Design](./11-ui-ux-design.md) | [Back to Index](./00-index.md) | [Next: Appendices →](./13-appendices.md)
