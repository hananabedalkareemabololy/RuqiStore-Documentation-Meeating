# 01. Project Introduction

## 1.1 Project Overview

**Ruqi Store** is a single-vendor, web-based furniture e-commerce and retail management system designed specifically for furniture retail. The system provides a complete digital environment that allows customers to discover furniture products online, search and filter the catalog, view detailed product information, manage a shopping cart, complete checkout, place orders, track fulfillment and payment status, and submit verified product reviews.

Unlike a generic multi-vendor marketplace, Ruqi Store is designed for **one furniture store** with a single product catalog and centralized store operations. The system focuses exclusively on furniture products and provides dedicated functionality for both customers and internal store staff.

Customers can browse a paginated furniture catalog, search by product name, and filter products by category, price range, material, and stock availability. Each product can contain detailed specifications including SKU, dimensions, weight, material, price, stock status, multiple product images, average rating, and verified customer reviews.

Customers can add products to a persistent database-backed shopping cart and proceed through a structured checkout process. During checkout, customers can select one of their saved delivery addresses or enter a new address, select **Cash on Delivery** or **Bank Transfer**, review their order summary, and confirm the order.

Order placement is processed through an **atomic Entity Framework Core transaction**. The transaction creates the order and its items, stores price and product-name snapshots, deducts the required stock, and clears the customer's cart. If any part of the transaction fails, the operation is rolled back so that stock and cart data remain consistent.

After an order is placed, the **Store Manager** manages its fulfillment status through the defined workflow, while the **Payment Officer** manages the payment status. This separation ensures that fulfillment and payment responsibilities are handled independently.

The system also provides a dedicated **Administrator** area for platform-wide oversight. The Administrator can manage users and roles, moderate reviews, monitor orders, view immutable audit logs, and generate CSV reports.

Ruqi Store is implemented as a **Monolithic ASP.NET Core MVC application** using Razor Views, HTML5, CSS3, Vanilla JavaScript, Entity Framework Core, ASP.NET Core Identity, SQLite for development, and SQL Server as the planned production database.

---

## 1.2 Problem Statement

Traditional single-store furniture retailers may face several operational and customer-experience problems when their sales and management processes are not supported by a centralized digital system.

The main problems addressed by Ruqi Store include:

| Problem Area | Description |
| :--- | :--- |
| **Limited Digital Presence** | Customers may have difficulty discovering and browsing the store's furniture catalog remotely without a dedicated online storefront. |
| **Difficult Product Discovery** | Customers need efficient search and filtering tools to find furniture based on category, price, material, and availability. |
| **High-Value Furniture Purchases** | Furniture customers often need detailed information such as dimensions, material, images, stock availability, and reviews before purchasing. |
| **Manual Order Management** | Without centralized order tracking, managing order fulfillment and monitoring customer orders becomes more difficult. |
| **Inventory Management Challenges** | Incorrect or delayed stock updates can lead to inaccurate availability information and potential overselling. |
| **Payment Confirmation** | Furniture purchases may require direct communication with customers to confirm payment details, product availability, and delivery arrangements. |
| **Lack of Role Separation** | Product management, fulfillment, payment confirmation, and system administration require different responsibilities and permissions. |
| **Limited Management Visibility** | Without centralized reports and audit logs, administrators have less visibility into system activity and operational performance. |

Ruqi Store addresses these challenges by combining online furniture shopping with centralized product, inventory, order, payment, user, review, and administrative management.

---

## 1.3 Project Scope

### 🟢 In Scope

* **User & Identity Management:** Secure registration, authentication, profile management, and role-based authorization using ASP.NET Core Identity for Customer, Store Manager, Payment Officer, and Administrator roles.
* **Furniture Product Catalog:** Searchable and filterable furniture catalog with categories, prices, materials, stock availability, product specifications, and product images.
* **Product Management:** Store Managers can create, edit, activate/deactivate, and manage furniture products and categories.
* **Shopping Cart:** Persistent database-backed cart with quantity management and real-time stock validation.
* **Checkout & Order Placement:** Customers can select delivery addresses, choose Cash on Delivery or Bank Transfer, review order details, and place orders through an atomic EF Core transaction.
* **Order Management:** Customers can view their order history and track fulfillment and payment statuses.
* **Fulfillment Management:** Store Managers can update order fulfillment through Pending → Processing → Shipped → Delivered, with cancellation supported according to the defined business rules.
* **Payment Management:** Payment Officers review payment submissions and mark orders as Paid or Rejected while maintaining payment history.
* **Verified Product Reviews:** Customers can review products only after purchasing them and receiving a Delivered order, with one review allowed per product per customer.
* **Inventory Management:** Store Managers can monitor and update stock quantities, with low-stock alerts and stock validation during order placement.
* **Category Management:** Store Managers can create, rename, reorder, activate, and deactivate furniture categories.
* **Address Book:** Customers can save and manage up to **5 delivery addresses**.
* **File Uploads:** Store Managers can upload up to **8 product images** per product. Supported formats are JPEG, PNG, and WebP, with a maximum size of **5 MB per image** and server-side magic-byte validation.
* **Administrator Panel:** Administrators can manage users, assign or revoke roles, oversee orders, moderate reviews, view audit logs, and generate CSV reports.
* **Audit Logging:** Important administrative actions are recorded in an append-only AuditLogs table.
* **Bilingual Interface:** The system supports Arabic (RTL) and English (LTR), with the selected culture stored in a cookie.
* **Responsive Web Interface:** The system provides a responsive interface supporting screen widths from **320px to 2560px**.

