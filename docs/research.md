Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Author: AWS Student / Lead Developer
Status: Approved for Implementation
Revision: v1.1 (Clarity & Production Readiness Update)

1. Multi-Tenancy Architecture Analysis
Multi-tenancy refers to the ability of a single SaaS application instance to serve multiple independent customer organizations (tenants) while ensuring logical data isolation, security, and performance fairness.

The most critical architectural decision in any SaaS system is how tenant data is stored and isolated, as it directly impacts:

Security & compliance

Cost efficiency

Scalability

Operational complexity

Speed of onboarding new tenants

The following three industry-standard approaches were evaluated.

A. Shared Database, Shared Schema (Discriminator Column)
Also known as the Pool Model.
All tenants share the same database and same schema, with tenant isolation enforced using a discriminator column such as tenant_id.

Mechanism
Every tenant-owned table contains a tenant_id

Every query is scoped with tenant_id

Isolation is enforced at the application + ORM layer

Pros
Lowest infrastructure cost

Instant tenant provisioning (no DB setup)

Simple CI/CD and DevOps

Easy system-wide analytics

Excellent fit for MVP and early-stage SaaS

Cons
Risk of data leakage if tenant scoping is missed

Per-tenant backups are difficult

Noisy-neighbor performance risk at scale

B. Shared Database, Separate Schemas (Schema-per-Tenant)
Also known as the Bridge Model.
Each tenant is assigned a dedicated schema inside the same database.

Mechanism
Dynamic schema resolution per request

Database search_path is set at runtime

Queries are automatically schema-scoped

Pros
Better isolation than shared schema

Easier tenant-level backups

Allows limited tenant customization

Cons
Schema migrations scale poorly

Schema catalog grows rapidly

Slower deployments with many tenants

C. Separate Databases (Database-per-Tenant)
Also known as the Silo Model.
Each tenant runs in a fully isolated database.

Mechanism
Central tenant catalog maps tenants to DB connections

Connection routing layer required

Pros
Maximum isolation and security

No noisy-neighbor impact

Required for regulated industries (finance, healthcare)

Cons
Very high operational cost

Complex DevOps and monitoring

Poor fit for freemium or MVP SaaS

Comparison Summary
Feature	Shared Schema	Separate Schema	Separate Database
Tenant Isolation	Low	Medium	High
Cost Efficiency	High	Medium	Low
Onboarding Speed	Instant	Fast	Slow
DevOps Complexity	Low	Medium	High
Data Leak Risk	High	Medium	Very Low
Maintenance Effort	Easy	Hard	Very Hard
Scalability Model	Vertical	Vertical	Horizontal

Final Decision: Shared Database + Shared Schema
Justification
MVP & Startup Fit
Prioritizes feature velocity over infrastructure complexity.

ORM Compatibility (Prisma)
Prisma performs best with a single schema + discriminator pattern.

Dockerized Deployment
Works cleanly with Docker Compose and single-command setup.

Controlled Risk via Middleware
Tenant isolation is enforced centrally via middleware, eliminating human error.

Future Migration Path
Architecture allows selective tenant migration to separate schemas/databases if required later.

2. Technology Stack Justification
Backend: Node.js + Express
Why

Non-blocking I/O for high concurrency

Middleware-first design suits tenant isolation

Single language across backend & frontend

Rejected

Django (sync execution model)

Spring Boot (heavy runtime & memory footprint)

Frontend: React (Vite)
Why

Component-based architecture

Fast development with Vite

Strong ecosystem (Router, Axios, State tools)

Rejected

Angular (steeper learning curve, verbose)

Database: PostgreSQL
Why

Strong relational guarantees

Cascade deletes & referential integrity

JSONB for flexible metadata

Supports future Row-Level Security (RLS)

Rejected

MongoDB (weaker relational consistency)

Authentication: JWT (JSON Web Tokens)
Why

Stateless & horizontally scalable

Works across subdomains

No shared session store needed

Rejected

Server sessions (Redis dependency)

Deployment: Docker & Docker Compose
Why

Environment parity (dev = prod)

Simplified onboarding for developers

Easy service orchestration

3. Security Architecture
3.1 Middleware-Based Tenant Isolation
JWT validated on every request

tenant_id injected server-side

Client-supplied tenant identifiers are ignored

Controllers never accept tenant_id directly

3.2 Secure Password Storage
Passwords hashed using Bcrypt

Salt rounds ≥ 10

Resistant to brute-force & rainbow table attacks

3.3 Role-Based Access Control (RBAC)
Role	Access Scope
Super Admin	Platform-wide
Tenant Admin	Full tenant control
User	Restricted actions

RBAC guards execute before business logic.

3.4 API Hardening
Strict CORS configuration

Rate limiting on auth endpoints

Helmet security headers

Request size limits

3.5 Input Validation & ORM Safety
Schema-based input validation

Prisma parameterized queries

Prevents SQL Injection & mass assignment attacks

Data Isolation Strategy (Row-Level Pattern)
All tenant-owned tables include tenant_id

Queries are always tenant-scoped

sql
Copy code
SELECT *
FROM tasks
WHERE id = :taskId
AND tenant_id = :jwtTenantId;
Security Behavior
Cross-tenant access returns 404 Not Found

Foreign tenant data is completely invisible

Prevents data enumeration attacks

Authentication & Authorization Flow
Step-by-Step Flow
User Login
User submits credentials + tenant subdomain.

Tenant Resolution
Backend resolves tenant via subdomain.

User Validation
User credentials verified within tenant scope.

JWT Issuance

json
Copy code
{
  "userId": "<uuid>",
  "tenantId": "<uuid>",
  "role": "<role>"
}
Authenticated Requests
JWT sent via Authorization: Bearer <token>

Token Verification
Signature validated, payload attached to request.

RBAC Enforcement
Role permissions checked before DB access.

Request Lifecycle (Mermaid Diagram)
mermaid
Copy code
sequenceDiagram
    autonumber
    participant User as Client
    participant API as Express API
    participant Auth as Auth Middleware
    participant RBAC as RBAC Guard
    participant DB as PostgreSQL

    User->>API: POST /auth/login
    API->>DB: Resolve tenant
    API->>DB: Validate user
    API-->>User: JWT issued

    User->>API: Request with JWT
    API->>Auth: Verify token
    Auth-->>API: Claims attached
    API->>RBAC: Check permissions
    RBAC-->>API: Allowed
    API->>DB: Tenant-scoped query
    DB-->>API: Data
    API-->>User: 200 OK
✅ Final Note (Interview Ready)
This architecture:

Matches real-world SaaS systems

Is MVP-friendly

Is secure by default

Can evolve into schema/db-per-tenant when needed

