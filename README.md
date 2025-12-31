# Multi-Tenant SaaS Platform

## Overview
A production-grade B2B Multi-Tenant SaaS Application built to demonstrate real-world system design concepts such as Multi-Tenancy, Strict Data Isolation, and Role-Based Access Control (RBAC).

The platform enables multiple organizations (Tenants) to operate in fully isolated workspaces while sharing the same application infrastructure. Each tenant can securely manage its own users, projects, and tasks.

The system enforces a strict hierarchy:
- Super Admin: System-level control across all tenants
- Tenant Admin: Organization-level management
- Standard User: Task and project execution

---

## Key Features

- Multi-Tenant Architecture
  - Tenant-based data isolation using tenant_id
  - Prevents data leakage between organizations

- Secure Authentication
  - JWT-based authentication
  - Password hashing using Bcrypt
  - Token-based session management

- Role-Based Access Control (RBAC)
  - Clear separation of permissions
  - Middleware-enforced authorization

- Project Management
  - Create and manage tenant-specific projects
  - Track progress and status

- Task Orchestration
  - Assign tasks with priorities and deadlines
  - Update and monitor task status

- Team Management
  - Invite, remove, and manage users
  - Role assignment within tenants

- Super Admin Dashboard
  - Global visibility of all tenants
  - No access to tenant operational data

- Responsive UI
  - Modern dashboard built with React
  - Mobile-friendly layout

---

## Technology Stack

Frontend:
- React.js (v18)
- React Context API
- React Router DOM (v6)
- Axios
- CSS3 (Flexbox & Grid)

Backend:
- Node.js (v18)
- Express.js
- Prisma ORM
- JWT, Bcrypt, CORS
- Express Validator

Database & DevOps:
- PostgreSQL (v15)
- Docker & Docker Compose
- Linux (Alpine containers)

---

## Architecture Overview
The application follows a Client–Server architecture. The React frontend communicates with the Node.js/Express backend through REST APIs. The backend connects to a PostgreSQL database where all queries are strictly scoped using tenant_id to ensure complete data isolation.

---

## Installation & Setup

Prerequisites:
- Docker & Docker Compose (Recommended)
- Node.js v18+
- PostgreSQL (for local setup)

---

### Method 1: Docker Setup (Recommended)

1. Clone the Repository
   git clone <your-repo-url>
   cd Multi-Tenant-SaaS-Platform

2. Configure Environment Variables
   Create a .env file in the root directory:
   POSTGRES_USER=postgres
   POSTGRES_PASSWORD=postgres
   POSTGRES_DB=saas_db

3. Start the Application
   docker-compose up -d --build

4. Access the Application
   Frontend: http://localhost:3000
   Backend Health: http://localhost:5000/api/health

Note: Database migrations and seed data run automatically on startup.

---

### Method 2: Local Development (Manual)

1. Database
   Ensure PostgreSQL is running on port 5432

2. Backend Setup
   cd backend
   npm install
   cp .env.example .env
   Update database credentials in .env
   npx prisma migrate dev --name init
   npm run seed
   npm start

3. Frontend Setup
   cd frontend
   npm install
   npm start

---

## Environment Variables

PORT=5000
DATABASE_URL=postgresql://...@database:5432/saas_db
JWT_SECRET=your_secure_secret
FRONTEND_URL=http://localhost:3000

---

## API Endpoints

Authentication:
- POST /api/auth/register-tenant
- POST /api/auth/login

Projects & Tasks:
- GET /api/projects
- POST /api/projects
- POST /api/projects/:id/tasks
- PATCH /api/tasks/:id/status

Management:
- GET /api/tenants (Super Admin)
- GET /api/tenants/:id/users (Tenant Admin)
- POST /api/tenants/:id/users (Tenant Admin)

---

## Testing Credentials (Seed Data)

Super Admin:
Email: superadmin@system.com
Password: Admin@123

Tenant Admin (Demo Organization):
Email: admin@demo.com
Password: Admin@123
Subdomain: demo