### 🔴 Out of Scope

* **Multi-Vendor Marketplace:** The system supports one furniture store and one centralized product catalog only.
* **Non-Furniture Categories:** The system is exclusively designed for furniture retail.
* **Showroom / Appointment Booking:** The current system is online-only and does not provide showroom appointment functionality.
* **Online Payment Gateway:** Stripe, PayPal, and other direct online payment gateways are not integrated in the current version. The current payment model supports Cash on Delivery and Bank Transfer with direct customer communication.
* **Native Mobile Applications:** Dedicated iOS and Android applications are not included in the current version. The system is delivered as a responsive web application.
* **Wishlist:** Wishlist functionality is planned as a future feature and is not available in the current version.
* **Redis Caching:** Redis is not currently implemented and is planned as a future performance enhancement.
* **AR Room Visualization:** Augmented-reality furniture visualization is planned for a future phase.

---

## Project Objective

|   #   | Objective | Success Metric |
| :---: | :--- | :--- |
| **1** | **Provide an Accessible Online Furniture Store** | Customers can browse, search, filter, and view furniture products through a responsive web interface. |
| **2** | **Simplify Furniture Purchasing** | Customers can complete the workflow from product browsing → cart → checkout → order placement without requiring a separate purchasing system. |
| **3** | **Improve Order Processing** | Orders are created through an atomic EF Core transaction with stock deduction, price snapshots, and cart clearing performed consistently. |
| **4** | **Improve Inventory Accuracy** | Stock quantities are validated during cart and order operations and cannot become negative. |
| **5** | **Separate Operational Responsibilities** | Store Manager controls fulfillment and inventory, Payment Officer controls payment status, and Administrator controls system-wide administration. |
| **6** | **Provide Reliable Payment Management** | Payment Officers can review payment submissions, mark orders Paid or Rejected, and maintain payment history. |
| **7** | **Increase Customer Trust** | Customers can access detailed product specifications and verified-purchase reviews before and after purchasing. |
| **8** | **Improve Administrative Oversight** | Administrators can manage users, roles, reviews, audit logs, orders, and CSV reports from a centralized administration area. |
| **9** | **Provide Secure System Access** | Authentication uses ASP.NET Core Identity with PBKDF2 password hashing, secure HttpOnly cookies, role-based authorization, and CSRF protection. |
| **10** | **Provide a Responsive and Accessible Interface** | The system supports responsive layouts from **320px to 2560px** and targets **WCAG 2.1 AA** accessibility. |
| **11** | **Maintain Reliable Performance** | Target page render time is **less than 2 seconds**, with controller actions targeted at **less than 500 ms at p95**. |
| **12** | **Support System Reliability** | The system targets **99.5% uptime**, automated daily database backups, and transaction rollback on failed operations. |
| **13** | **Support Future Scalability** | Clean Service Layer and Repository Pattern separation allows future enhancements such as Redis caching and production migration to SQL Server. |

---

## 1.5 Methodology

This project follows an **Agile (Scrum) methodology** to support iterative development, continuous validation, and gradual improvement of the Ruqi Store system.

The project is developed in manageable increments so that major components can be implemented, tested, reviewed, and refined before the system is finalized.

The rationale behind this decision includes:

* **Iterative Requirement Refinement:** Furniture catalog functionality, shopping workflows, order processing, payment management, and administrative features can be reviewed and refined throughout development.
* **Incremental Feature Validation:** Core modules—including product browsing, shopping cart management, checkout, order tracking, payment management, inventory management, and administration—can be implemented and validated incrementally.
* **Continuous Stakeholder Feedback:** Regular reviews allow project stakeholders and supervisors to evaluate implemented functionality and identify required improvements.
* **Risk Reduction:** Developing the system in smaller iterations helps identify implementation problems early and reduces the risk of major defects accumulating near the end of development.
* **Separation of Responsibilities:** The development process can independently validate Customer, Store Manager, Payment Officer, and Administrator functionality while maintaining consistency across the complete system.
* **Continuous Testing:** Functional requirements and business rules can be verified as individual modules are completed, including stock validation, transaction processing, role authorization, payment status management, and verified reviews.

