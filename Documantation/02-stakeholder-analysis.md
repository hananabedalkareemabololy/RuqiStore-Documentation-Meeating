# 02. Stakeholder & User Role Analysis

## 2.1 Stakeholder

The **Ruqi Store** system has four official user roles. Each role has clearly defined responsibilities, permissions, interests, and concerns within the system.

| # | Stakeholder | Role | Interest | Influence | Key Concern |
| :---: | :--- | :--- | :--- | :---: | :--- |
| **1** | **Customer** | End User | Purchasing furniture online | High | Easy product discovery, reliable checkout, accurate stock, and clear order tracking |
| **2** | **Store Manager** | Store Operations | Product, inventory, and order management | High | Accurate inventory, efficient product management, and smooth order fulfillment |
| **3** | **Payment Officer** | Payment Management | Payment verification and order payment status | High | Accurate payment confirmation, rejection handling, and reliable payment records |
| **4** | **Administrator** | System Administration | Complete system oversight | High | User security, role management, review moderation, auditability, and system reports |

---

# 2.2 Stakeholder Map

This quadrant chart visualizes the four official Ruqi Store roles based on their level of interest and influence over the platform. It helps prioritize their requirements during system analysis and development.

```mermaid
quadrantChart
    title Stakeholder Influence vs. Interest

    x-axis Low Interest --> High Interest
    y-axis Low Influence --> High Influence

    quadrant-1 Manage Closely
    quadrant-2 Keep Satisfied
    quadrant-3 Monitor
    quadrant-4 Keep Informed

    "Administrator": [0.90, 0.95]
    "Store Manager": [0.90, 0.85]
    "Payment Officer": [0.75, 0.80]
    "Customer": [0.95, 0.55]
```
> **Note:** The positions in the map represent the relative importance of each system role for requirements analysis and are not measurements of individual people.

---

# 2.3 Stakeholder Needs Summary

## Customer

### Needs

- Easy access to the furniture catalog.
- Search and filtering by product name, category, price, material, and availability.
- Detailed furniture information including dimensions, weight, material, price, stock status, and images.
- Persistent database-backed shopping cart.
- Simple checkout process.
- Ability to select or create delivery addresses.
- Support for Cash on Delivery and Bank Transfer.
- Clear order confirmation after successful checkout.
- Ability to view order history and track fulfillment and payment status.
- Ability to submit verified-purchase product reviews.
- Responsive Arabic and English interface.
- Ability to save up to 5 delivery addresses.

### Key Goal

Provide a reliable and convenient online furniture shopping experience that allows customers to discover products, place orders, track purchases, and review products with confidence.

---

## Store Manager

### Needs

- Fast and intuitive management dashboard.
- Complete access to the furniture product catalog.
- Product creation, editing, activation, deactivation, and soft deletion.
- Category management.
- Inventory management and stock updates.
- Low-stock visibility and configurable stock thresholds.
- Product image management.
- Ability to upload up to 8 product images per product.
- Order management and fulfillment status updates.
- Sales and inventory analytics.
- Preservation of historical order prices when current product prices change.

### Key Goal

Manage the store's products, inventory, and order fulfillment efficiently while maintaining accurate stock levels and preserving historical order data.

---

## Payment Officer

### Needs

- Dedicated payment management dashboard.
- Access to orders requiring payment review.
- Ability to review payment submissions.
- Ability to mark orders as **Paid** or **Rejected**.
- Access to payment history records.
- Clear visibility of the customer's selected payment method.
- Separation between payment management and fulfillment management.
- Accurate and consistent payment status information.

### Key Goal

Ensure that customer payments are reviewed and recorded accurately while maintaining a clear separation between payment processing and order fulfillment responsibilities.

---

## Administrator

### Needs

