System Architecture Document
Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Version: 1.0
Author: AWS Student / Lead Developer

1. Architecture Overview
The system is built using a containerized three-tier web architecture, designed to ensure scalability, modularity, and secure tenant isolation.
All services are orchestrated using Docker Compose, providing consistent environments across development and deployment stages.

2. High-Level Architecture Diagram
mermaid
Copy code
graph LR
    User[Client Browser] --> Frontend[Frontend Container<br/>React + Vite]
    Frontend --> Backend[Backend Container<br/>Node.js + Express]
    Backend --> DB[(PostgreSQL Database)]

    subgraph DockerNetwork
        Frontend
        Backend
        DB
    end

    subgraph SecurityLayer
        Backend --> JWT[JWT Authentication]
        Backend --> RBAC[RBAC Middleware]
    end
3. System Architecture & Components
The application follows a multi-tenant, containerized architecture with a clear separation of concerns across:

Client Layer (Frontend)

Application Layer (Backend API)

Data Layer (Database)

Each layer is independently scalable and communicates through well-defined interfaces.

4. Component Details
4.1 Client Layer (Frontend)
Technology: React.js (Vite build tool)

Container Port Mapping: 3000 (External) → 3000 (Internal)

Responsibilities:

Render the user interface

Handle user interactions

Manage authentication state (JWT storage)

Communicate with backend REST APIs

Multi-Tenancy Handling:

Tenant context is resolved using:

Subdomain-based routing (e.g., tenant1.app.com), or

Tenant selection during login

4.2 Application Layer (Backend API)
Technology: Node.js, Express.js

Container Port Mapping: 5000 (External) → 5000 (Internal)

Responsibilities:

Execute business logic

Handle authentication using JWT

Enforce authorization via RBAC

Guarantee tenant-level data isolation

Tenant Isolation Mechanism:

JWT contains the tenant_id

Middleware extracts and validates tenant_id

All database queries are scoped by tenant_id

Prevents cross-tenant data access

4.3 Data Layer (Database)
Technology: PostgreSQL 15

Container Port Mapping: 5432 (External) → 5432 (Internal)

Responsibilities:

Persistent relational data storage

Isolation Strategy:

Shared Database, Shared Schema

Logical isolation using a tenant_id discriminator

tenant_id is present in all tenant-owned tables

5. High-Level Multi-Tenant Architecture
mermaid
Copy code
graph LR
    Client[Frontend<br/>React.js :3000] -->|HTTPS + JWT| API[Backend API<br/>Node.js :5000]
    API -->|Tenant-Scoped SQL Queries| DB[(PostgreSQL 15<br/>Shared Database)]

    subgraph Multi-Tenant Isolation
        API
        DB
    end
6. Database Schema Design (ERD)
The database schema is designed following Third Normal Form (3NF) to reduce redundancy and maintain data integrity.

The tenant_id column acts as the logical partition key, enabling secure multi-tenancy within a shared database, shared schema model.

mermaid
Copy code
erDiagram
    TENANTS ||--o{ USERS : owns
    TENANTS ||--o{ PROJECTS : owns
    TENANTS ||--o{ TASKS : owns
    TENANTS ||--o{ AUDIT_LOGS : records

    USERS ||--o{ PROJECTS : creates
    PROJECTS ||--o{ TASKS : contains
    USERS ||--o{ TASKS : assigned_to

    TENANTS {
        uuid id PK
        string name
        string subdomain UK
        string status
        string subscription_plan
        int max_users
        int max_projects
    }

    USERS {
        uuid id PK
        uuid tenant_id FK
        string email
        string password_hash
        string full_name
        string role
    }

    PROJECTS {
        uuid id PK
        uuid tenant_id FK
        uuid created_by FK
        string name
        string description
        string status
    }

    TASKS {
        uuid id PK
        uuid tenant_id FK
        uuid project_id FK
        uuid assigned_to FK
        string title
        string priority
        string status
        date due_date
    }

    AUDIT_LOGS {
        uuid id PK
        uuid tenant_id FK
        string action
        string entity_type
        uuid entity_id
        string ip_address
    }
7. Schema Details
7.1 tenants (Root Entity)
Primary Key: id (UUID)

Attributes:

name

subdomain (Unique)

status

subscription_plan

Limits & Constraints:

max_users

max_projects

Isolation Note:

Root table; does not contain tenant_id

7.2 users
Primary Key: id (UUID)

Foreign Key:

tenant_id → tenants.id (ON DELETE CASCADE) [ISOLATION KEY]

Attributes:

email

password_hash

full_name

role

Constraints:

UNIQUE (tenant_id, email)
(Email uniqueness enforced per tenant)

7.3 projects
Primary Key: id (UUID)

Foreign Keys:

tenant_id → tenants.id (ON DELETE CASCADE) [ISOLATION KEY]

created_by → users.id

Attributes:

name

description

status

Index:

sql
Copy code
CREATE INDEX idx_projects_tenant ON projects(tenant_id);
7.4 tasks
Primary Key: id (UUID)

Foreign Keys:

project_id → projects.id (ON DELETE CASCADE)

tenant_id → tenants.id [ISOLATION KEY]

assigned_to → users.id (Nullable)

Attributes:

title

priority

status

due_date

Index:

sql
Copy code
CREATE INDEX idx_tasks_tenant ON tasks(tenant_id);
7.5 audit_logs
Primary Key: id (UUID)

Foreign Key:

tenant_id → tenants.id [ISOLATION KEY]

Attributes:

action

entity_type

entity_id

ip_address

8. API Architecture
The system exposes 19 RESTful endpoints, following standard REST conventions and tenant-scoped access control.

Standard API Response Format
json
Copy code
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}
9. API Modules
Module A: Authentication
Method	Endpoint	Description	Auth Required	Role
POST	/api/auth/register-tenant	Register new tenant and admin	No	Public
POST	/api/auth/login	Authenticate user and issue JWT	No	Public
GET	/api/auth/me	Get current user context	Yes	Any
POST	/api/auth/logout	Logout user session	Yes	Any

Module B: Tenant Management
Method	Endpoint	Description	Auth Required	Role
GET	/api/tenants	List all tenants	Yes	super_admin
GET	/api/tenants/:tenantId	Get tenant details	Yes	super_admin, owner
PUT	/api/tenants/:tenantId	Update tenant settings	Yes	super_admin, tenant_admin

Module C: User Management
Method	Endpoint	Description	Auth Required	Role
POST	/api/tenants/:tenantId/users	Create user	Yes	tenant_admin
GET	/api/tenants/:tenantId/users	List users	Yes	Tenant member
PUT	/api/users/:userId	Update user	Yes	tenant_admin, Self
DELETE	/api/users/:userId	Delete user	Yes	tenant_admin

Module D: Project Management
Method	Endpoint	Description	Auth Required	Role
POST	/api/projects	Create project	Yes	Tenant member
GET	/api/projects	List projects	Yes	Tenant member
PUT	/api/projects/:projectId	Update project	Yes	Creator, Admin
DELETE	/api/projects/:projectId	Delete project	Yes	Creator, Admin

Module E: Task Management
Method	Endpoint	Description	Auth Required	Role
POST	/api/projects/:projectId/tasks	Create task	Yes	Tenant member
GET	/api/projects/:projectId/tasks	List tasks	Yes	Tenant member
PATCH	/api/tasks/:taskId/status	Update task status	Yes	Tenant member
PUT	/api/tasks/:taskId	Update task details	Yes	Tenant member