---

## 1.6 Document Audience

| Audience | What They Need |
| :--- | :--- |
| **Developers** | ASP.NET Core MVC architecture, Service and Repository layers, EF Core entities, Identity configuration, database structure, routing, and implementation constraints. |
| **Business Analysts** | Project scope, functional requirements, business rules, user roles, workflows, and traceability between system objectives and implemented features. |
| **Project Managers** | Project scope, objectives, system modules, development methodology, role responsibilities, and future enhancement boundaries. |
| **QA / Testers** | Use cases, business rules, validation requirements, role permissions, order workflows, payment workflows, security requirements, and acceptance criteria. |
| **Store Management** | Product management, inventory control, order fulfillment, payment confirmation, reporting, and administrative oversight capabilities. |
| **Project Supervisors / Evaluators** | Overall project idea, problem statement, objectives, scope, architecture, technologies, system roles, and major functional capabilities. |

---

## 1.7 Project Constraints

| Constraint | Description |
| :--- | :--- |
| **Single-Vendor Model** | The system is designed for one furniture store and does not support multiple independent vendors. |
| **Furniture Domain** | Only furniture products are supported by the current system. |
| **Monolithic Architecture** | The system is implemented as a single ASP.NET Core MVC application with no separate API server or microservices layer. |
| **Frontend Technology** | The presentation layer uses Razor Views, HTML5, CSS3, and Vanilla JavaScript without React, Vue, Angular, jQuery, or Bootstrap JavaScript. |
| **Development Database** | SQLite is used during development because it requires minimal setup and can be shared as a single database file. |
| **Production Database** | SQL Server is planned for production deployment. |
| **Authentication** | ASP.NET Core Identity with secure cookies is used instead of JWT-based authentication. |
| **Online-Only System** | Showroom visits and appointment booking are outside the current scope. |
| **Payment Gateway** | Stripe and PayPal are not currently integrated. |
| **Role Structure** | The system contains four official user roles: Customer, Store Manager, Payment Officer, and Administrator. |

---

## 1.8 Key System Characteristics

Ruqi Store is characterized by the following core principles:

### Customer-Centered Shopping

The system provides customers with a structured furniture discovery and purchasing experience, including product search, filtering, detailed product information, shopping cart management, checkout, order tracking, and verified reviews.

### Role-Based Operations

Each internal responsibility is separated into an appropriate role:

* **Customer** — shopping and personal order management.
* **Store Manager** — products, categories, inventory, and fulfillment.
* **Payment Officer** — payment confirmation and payment records.
* **Administrator** — system-wide oversight and administration.

### Data Consistency

Critical operations such as order placement use EF Core transactions to maintain consistency between orders, order items, inventory, and shopping carts.

### Historical Data Preservation

Product deletion uses soft deletion, and order items preserve historical product names and prices through snapshots so that changes to current products do not alter historical orders.

### Security and Authorization

The system uses ASP.NET Core Identity, role-based authorization, secure cookies, CSRF protection, parameterized EF Core queries, and Razor output encoding.

### Future-Oriented Architecture

The Service Layer and Repository Pattern provide a clean foundation for future enhancements without requiring the current monolithic architecture to be replaced.

---

## 1.9 Current Payment Model

Ruqi Store currently uses a payment workflow designed for furniture retail rather than direct online payment gateway processing.

The current workflow is:

1. The **Customer** places an order online.
2. Customer and order information are stored securely in the database.
3. The **Payment Officer** reviews the payment submission.
4. The store communicates directly with the customer when required to confirm payment details, product availability, and delivery arrangements.
5. The Payment Officer records the resulting payment status as **Paid** or **Rejected**.
6. The payment activity is maintained in the payment history.

The current payment methods are:

* **Cash on Delivery**
* **Bank Transfer**

This approach supports furniture purchases where customers may require additional communication regarding:

* Payment confirmation
* Product availability
* Delivery arrangements
* Assembly requirements
* Customization or other purchase-related details

> **Note:** Stripe or another online payment gateway may be integrated as a future enhancement.

---

## 1.10 Future Enhancements

| Enhancement | Description |
| :--- | :--- |
| **Wishlist** | Allow customers to save favorite furniture products for future purchases. |
| **Redis Caching** | Cache frequently accessed data such as categories and featured products to improve performance when traffic increases. |
| **Stripe / Payment Gateway** | Enable direct online payment processing through an integrated payment provider. |
| **AR Room Visualizer** | Allow customers to visualize furniture within their own spaces before purchasing. |
| **Native Mobile Application** | Provide dedicated iOS and Android applications using the same backend services. |

---

[← Back to Index](./00-index.md) | [Next: Stakeholder Analysis →](./02-stakeholder-analysis.md)