- Centralized administration dashboard.
- Complete user management.
- Ability to activate and deactivate user accounts.
- Ability to assign and revoke Store Manager and Payment Officer roles.
- Platform-wide read-only order oversight.
- Review moderation and visibility control.
- CSV reporting and analytics.
- Immutable audit logs for important administrative actions.
- Ability to monitor system activity.
- Protection against unauthorized administrative actions.
- Prevention of administrators modifying their own permissions or deactivating their own account.

### Key Goal

Maintain complete system oversight, security, accountability, and administrative control while preserving the integrity of user, order, review, and audit data.

---

# 2.4 Stakeholder Priority Summary

| Stakeholder | Priority Level | Main Reason |
| :--- | :---: | :--- |
| **Administrator** | Critical | Responsible for system-wide oversight, user management, role management, audit logs, reviews, and reports |
| **Store Manager** | Critical | Controls the product catalog, inventory, categories, and order fulfillment |
| **Payment Officer** | High | Responsible for payment review, confirmation, rejection, and payment records |
| **Customer** | High | Direct end user whose shopping experience and successful orders are central to the system |

---

# 2.5 Stakeholder Responsibilities Summary

| Stakeholder | Main Responsibilities |
| :--- | :--- |
| **Customer** | Browse products, search and filter the catalog, manage cart, checkout, place orders, track orders, manage addresses, and submit verified reviews |
| **Store Manager** | Manage products, categories, inventory, product images, order fulfillment, and sales-related dashboard information |
| **Payment Officer** | Review payment submissions, update payment status, and maintain payment history |
| **Administrator** | Manage users and roles, oversee orders, moderate reviews, generate reports, and monitor immutable audit logs |

---

# 2.6 Role Boundaries

The Ruqi Store system maintains clear boundaries between the four official roles.

- **Customer** cannot manage store products, inventory, payments, users, or administrative functions.
- **Store Manager** manages products, categories, inventory, and fulfillment status but does not manage payment status.
- **Payment Officer** manages payment status but does not manage product inventory or fulfillment status.
- **Administrator** has system-wide administrative oversight but does not replace the Store Manager's operational responsibilities or the Payment Officer's payment workflow.
- Administrative permissions are protected through ASP.NET Core Identity role-based authorization.
- Customer data isolation is enforced through the Service Layer so customers can only access their own carts, orders, addresses, and reviews.

---

# 2.7 Stakeholder-to-System Feature Mapping

| Feature | Customer | Store Manager | Payment Officer | Administrator |
| :--- | :---: | :---: | :---: | :---: |
| Browse Product Catalog | ✅ | ✅ | ✅ | ✅ |
| Search & Filter Products | ✅ | ✅ | ✅ | ✅ |
| Shopping Cart | ✅ | ❌ | ❌ | ❌ |
| Checkout & Order Placement | ✅ | ❌ | ❌ | ❌ |
| Order Tracking | ✅ | ❌ | ❌ | ❌ |
| Product Reviews | ✅ | ❌ | ❌ | ✅ Moderation |
| Product Management | ❌ | ✅ | ❌ | ❌ |
| Category Management | ❌ | ✅ | ❌ | ❌ |
| Inventory Management | ❌ | ✅ | ❌ | ❌ |
| Fulfillment Status Management | ❌ | ✅ | ❌ | ❌ |
| Payment Status Management | ❌ | ❌ | ✅ | ❌ |
| Payment History | ❌ | ❌ | ✅ | ✅ Oversight |
| User Management | ❌ | ❌ | ❌ | ✅ |
| Role Assignment / Revocation | ❌ | ❌ | ❌ | ✅ |
| Order Oversight | Own Orders | All Orders | Payment-related Orders | All Orders |
| CSV Reports | ❌ | ❌ | ❌ | ✅ |
| Audit Logs | ❌ | ❌ | ❌ | ✅ |
| Address Book | ✅ | ❌ | ❌ | ❌ |

---

# Navigation

[← Previous: Project Introduction](./01-introduction.md) | [Back to Index](./00-index.md) | [Next: Requirements Specification →](./03-requirements.md)
