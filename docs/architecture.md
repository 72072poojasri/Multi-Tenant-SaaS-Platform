System Architecture Document
Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Version: 1.0
Author: AWS Student / Lead Developer

1. System Architecture Overview
The system is built using a containerized three-tier architecture to ensure scalability, modularity, and strict tenant isolation.
All components are orchestrated using Docker Compose, enabling consistent environments across development, staging, and production.

Architecture Style:

Multi-tenant SaaS

Shared Database, Shared Schema

Stateless backend with JWT-based authentication

2. High-Level Architecture
mermaid
Copy code
graph LR
    User[Client Browser] --> FE[Frontend<br/>React + Vite]
    FE --> BE[Backend API<br/>Node.js + Express]
    BE --> DB[(PostgreSQL)]

    subgraph Docker_Network
        FE
        BE
        DB
    end

    subgraph Security
        BE --> JWT[JWT Authentication]
        BE --> RBAC[RBAC Middleware]
    end
3. System Components
The architecture separates responsibilities across Client, Application, and Data layers.

3.1 Client Layer (Frontend)
Technology: React.js (Vite)

Port Mapping: 3000 (Host) → 3000 (Container)

Responsibilities:

Render UI and handle user interactions

Manage authentication state (JWT)

Communicate with backend APIs

Multi-Tenancy Handling:

Tenant context determined by:

Subdomain (tenant1.app.com), or

Tenant selection during login

3.2 Application Layer (Backend API)
Technology: Node.js, Express.js

Port Mapping: 5000 (Host) → 5000 (Container)

Responsibilities:

Business logic execution

JWT-based authentication

Role-Based Access Control (RBAC)

Tenant isolation enforcement

Tenant Isolation Mechanism:

tenant_id extracted from JWT payload

Injected into every database query

Prevents cross-tenant data access

3.3 Data Layer (Database)
Technology: PostgreSQL 15

Port Mapping: 5432 (Host) → 5432 (Container)

Responsibilities:

Persistent relational data storage

Enforce data integrity via constraints and indexes

Isolation Strategy:

Shared database and schema

Logical isolation using tenant_id in all tenant-owned tables

4. Multi-Tenant Architecture View
mermaid
Copy code
graph LR
    Client[Frontend<br/>React :3000] -->|HTTPS + JWT| API[Backend API<br/>Node :5000]
    API -->|SQL + tenant_id| DB[(PostgreSQL<br/>Shared DB)]

    subgraph Tenant_Isolation
        API
        DB
    end
5. Database Architecture & ER Diagram
The database schema is normalized to Third Normal Form (3NF).
The tenant_id column acts as the logical partition key to enforce tenant-level isolation.

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
6. Schema Details
6.1 tenants (Root Table)
Primary Key: id (UUID)

Attributes:
name, subdomain (UNIQUE), status, subscription_plan

Constraints:
max_users, max_projects

Note:
Root entity (does not reference tenant_id)

6.2 users
Primary Key: id (UUID)

Foreign Key:
tenant_id → tenants.id (ON DELETE CASCADE)

Constraints:
UNIQUE (tenant_id, email) ensures tenant-level email uniqueness

6.3 projects
Primary Key: id (UUID)

Foreign Keys:

tenant_id → tenants.id

created_by → users.id

Index:

sql
Copy code
CREATE INDEX idx_projects_tenant ON projects(tenant_id);
6.4 tasks
Primary Key: id (UUID)

Foreign Keys:

tenant_id → tenants.id

project_id → projects.id

assigned_to → users.id

Index:

sql
Copy code
CREATE INDEX idx_tasks_tenant ON tasks(tenant_id);
6.5 audit_logs
Primary Key: id (UUID)

Foreign Key:
tenant_id → tenants.id

Purpose:
Stores security and activity events for auditing and compliance

7. API Architecture
The backend exposes 19 RESTful endpoints, strictly following REST conventions and tenant scoping.

Standard API Response Format
json
Copy code
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}
Module A: Authentication
Method	Endpoint	Description	Auth	Role
POST	/api/auth/register-tenant	Register tenant & admin	No	Public
POST	/api/auth/login	Login and receive JWT	No	Public
GET	/api/auth/me	Get current user	Yes	Any
POST	/api/auth/logout	Logout user	Yes	Any

Module B: Tenant Management
Method	Endpoint	Description	Auth	Role
GET	/api/tenants	List all tenants	Yes	super_admin
GET	/api/tenants/:tenantId	Get tenant details	Yes	super_admin
PUT	/api/tenants/:tenantId	Update tenant	Yes	super_admin

Module C: User Management
Method	Endpoint	Description	Auth	Role
POST	/api/tenants/:tenantId/users	Create user	Yes	tenant_admin
GET	/api/tenants/:tenantId/users	List users	Yes	Tenant member
PUT	/api/users/:userId	Update user	Yes	Admin / Self
DELETE	/api/users/:userId	Delete user	Yes	tenant_admin

Module D: Project Management
Method	Endpoint	Description	Auth	Role
POST	/api/projects	Create project	Yes	Tenant member
GET	/api/projects	List projects	Yes	Tenant member
PUT	/api/projects/:projectId	Update project	Yes	Creator / Admin
DELETE	/api/projects/:projectId	Delete project	Yes	Creator / Admin

Module E: Task Management
Method	Endpoint	Description	Auth	Role
POST	/api/projects/:projectId/tasks	Create task	Yes	Tenant member
GET	/api/projects/:projectId/tasks	List tasks	Yes	Tenant member
PATCH	/api/tasks/:taskId/status	Update task status	Yes	Tenant member
PUT	/api/tasks/:taskId	Update task	Yes	Tenant member

