Multi-Tenant SaaS Platform
Overview
A production-grade B2B Multi-Tenant SaaS Platform built to demonstrate real-world system design principles such as Multi-Tenancy, Strict Data Isolation, and Role-Based Access Control (RBAC). Each organization (Tenant) operates within a completely isolated workspace while sharing the same application infrastructure.

This project illustrates how modern SaaS products (for example, Jira, Notion, and Asana) securely manage multiple organizations within a single system.

Core Concepts Demonstrated
Multi-Tenancy with logical isolation using tenant_id at the database level

Role-Based Access Control (RBAC) across system and tenant roles

JWT-based Authentication and Authorization

Secure Backend APIs with validation and access guards

Scalable SaaS Architecture designed for enterprise usage

User Roles & Permissions
1. Super Admin (System Level)
Manage all registered tenants

Monitor system-wide usage and platform health

No access to tenant-specific operational data

2. Tenant Admin (Organization Level)
Manage organization users

Assign roles and permissions

Create, update, and manage projects and tasks

3. Standard User
Access only assigned projects

Create and update tasks

Cannot manage users or organization-level settings

Key Features
🔐 Security & Authentication
JWT-based authentication

Password hashing using Bcrypt

Secure CORS configuration

Middleware-based role and tenant validation

🏢 Multi-Tenancy Architecture
Centralized database with logical tenant isolation

Every entity scoped using tenant_id

Protection against cross-tenant data access

📊 Project & Task Management
Tenant-specific projects

Task assignment with priorities and deadlines

Task status lifecycle (Todo → In Progress → Completed)

👥 Team Management
Tenant Admins can:

Invite users

Remove users

Update user roles

🧑‍💻 Admin Dashboards
Super Admin Dashboard: Global tenant overview and monitoring

Tenant Dashboard: Organization-specific analytics and insights

🎨 Responsive UI
Clean and intuitive React-based dashboard

Mobile-friendly layout using Flexbox and Grid

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

Alpine Linux containers

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
Step 1: Clone Repository
bash
Copy code
git clone <your-repo-url>
cd Multi-Tenant-SaaS-Platform
Step 2: Configure Environment Variables
Create a .env file in the root directory (or verify values in docker-compose.yml):

env
Copy code
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=saas_db
Step 3: Run Application
bash
Copy code
docker-compose up -d --build
Step 4: Access Application
Frontend: http://localhost:3000

Backend Health Check: http://localhost:5000/api/health

✔ Database migrations and seed data are executed automatically

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

API Documentation
Base URL (Local): http://localhost:5000/api

All protected endpoints require the following header:

makefile
Copy code
Authorization: Bearer <JWT_TOKEN>
Token expiry duration: 24 hours

Seeded Test Credentials
Super Admin
Email: superadmin@system.com

Password: Admin@123

Tenant Admin (Demo Company)
Email: admin@demo.com

Password: Admin@123

Subdomain: demo

Future Enhancements
OAuth authentication (Google / GitHub)

Subscription and billing integration (Stripe)

Audit logs and activity tracking

Advanced role-based permission management

Microservices-based scalability

Project Purpose
This project is developed for portfolio presentation, technical interviews, and real-world SaaS architecture demonstration, highlighting backend security, scalability, and full-stack engineering practices.

Key Takeaway
The platform demonstrates how a single SaaS application can securely serve multiple organizations while maintaining strict data isolation, role-based security, and scalable architecture.

