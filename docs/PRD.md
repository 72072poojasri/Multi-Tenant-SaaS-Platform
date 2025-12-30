Multi-Tenant SaaS Platform
Overview
A production-ready B2B Multi-Tenant SaaS Platform built to demonstrate real-world system design principles such as Multi-Tenancy, Strict Data Isolation, and Role-Based Access Control (RBAC).
Each organization (Tenant) operates within a fully isolated workspace while sharing a common application infrastructure.

This platform mirrors how modern SaaS products like Jira, Notion, and Asana securely support multiple organizations within a single system.

Core Concepts Demonstrated
Multi-Tenant Architecture with strict tenant isolation using tenant_id

Role-Based Access Control (RBAC) across system and tenant levels

JWT-based Authentication & Authorization

Secure REST APIs with middleware-based validation

Scalable SaaS Architecture suitable for enterprise environments

User Roles & Permissions
1. Super Admin (System-Level)
Manage and monitor all registered tenants

View platform-wide usage metrics

No access to tenant-specific operational data

2. Tenant Admin (Organization-Level)
Manage organization users and roles

Create, update, and oversee projects and tasks

Control tenant-level access and configuration

3. Standard User
Access assigned projects only

Create and update tasks

No permissions for user or organization management

Key Features
🔐 Security & Authentication
JWT-based stateless authentication

Password hashing using Bcrypt

Secure CORS configuration

Role and tenant validation via middleware

🏢 Multi-Tenancy Architecture
Shared database with logical tenant isolation

All entities scoped using tenant_id

Protection against cross-tenant data access

📊 Project & Task Management
Tenant-specific project workspaces

Task assignments with priorities and due dates

Task lifecycle management
(Todo → In Progress → Completed)

👥 Team Management
Tenant Admins can:

Invite users

Remove users

Update user roles

🧑‍💻 Dashboards
Super Admin Dashboard: Global tenant overview

Tenant Dashboard: Organization-level analytics

🎨 Responsive UI
Clean and intuitive React-based interface

Fully responsive design using Flexbox and Grid

Technology Stack
Frontend
React.js (v18)

React Router DOM (v6)

Context API for state management

Axios for API communication

CSS3 (Flexbox & Grid)

Backend
Node.js (v18)

Express.js

Prisma ORM

JWT Authentication

Express Validator

Database & DevOps
PostgreSQL (v15)

Docker & Docker Compose

Alpine Linux-based containers

System Architecture
csharp
Copy code
Client (React)
     |
REST APIs (JWT Secured)
     |
Node.js + Express
     |
Prisma ORM
     |
PostgreSQL (tenant_id scoped data)
Installation & Setup
Prerequisites
Docker & Docker Compose (Recommended)

Node.js v18+

PostgreSQL (for local setup only)

Method 1: Docker Setup (Recommended)
Step 1: Clone the Repository
bash
Copy code
git clone <your-repo-url>
cd Multi-Tenant-SaaS-Platform
Step 2: Configure Environment Variables
Create a .env file in the root directory (or validate docker-compose.yml):

env
Copy code
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=saas_db
Step 3: Start the Application
bash
Copy code
docker-compose up -d --build
Step 4: Access the Application
Frontend: http://localhost:3000

Backend Health Check: http://localhost:5000/api/health

✔ Database migrations and seed data execute automatically

Method 2: Local Development Setup
Backend Setup
bash
Copy code
cd backend
npm install
cp .env.example .env
npx prisma migrate dev --name init
npm run seed
npm start
Frontend Setup
bash
Copy code
cd frontend
npm install
npm start
Environment Variables
Variable	Description	Default
PORT	Backend server port	5000
DATABASE_URL	PostgreSQL connection string	Docker value
JWT_SECRET	JWT signing secret	Custom
FRONTEND_URL	Allowed CORS origin	http://localhost:3000

API Overview
Base URL (Local): http://localhost:5000/api

All protected endpoints require:

makefile
Copy code
Authorization: Bearer <JWT_TOKEN>
Token Validity: 24 hours

API Modules Summary
System
GET /health

Authentication
POST /auth/register-tenant

POST /auth/login

GET /auth/me

Tenant Management (Super Admin)
GET /tenants

GET /tenants/:id

PUT /tenants/:id

User Management (Tenant Admin)
GET /tenants/:tenantId/users

POST /tenants/:tenantId/users

PUT /users/:id

DELETE /users/:id

Project Management
GET /projects

POST /projects

GET /projects/:id

PUT /projects/:id

Task Management
GET /projects/:projectId/tasks

POST /projects/:projectId/tasks

PATCH /tasks/:id/status

PUT /tasks/:id

Seeded Test Credentials
Super Admin
Email: superadmin@system.com

Password: Admin@123

Tenant Admin (Demo Tenant)
Email: admin@demo.com

Password: Admin@123

Subdomain: demo

Future Enhancements
OAuth authentication (Google / GitHub)

Subscription and billing integration (Stripe)

Audit logs and activity tracking

Fine-grained permission system

Microservices-based scaling

Project Purpose
This project is designed for portfolio presentation, technical interviews, and real-world SaaS architecture demonstration, highlighting full-stack development, backend security, and scalable system design.

System Architecture & PRD Summary
Containerized three-tier architecture

Strong tenant isolation using tenant_id

Clean separation of frontend, backend, and database layers

Enterprise-grade RBAC and security

Designed to meet both functional and non-functional SaaS requirements
