Multi-Tenant SaaS Platform – API Documentation (Updated)
Authentication & Security
Authentication Method: JWT (Bearer Token)

Authorization Header Format:
Authorization: Bearer <JWT_TOKEN>

Token Validity: 24 hours

Base URL (Local Environment):
http://localhost:5000/api

1. System Module
1.1 Health Check
Checks whether the API server is running and verifies database connectivity.

Endpoint: GET /health

Access Level: Public

Successful Response (200 OK)

json
Copy code
{
  "status": "ok",
  "database": "connected"
}
2. Authentication Module
2.1 Register Tenant (Sign Up)
Creates a new Tenant (Organization) along with its initial Tenant Admin account.

Endpoint: POST /auth/register-tenant

Access Level: Public

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
  "message": "Tenant created successfully",
  "tenantId": "uuid-string"
}
2.2 Login
Authenticates a registered user and returns a JWT access token.

Endpoint: POST /auth/login

Access Level: Public

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
Retrieves details of the currently authenticated user.

Endpoint: GET /auth/me

Access Level: Protected (All Roles)

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
3.1 Retrieve All Tenants
Endpoint: GET /tenants

Access Level: Super Admin

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
3.2 Get Tenant Information
Endpoint: GET /tenants/:id

Access Level: Super Admin

Response (200 OK)

json
Copy code
{
  "id": "uuid",
  "name": "Acme Corp",
  "subdomain": "acme",
  "status": "active"
}
3.3 Modify Tenant Details
Endpoint: PUT /tenants/:id

Access Level: Super Admin

Request Body

json
Copy code
{
  "name": "Acme Global",
  "status": "inactive"
}
4. User Management (Tenant Admin)
4.1 Get Tenant Users
Endpoint: GET /tenants/:tenantId/users

Access Level: Tenant Admin

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
4.2 Add New User
Endpoint: POST /tenants/:tenantId/users

Access Level: Tenant Admin

Request Body

json
Copy code
{
  "email": "alice@acme.com",
  "password": "Password123",
  "fullName": "Alice Smith",
  "role": "user"
}
4.3 Update User Information
Endpoint: PUT /users/:id

Access Level: Tenant Admin

Request Body

json
Copy code
{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}
4.4 Remove User
Endpoint: DELETE /users/:id

Access Level: Tenant Admin

5. Project Management
5.1 Get All Projects
Endpoint: GET /projects

Access Level: User / Admin

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

Access Level: Admin

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

Access Level: User / Admin

5.4 Update Project
Endpoint: PUT /projects/:id

Access Level: Admin

Request Body

json
Copy code
{
  "status": "completed"
}
Note: Projects can be deleted using
DELETE /projects/:id

6. Task Management
6.1 Retrieve Project Tasks
Endpoint: GET /projects/:projectId/tasks

Access Level: User / Admin

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

Access Level: Admin

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

Access Level: User / Admin

Request Body

json
Copy code
{
  "status": "IN_PROGRESS"
}
6.4 Update Task Details
Endpoint: PUT /tasks/:id

Access Level: Admin

Request Body

json
Copy code
{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}
