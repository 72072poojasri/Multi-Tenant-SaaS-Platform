Technical Specification
Project Name: Multi-Tenant SaaS Project Management System
Date: October 26, 2025
Version: 1.0
Author: AWS Student / Lead Developer

1. Project Structure
The application follows a monorepo architecture that contains both the Backend API and the Frontend React application, with services orchestrated using Docker Compose at the root level.

This structure ensures:

Clear separation of concerns

Simplified local and production deployment

Easier maintenance and scalability

1.1 Root Directory Layout
text
Copy code
/Multi-Tenant-SaaS-Platform
├── docker-compose.yml        # Service orchestration (DB, Backend, Frontend)
├── submission.json           # Credentials for automated evaluation
├── README.md                 # Project overview and usage instructions
├── .gitignore                # Git ignore rules
├── docs/                     # PRD, Architecture, Research documents
├── backend/                  # Node.js / Express API
└── frontend/                 # React (Vite) application
1.2 Backend Structure (/backend)
The backend is implemented using Node.js, Express, and Prisma ORM, following a modular and scalable architecture suitable for SaaS systems.

text
Copy code
backend/
├── .env.example              # Environment variable template
├── Dockerfile                # Backend container configuration
├── package.json              # Backend dependencies and scripts
├── prisma/
│   ├── schema.prisma         # Database schema definition
│   └── migrations/           # Prisma migration history
├── seeds/
│   └── seed.js               # Initial database seed script
└── src/
    ├── controllers/          # Business logic (Auth, Tenant, Project, Task)
    ├── middleware/           # Auth, RBAC, Error handling, Validation
    ├── routes/               # REST API route definitions
    └── utils/                # Utility helpers (JWT, hashing, constants)
1.3 Frontend Structure (/frontend)
The frontend is built using React with the Vite build tool to enable fast development and optimized production builds.

text
Copy code
frontend/
├── Dockerfile                # Frontend container configuration
├── package.json              # Frontend dependencies
├── public/                   # Static assets (index.html, icons)
└── src/
    ├── context/              # Global state (AuthContext)
    ├── pages/                # Page components (Login, Register, Dashboard)
    ├── App.js                # Root component and routing
    └── index.js              # Application entry point
2. Development & Setup Guide
2.1 Prerequisites
Ensure the following tools are installed before starting development:

Docker Desktop — Version 4.0+
(Required to run the full stack locally)

Node.js — Version 18 LTS
(Needed for local development, tooling, and IntelliSense)

Git — Version 2.0+

2.2 Environment Configuration
Create a .env file inside the backend/ directory.
(Default values are also provided via docker-compose.yml.)

Required Environment Variables
ini
Copy code
# Server Configuration
PORT=5000
NODE_ENV=development

# Database Connection (Docker Internal Network)
DATABASE_URL="postgresql://postgres:postgres@database:5432/saas_db?schema=public"

# Authentication
JWT_SECRET="your_secure_random_secret_key_min_32_chars"
JWT_EXPIRES_IN="24h"

# CORS
FRONTEND_URL="http://localhost:3000"
2.3 Installation Steps
Clone the Repository
bash
Copy code
git clone <repository_url>
cd Multi-Tenant-SaaS-Platform
Install Dependencies (Optional)
Dependency installation is optional when using Docker, but recommended for local development and editor support.

Backend
bash
Copy code
cd backend
npm install
Frontend
bash
Copy code
cd ../frontend
npm install
2.4 Running the Application (Docker – Recommended)
The entire stack is designed to run using Docker Compose, which automatically configures networking between services.

Build and Start Services
From the root directory, run:

bash
Copy code
docker-compose up -d --build
Verify Running Containers
bash
Copy code
docker-compose ps
Expected services:

database

backend

frontend

Automatic Initialization
On startup, the backend container automatically performs:

Prisma migrations (prisma migrate deploy)

Database seeding (node seeds/seed.js)

⏳ Allow 30–60 seconds for database initialization and seed completion.

2.5 Accessing the Application
Frontend UI: http://localhost:3000

Backend API: http://localhost:5000

Health Check: http://localhost:5000/api/health

2.6 Testing & Verification
Manual API Testing (Postman / Curl)
Use credentials provided in submission.json

Validate authentication and role-based access

Confirm tenant isolation

Health Check Example
bash
Copy code
curl http://localhost:5000/api/health
Expected Response:

json
Copy code
{
  "status": "ok",
  "database": "connected"
}
Database Inspection
To inspect database tables or seeded data:

bash
Copy code
docker exec -it database psql -U postgres -d saas_db
Example query:

sql
Copy code
SELECT * FROM tenants;
