Multi-Tenant SaaS Platform – API Documentation (Revised)
Authentication & Security
Authentication: JWT (Bearer Token)

Header Format:
Authorization: Bearer <JWT_TOKEN>

Token Expiry: 24 hours

Base URL (Local):
http://localhost:5000/api

1. System
1.1 Health Check
Verifies API server and database connectivity.

Endpoint: GET /health

Access: Public

Response (200 OK)
json
Copy code
{
  "status": "ok",
  "database": "connected"
}
2. Authentication Module
2.1 Register Tenant (Sign Up)
Creates a new Tenant (Organization) and its first Tenant Admin.

Endpoint: POST /auth/register-tenant

Access: Public

Request Body
json
Copy code
{
  "tenantName": "Acme Corp",
  "subdomain": "acme",
  "adminEmail": "admin@acme.com",
  "password": "SecurePassword123"
}
Response (201 Created)
json
Copy code
{
  "message": "Tenant registered successfully",
  "tenantId": "uuid-string"
}
2.2 Login
Authenticates a user and returns a JWT token.

Endpoint: POST /auth/login

Access: Public

Request Body
json
Copy code
{
  "email": "admin@acme.com",
  "password": "SecurePassword123"
}
Response (200 OK)
json
Copy code
{
  "token": "eyJhbGciOiJIUzI1NiIsIn...",
  "user": {
    "id": "uuid",
    "email": "admin@acme.com",
    "role": "tenant_admin",
    "tenantId": "uuid"
  }
}
2.3 Get Current User
Returns details of the logged-in user.

Endpoint: GET /auth/me

Access: Protected (All Roles)

Response (200 OK)
json
Copy code
{
  "user": {
    "id": "uuid",
    "fullName": "John Doe",
    "email": "john@acme.com",
    "role": "user",
    "tenantId": "uuid"
  }
}
3. Tenant Management (Super Admin Only)
3.1 List All Tenants
Endpoint: GET /tenants

Access: Super Admin

Response (200 OK)
json
Copy code
{
  "status": "success",
  "results": 2,
  "data": {
    "tenants": [
      { "id": "1", "name": "Acme Corp", "subdomain": "acme" },
      { "id": "2", "name": "Beta Inc", "subdomain": "beta" }
    ]
  }
}
3.2 Get Tenant Details
Endpoint: GET /tenants/:id

Access: Super Admin

Response (200 OK)
json
Copy code
{
  "id": "uuid",
  "name": "Acme Corp",
  "subdomain": "acme",
  "status": "active"
}
3.3 Update Tenant
Endpoint: PUT /tenants/:id

Access: Super Admin

Request Body
json
Copy code
{
  "name": "Acme Global",
  "status": "inactive"
}
4. User Management (Tenant Admin)
4.1 List Users
Endpoint: GET /tenants/:tenantId/users

Access: Tenant Admin

Response (200 OK)
json
Copy code
{
  "data": {
    "users": [
      {
        "id": "u1",
        "fullName": "Alice",
        "email": "alice@acme.com",
        "role": "user"
      }
    ]
  }
}
4.2 Create User
Endpoint: POST /tenants/:tenantId/users

Access: Tenant Admin

Request Body
json
Copy code
{
  "email": "alice@acme.com",
  "password": "Password123",
  "fullName": "Alice Smith",
  "role": "user"
}
4.3 Update User
Endpoint: PUT /users/:id

Access: Tenant Admin

Request Body
json
Copy code
{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}
4.4 Delete User
Endpoint: DELETE /users/:id

Access: Tenant Admin

5. Project Management
5.1 List Projects
Endpoint: GET /projects

Access: User / Admin

Response (200 OK)
json
Copy code
{
  "data": {
    "projects": [
      {
        "id": "p1",
        "title": "Website Redesign",
        "status": "active"
      }
    ]
  }
}
5.2 Create Project
Endpoint: POST /projects

Access: Admin

Request Body
json
Copy code
{
  "title": "Q3 Marketing Campaign",
  "description": "Planning for Q3",
  "status": "active"
}
5.3 Get Project Details
Endpoint: GET /projects/:id

Access: User / Admin

5.4 Update Project
Endpoint: PUT /projects/:id

Access: Admin

Request Body
json
Copy code
{
  "status": "completed"
}
Note:
Project deletion uses: DELETE /projects/:id

6. Task Management
6.1 List Tasks
Endpoint: GET /projects/:projectId/tasks

Access: User / Admin

Response (200 OK)
json
Copy code
{
  "data": {
    "tasks": [
      {
        "id": "t1",
        "title": "Draft content",
        "status": "TODO"
      }
    ]
  }
}
6.2 Create Task
Endpoint: POST /projects/:projectId/tasks

Access: Admin

Request Body
json
Copy code
{
  "title": "Fix Header Bug",
  "description": "CSS issue on mobile",
  "priority": "HIGH",
  "dueDate": "2023-12-31"
}
6.3 Update Task Status
Endpoint: PATCH /tasks/:id/status

Access: User / Admin

Request Body
json
Copy code
{
  "status": "IN_PROGRESS"
}
6.4 Update Task Details
Endpoint: PUT /tasks/:id

Access: Admin

Request Body
json
Copy code
{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}
