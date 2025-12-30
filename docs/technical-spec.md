# **Multi-Tenant SaaS Platform**

## **Overview**

A production-grade **B2B Multi-Tenant SaaS Platform** designed to showcase real-world system design concepts such as **Multi-Tenancy**, **Strict Data Isolation**, and **Role-Based Access Control (RBAC)**. Each organization (Tenant) operates within a completely isolated workspace while sharing the same application infrastructure.

This project demonstrates how modern SaaS products (e.g., Jira, Notion, Asana) manage multiple organizations securely within a single system.

---

## **Core Concepts Demonstrated**

* **Multi-Tenancy with Isolation** using `tenant_id` at the database level
* **RBAC (Role-Based Access Control)** across system roles
* **JWT Authentication & Authorization**
* **Secure Backend APIs** with validation and access guards
* **Scalable SaaS Architecture** suitable for enterprise use

---

## **User Roles & Permissions**

### **1. Super Admin (System Level)**

* Manage all registered tenants
* Monitor system-wide usage
* No access to tenant-specific operational data

### **2. Tenant Admin (Organization Level)**

* Manage organization users
* Assign roles and permissions
* Create and manage projects and tasks

### **3. Standard User**

* Access only assigned projects
* Create and update tasks
* Cannot manage users or organization settings

---

## **Key Features**

### **🔐 Security & Authentication**

* JWT-based authentication
* Password hashing using **Bcrypt**
* Secure CORS configuration
* Middleware-based role and tenant validation

### **🏢 Multi-Tenancy Architecture**

* Centralized database with logical isolation
* Every entity scoped using `tenant_id`
* Protection against cross-tenant data leaks

### **📊 Project & Task Management**

* Tenant-specific projects
* Task assignment with priorities and deadlines
* Task status lifecycle (Todo → In Progress → Completed)

### **👥 Team Management**

* Tenant Admins can:
  * Invite users
  * Remove users
  * Change roles

### **🧑‍💻 Admin Dashboards**

* **Super Admin Dashboard**: Global tenant overview
* **Tenant Dashboard**: Organization-specific analytics

### **🎨 Responsive UI**

* Clean and intuitive React dashboard
* Mobile-friendly layout using Flexbox & Grid

---

## **Technology Stack**

### **Frontend**
* **React.js (v18)**
* React Router DOM (v6)
* Context API for state management
* Axios for API communication
* CSS3 (Flexbox & Grid)

### **Backend**
* **Node.js (v18)**
* Express.js
* Prisma ORM
* JWT Authentication
* Express Validator

### **Database & DevOps**
* PostgreSQL (v15)
* Docker & Docker Compose
* Alpine Linux containers

---

## **System Architecture**

Client (React)  
→ REST APIs (JWT Secured)  
→ Node.js + Express  
→ Prisma ORM  
→ PostgreSQL (tenant_id scoped data)

---

## **Installation & Setup**

### **Prerequisites**
* Docker & Docker Compose (Recommended)
* Node.js v18+
* PostgreSQL (Local setup only)

---

## **Docker Setup (Recommended)**

```bash
git clone <your-repo-url>
cd Multi-Tenant-SaaS-PlatformFrontend: http://localhost:3000
Backend Health: http://localhost:5000/api/health

API Documentation
Base URL: http://localhost:5000/api

makefile
Copy code
Authorization: Bearer <JWT_TOKEN>
Token expiry: 24 hours

Seeded Test Credentials
Super Admin
Email: superadmin@system.com
Password: Admin@123

Tenant Admin (Demo)
Email: admin@demo.com
Password: Admin@123
Subdomain: demo

System Architecture Document
Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Author: AWS Student / Lead Developer
Status: Approved for Implementation
Revision: v1.1

Multi-Tenancy Architecture Decision
Final Choice: Shared Database + Shared Schema
Reason: Best balance of cost, scalability, Prisma compatibility, and MVP speed.

Tenant isolation is enforced via:

Middleware

JWT tenant_id

ORM-level query scoping

Security Architecture
JWT validation on every request

RBAC enforced before business logic

Bcrypt password hashing (10+ salt rounds)

Strict CORS + rate limiting

Prisma parameterized queries

Data Isolation Pattern
sql
Copy code
SELECT *
FROM tasks
WHERE id = :taskId
AND tenant_id = :jwtTenantId;
Cross-tenant data returns 404 Not Found.

Request Lifecycle
mermaid
Copy code
sequenceDiagram
    autonumber
    participant User as Client
    participant API as Express API
    participant Auth as Auth Middleware
    participant RBAC as RBAC Guard
    participant DB as PostgreSQL

    User->>API: Login
    API-->>User: JWT
    User->>API: Authenticated Request
    API->>Auth: Verify JWT
    API->>RBAC: Check Role
    API->>DB: Tenant-scoped Query
    DB-->>API: Data
    API-->>User: Response
PRD Summary
User Personas
Super Admin

Tenant Admin

End User

Functional Requirements
Tenant registration

JWT authentication

RBAC

Project & task lifecycle

Non-Functional Requirements
Performance < 200ms

Horizontal scalability

Docker-based deployment

Responsive UI

Technical Highlights
Production-grade multi-tenancy

Secure-by-default architecture

Monorepo with Docker orchestration

Interview & enterprise ready


docker-compose up -d --build
