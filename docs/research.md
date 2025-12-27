Research Document: Multi-Tenant SaaS Platform Architecture
Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Author: AWS Student / Lead Developer
Status: Approved for Implementation

1. Multi-Tenancy Architecture Analysis
Multi-tenancy refers to the architectural capability of a single application instance serving multiple independent customer organizations (tenants) while ensuring logical data isolation and security.

In SaaS systems, the database design strategy is the most critical architectural decision, as it directly affects:

Scalability

Tenant isolation

Operational complexity

Infrastructure cost

This document evaluates the three widely adopted multi-tenancy models and justifies the selected approach.

2. Multi-Tenancy Models Evaluated
A. Shared Database, Shared Schema (Discriminator-Based Model)
Also known as the Pool Model.
All tenants share a single database and schema. Logical isolation is achieved using a discriminator column such as tenant_id.

Mechanism
All tenant-owned tables include a tenant_id column

All queries explicitly filter by tenant_id

Isolation enforced at the application layer

Advantages
Lowest infrastructure and operational cost

Instant tenant onboarding

Simple DevOps and schema migrations

Enables easy cross-tenant analytics

Disadvantages
Risk of data leaks if tenant_id filtering is missed

Per-tenant backup and restore is complex

Potential noisy-neighbor performance issues

B. Shared Database, Separate Schemas (Schema-per-Tenant Model)
Also known as the Bridge Model.
Each tenant has a dedicated database schema (tenant_a.users, tenant_b.projects, etc.).

Mechanism
Database search path set dynamically per request

Schema-level isolation handled by the database engine

Advantages
Stronger isolation than shared schema

Easier per-tenant backup and restore

Allows limited tenant-level customization

Disadvantages
Schema migrations scale linearly with tenant count

Schema catalog growth impacts performance

Slower CI/CD pipelines at scale

C. Separate Databases (Database-per-Tenant Model)
Also known as the Silo Model.
Each tenant has a fully isolated database instance.

Mechanism
Tenant routing layer maps tenants to database connections

Complete physical isolation

Advantages
Maximum data isolation and security

No noisy-neighbor impact

Ideal for regulated or enterprise environments

Disadvantages
Very high infrastructure and maintenance cost

Complex DevOps and monitoring

Poor fit for freemium or MVP SaaS products

3. Comparative Analysis
Dimension	Shared Schema	Separate Schema	Separate Database
Isolation Level	Low	Medium	High
Cost Efficiency	High	Medium	Low
Tenant Onboarding	Instant	Fast	Slow
DevOps Complexity	Low	Medium	High
Data Leak Risk	High	Medium	Very Low
Maintenance Effort	Easy	Hard	Very Hard
Scalability Model	Vertical	Vertical	Horizontal

4. Selected Approach
✅ Shared Database + Shared Schema
Justification
MVP & Learning Scope
Prioritizes feature development and SaaS fundamentals over complex infrastructure orchestration.

ORM Compatibility (Prisma)
Prisma performs optimally with a single schema and discriminator-based isolation.

Containerized Deployment
Works seamlessly with Docker Compose and single-command deployments.

Risk Mitigation Strategy
Tenant isolation enforced centrally via middleware that automatically injects tenant_id, minimizing developer error.

5. Technology Stack Justification
Backend: Node.js + Express
Rationale

Non-blocking I/O supports high concurrency

Middleware architecture simplifies tenant isolation

Single language across frontend and backend

Alternatives Rejected

Django (synchronous request model)

Spring Boot (heavy runtime overhead)

Frontend: React (Vite)
Rationale

Component-based architecture

Fast build and hot reload via Vite

Mature ecosystem (Router, Axios)

Alternatives Rejected

Angular (steep learning curve, heavier framework)

Database: PostgreSQL
Rationale

Strong relational integrity and constraints

Supports cascade deletes and indexing

JSONB support for future extensibility

Compatible with future Row-Level Security (RLS)

Alternatives Rejected

MongoDB (weak relational guarantees for this domain)

Authentication: JWT (JSON Web Tokens)
Rationale

Stateless and horizontally scalable

Works across subdomains

No centralized session store required

Alternatives Rejected

Server-side sessions (Redis dependency and scaling overhead)

Deployment: Docker & Docker Compose
Rationale

Ensures environment consistency

Simplifies onboarding and deployment

Supports multi-container orchestration

6. Security Design & Risk Mitigation
6.1 Middleware-Based Tenant Isolation
JWT validated on every request

tenant_id injected server-side

Client-supplied tenant identifiers are never trusted

6.2 Secure Password Storage
Passwords hashed using Bcrypt

Minimum of 10 salt rounds

Protects against brute-force and rainbow table attacks

6.3 Role-Based Access Control (RBAC)
Role	Access Scope
Super Admin	Global system access
Tenant Admin	Full tenant-level control
User	Restricted tenant access

RBAC checks execute before database access, enforcing least-privilege access.

6.4 API Hardening
Strict CORS policies

Rate limiting on authentication endpoints

Security headers via Helmet middleware

6.5 Input Validation & ORM Safety
Schema-based input validation

Parameterized queries via Prisma

Eliminates SQL injection risks

7. Data Isolation Strategy (Row-Level Pattern)
tenant_id present in all tenant-owned tables

Every query is explicitly scoped:

sql
Copy code
SELECT *
FROM tasks
WHERE id = :taskId
  AND tenant_id = :jwtTenantId;
If no row matches, the system returns 404 Not Found, ensuring foreign tenant data remains completely invisible.

8. Authentication & Authorization Flow
User Login
User submits credentials along with tenant subdomain.

Tenant Resolution
Backend resolves tenant using subdomain.

Credential Validation
User credentials validated within tenant scope.

JWT Issuance
Backend issues signed JWT containing:

json
Copy code
{
  "userId": "<uuid>",
  "tenantId": "<uuid>",
  "role": "<role>"
}
Authenticated Requests
Client sends JWT with every protected request:

http
Copy code
Authorization: Bearer <token>
Token Validation
Middleware verifies JWT signature and extracts claims.

Authorization (RBAC)
Role guards validate permissions before business logic execution.

9. Authentication & Authorization Sequence
mermaid
Copy code
sequenceDiagram
    autonumber
    participant User as Client
    participant API as Backend API
    participant Auth as Auth Middleware
    participant RBAC as RBAC Guard
    participant DB as PostgreSQL

    User->>API: Login (credentials + subdomain)
    API->>DB: Resolve tenant
    API->>DB: Validate user
    API->>API: Compare password (Bcrypt)
    API-->>User: JWT issued

    User->>API: Request with JWT
    API->>Auth: Verify JWT
    Auth-->>API: Claims attached
    API->>RBAC: Check permissions
    RBAC-->>API: Access granted
    API->>DB: Query with tenant_id
    DB-->>API: Scoped data
    API-->>User: Response
