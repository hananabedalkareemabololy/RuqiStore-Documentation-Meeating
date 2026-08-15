# 13. Appendices

## Appendix A: Glossary

| Term | Definition |
|------|-----------|
| Actor | An external entity that interacts with the Ruqi Store system, such as Customer, Store Manager, Payment Officer, or Administrator. |
| ASP.NET Core MVC | The monolithic web application framework used to implement the Ruqi Store backend, controllers, routing, and server-rendered Razor Views. |
| ASP.NET Core Identity | The authentication and authorization framework used for user accounts, password management, authentication cookies, and role-based access control. |
| CRUD | Create, Read, Update, Delete — the basic operations performed on system data such as products, categories, users, and addresses. |
| EF Core | Entity Framework Core — the Object-Relational Mapper (ORM) used for database access, LINQ queries, Code-First development, migrations, and transactions. |
| ER Diagram | Entity-Relationship Diagram — a visual representation of database entities, tables, keys, and relationships within the Ruqi Store database. |
| LTR | Left-to-Right — the standard layout direction used for English content. |
| MoSCoW | A prioritization method consisting of Must have, Should have, Could have, and Won't have requirements. |
| NFR | Non-Functional Requirement — a quality or system constraint such as security, performance, accessibility, or usability. |
| Price Snapshot | A persistent copy of the product price stored in `OrderItem.UnitPrice` when an order is created, ensuring historical order prices remain unchanged after future product price updates. |
| Razor Views | Server-rendered `.cshtml` views used by ASP.NET Core MVC to generate the Ruqi Store user interface. |
| RTL | Right-to-Left — the layout direction used for Arabic content and supported by the Ruqi Store bilingual interface. |
| SKU | Stock Keeping Unit — a unique identifier assigned to a furniture product for catalog and inventory management. |
| Soft Delete | Deactivating a product by setting `IsActive = false` instead of physically deleting the database record, preserving historical order references. |
| SRS | Software Requirements Specification — a document defining the functional, non-functional, technical, and business requirements of the Ruqi Store system. |
| Three-Tier Architecture | The application architecture separating Presentation/Controllers, Business Logic/Services, and Data Access/Repositories with EF Core and the database. |
| UML | Unified Modeling Language — a standardized modeling language used to describe system structure, behavior, and interactions. |
| Use Case | A description of how an actor interacts with the system to achieve a specific goal, such as placing an order or managing products. |
| WCAG | Web Content Accessibility Guidelines — accessibility standards used to make the Ruqi Store interface accessible to users with different abilities. |

## Appendix B: References

| # | Reference | Used In |
|---|-----------|---------|
| 1 | IEEE 830-1998: Recommended Practice for Software Requirements Specifications | Requirements Documentation |
| 2 | UML 2.5 Specification (OMG) | System Modeling, Use Cases, Behavioral and Structural Diagrams |
| 3 | WCAG 2.1 Level AA Guidelines (W3C) | `11-ui-ux-design.md`, `11-testing-strategy.md` |
| 4 | ASP.NET Core MVC Documentation (Microsoft) | Architecture, Controllers, Routing, Razor Views |
| 5 | ASP.NET Core Identity Documentation (Microsoft) | Authentication, Authorization, Users, and Roles |
| 6 | Entity Framework Core Documentation (Microsoft) | Data Access, Code-First, Migrations, LINQ, and Transactions |
| 7 | C# Documentation (Microsoft) | Application and Service Layer Implementation |
| 8 | HTML5, CSS3, and JavaScript Standards | Frontend and Responsive UI Implementation |

## Appendix C: Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | May 10, 2026 | Analysis Team | Initial draft — requirements gathering and project scope. |
| 0.5 | May 24, 2026 | Analysis Team | Added core e-commerce requirements, user stories, use cases, and domain concepts. |
| 0.8 | Jun 08, 2026 | Design Team | Added MVC architecture, database design, service-layer rules, and checkout workflows. |
| 1.0 | Jun 22, 2026 | Full Team | Finalized the main system documentation and synchronized architecture and requirements. |
| 2.0 | Apr 2026 | Full Team | Updated and synchronized the documentation with the final Ruqi Store architecture: ASP.NET Core MVC, ASP.NET Core Identity, EF Core, SQLite for development, and SQL Server for production. |

## Appendix D: Diagram Index

| # | Diagram | Type | Section |
|---|---------|------|---------|
| 1 | Stakeholder Map | Quadrant Chart | Requirements Analysis |
| 2 | Actor Generalization | UML Class Diagram | System Modeling |
| 3 | Use Case Diagram | Use Case Diagram | Use Cases |
| 4 | User Story Map | Block Diagram | Requirements Analysis |
| 5 | Domain Model / Class Diagram | Class Diagram | Domain Model |
| 6 | Enumeration Types | Class Diagram | Domain Model |
| 7 | Secure Checkout Sequence | Sequence Diagram | Behavioral Design |
| 8 | Add to Cart & Checkout Activity | Activity Diagram | Behavioral Design |
| 9 | Order Fulfillment State Machine | State Diagram | Behavioral Design |
| 10 | Stock Validation Activity | Activity Diagram | Business Rules |
| 11 | Database Entity-Relationship Diagram | ER Diagram | Database Design |
| 12 | Monolithic ASP.NET Core MVC Architecture | Architecture Diagram | System Architecture |
| 13 | Component Diagram | Component Diagram | System Architecture |
| 14 | Deployment Architecture | Deployment Diagram | System Architecture |
| 15 | User Interface Navigation Flow | Flowchart | UI/UX Design |

---

[← Previous: Traceability Matrix](./12-traceability.md) | [Back to Index](./00-index.md)
