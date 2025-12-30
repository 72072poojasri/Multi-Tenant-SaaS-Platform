# Multi-Tenant SaaS Platform

## Overview

A production-grade **B2B Multi-Tenant SaaS Platform** designed to showcase real-world system design concepts such as **Multi-Tenancy**, **Strict Data Isolation**, and **Role-Based Access Control (RBAC)**. Each organization (Tenant) operates within a completely isolated workspace while sharing the same application infrastructure.

This project demonstrates how modern SaaS products (e.g., Jira, Notion, Asana) manage multiple organizations securely within a single system.

---

## Core Concepts Demonstrated

* **Multi-Tenancy with Isolation** using `tenant_id` at the database level
* **RBAC (Role-Based Access Control)** across system roles
* **JWT Authentication & Authorization**
* **Secure Backend APIs** with validation and access guards
* **Scalable SaaS Architecture** suitable for enterprise use

---

## User Roles & Permissions

### 1. Super Admin (System Level)

* Manage all registered tenants
* Monitor system-wide usage
* No access to tenant-specific operational data

### 2. Tenant Admin (Organization Level)

* Manage organization users
* Assign roles and permissions
* Create and manage projects and tasks

### 3. Standard User

* Access only assigned projects
* Create and update tasks
* Cannot manage users or organization settings

---

## Key Features

### 🔐 Security & Authentication

* JWT-based authentication
* Password hashing using **Bcrypt**
* Secure CORS configuration
* Middleware-based role and tenant validation

### 🏢 Multi-Tenancy Architecture

* Centralized database with logical isolation
* Every entity scoped using `tenant_id`
* Protection against cross-tenant data leaks

### 📊 Project & Task Management

* Tenant-specific projects
* Task assignment with priorities and deadlines
* Task status lifecycle (Todo → In Progress → Completed)

### 👥 Team Management

* Tenant Admins can:
  * Invite users
  * Remove users
  * Change roles

### 🧑‍💻 Admin Dashboards

* **Super Admin Dashboard**: Global tenant overview
* **Tenant Dashboard**: Organization-specific analytics

### 🎨 Responsive UI

* Clean and intuitive React dashboard
* Mobile-friendly layout using Flexbox & Grid

---

## Technology Stack

### Frontend

* React.js (v18)
* React Router DOM (v6)
* Context API for state management
* Axios for API communication
* CSS3 (Flexbox & Grid)

### Backend

* Node.js (v18)
* Express.js
* Prisma ORM
* JWT Authentication
* Express Validator

### Database & DevOps

* PostgreSQL (v15)
* Docker & Docker Compose
* Alpine Linux containers

---

## System Architecture

Client (React)  
→ REST APIs (JWT Secured)  
→ Node.js + Express  
→ Prisma ORM  
→ PostgreSQL (tenant_id scoped data)

---

## Installation & Setup

### Prerequisites

* Docker & Docker Compose (Recommended)
* Node.js v18+
* PostgreSQL (Local setup only)

---

## Method 1: Docker Setup (Recommended)

### Clone Repository
git clone <your-repo-url>  
cd Multi-Tenant-SaaS-Platform

### Environment Variables
POSTGRES_USER=postgres  
POSTGRES_PASSWORD=postgres  
POSTGRES_DB=saas_db  

### Run Application
docker-compose up -d --build

### Access Application
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

## API Documentation

Base URL: http://localhost:5000/api  

Authorization Header:  
Authorization: Bearer <JWT_TOKEN>  

Token expiry: 24 hours

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

* OAuth (Google / GitHub)
* Subscription & Billing (Stripe)
* Audit Logs & Activity Tracking
* Advanced Role Permissions
* Microservices-based scaling

---

## Project Purpose

Built for **portfolio, interviews, and real-world SaaS architecture demonstration**, showcasing backend security, scalable design, and full-stack engineering skills.

---

## Frontend (React)

* Authentication & session handling
* Role-based route protection (RBAC)
* Tenant-aware API communication
* Project & task management UI
* Responsive dashboard layouts

---

## System Architecture Document

Project: Multi-Tenant SaaS Project Management System  
Version: 1.0  
Author: Lead Developer  

---

## Architecture Overview

The system follows a **containerized three-tier architecture** designed for scalability, security, and strict tenant isolation. All services are orchestrated using **Docker Compose**.

---

## High-Level Architecture

```mermaid
graph LR
    User[Client Browser] --> Frontend[React Frontend Container]
    Frontend --> Backend[Node.js API Container]
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
