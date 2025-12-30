# Multi-Tenant SaaS Platform

## Overview

A **production-grade B2B Multi-Tenant SaaS Platform** designed to demonstrate real-world system design principles such as **multi-tenancy**, **strict data isolation**, and **role-based access control (RBAC)**. Each organization (tenant) operates within a fully isolated workspace while sharing a single application infrastructure.

This project mirrors how modern SaaS products like **Jira, Notion, and Asana** securely manage multiple organizations within one system.

---

## Core Concepts Demonstrated

- Multi-Tenancy with Data Isolation using `tenant_id`
- Role-Based Access Control (RBAC)
- JWT Authentication & Authorization
- Secure REST APIs with validation and access guards
- Scalable SaaS Architecture suitable for enterprise systems

---

## User Roles & Permissions

### 1. Super Admin (System Level)

- Manage all registered tenants
- Monitor system-wide usage
- No access to tenant-level operational data

### 2. Tenant Admin (Organization Level)

- Manage users within the organization
- Assign roles and permissions
- Create and manage projects and tasks

### 3. Standard User

- Access only assigned projects
- Create and update tasks
- No access to user or organization management

---

## Key Features

### 🔐 Security & Authentication

- JWT-based authentication
- Password hashing using **Bcrypt**
- Secure CORS configuration
- Middleware-based role and tenant validation

### 🏢 Multi-Tenancy Architecture

- Centralized database with logical isolation
- All entities scoped by `tenant_id`
- Protection against cross-tenant data access

### 📊 Project & Task Management

- Tenant-specific projects
- Task assignment with priorities and deadlines
- Task lifecycle: **Todo → In Progress → Completed**

### 👥 Team Management

Tenant Admins can:
- Invite users
- Remove users
- Change user roles

### 🧑‍💻 Admin Dashboards

- **Super Admin Dashboard:** Global tenant overview
- **Tenant Dashboard:** Organization-specific insights

### 🎨 Responsive UI

- Clean and intuitive React dashboard
- Mobile-friendly layout using Flexbox & Grid

---

## Technology Stack

### Frontend
- React.js (v18)
- React Router DOM (v6)
- Context API for state management
- Axios for API communication
- CSS3 (Flexbox & Grid)

### Backend
- Node.js (v18)
- Express.js
- Prisma ORM
- JWT Authentication
- Express Validator

### Database & DevOps
- PostgreSQL (v15)
- Docker & Docker Compose
- Alpine Linux containers

---

## System Architecture

Client (React)
→ JWT Secured REST APIs
→ Node.js + Express
→ Prisma ORM
→ PostgreSQL (tenant_id scoped data)

---

## Installation & Setup

### Prerequisites
- Docker & Docker Compose (Recommended)
- Node.js v18+
- PostgreSQL (Local setup)

---

## Method 1: Docker Setup (Recommended)

### Step 1: Clone Repository
git clone <your-repo-url>
cd Multi-Tenant-SaaS-Platform

### Step 2: Configure Environment Variables

POSTGRES_USER=postgres  
POSTGRES_PASSWORD=postgres  
POSTGRES_DB=saas_db  

### Step 3: Run Application
docker-compose up -d --build

### Step 4: Access Application
Frontend: http://localhost:3000  
Backend Health: http://localhost:5000/api/health  

✔ Migrations and seed data run automatically

---

## Method 2: Local Development Setup

### Backend
cd backend  
npm install  
cp .env.example .env  
npx prisma migrate dev --name init  
npm run seed  
npm start  

### Frontend
cd frontend  
npm install  
npm start  

---

## Environment Variables

PORT – Backend server port (5000)  
DATABASE_URL – PostgreSQL connection string  
JWT_SECRET – JWT signing key  
FRONTEND_URL – http://localhost:3000  

---

## API Documentation

Base URL: http://localhost:5000/api  

Authorization Header:
Authorization: Bearer <JWT_TOKEN>

Token expiry: 24 hours

---

## Authentication

### Register Tenant
POST /auth/register-tenant

tenantName: Acme Corp  
subdomain: acme  
adminEmail: admin@acme.com  
password: SecurePassword123  

### Login
POST /auth/login

### Get Current User
GET /auth/me

---

## Tenant Management (Super Admin)

GET /tenants  
GET /tenants/:id  
PUT /tenants/:id  

---

## User Management (Tenant Admin)

GET /tenants/:tenantId/users  
POST /tenants/:tenantId/users  
PUT /users/:id  
DELETE /users/:id  

---

## Project Management

GET /projects  
POST /projects  
GET /projects/:id  
PUT /projects/:id  

---

## Task Management

GET /projects/:projectId/tasks  
POST /projects/:projectId/tasks  
PATCH /tasks/:id/status  
PUT /tasks/:id  

---

## Seeded Test Credentials

Super Admin  
Email: superadmin@system.com  
Password: Admin@123  

Tenant Admin (Demo)  
Email: admin@demo.com  
Password: Admin@123  
Subdomain: demo  

---

## Future Enhancements

- OAuth (Google / GitHub)
- Subscription & Billing (Stripe)
- Audit Logs & Activity Tracking
- Advanced RBAC permissions
- Microservices-based scaling

---

## Project Purpose

Built for **portfolio, interviews, and real-world SaaS architecture demonstration**, showcasing secure backend design, scalable architecture, and full-stack engineering skills.

---

## Frontend (React)

- Authentication & session handling
- Role-based route protection
- Tenant-aware API communication
- Project & task management UI
- Responsive dashboards

---

## Notes

- CRA boilerplate removed for clarity
- Documentation focused on real-world SaaS implementation
