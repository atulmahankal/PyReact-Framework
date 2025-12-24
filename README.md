# SampleApp

A full-stack application template with authentication, RBAC, admin panel, and API Gateway architecture.

## Features

### Authentication & Authorization

- User signup/login with JWT authentication
- Password reset via email
- Role-Based Access Control (RBAC)
- Permission-based access management

### User Management

- Profile management (update details, change password)
- Admin user management panel
- User activation/deactivation
- Role assignment

### Admin Panel

- Dashboard with dark sidebar theme
- User management (view, edit, activate/deactivate)
- Role management (create, edit, delete, copy permissions)
- Settings management

### Roles & Permissions

- **Super Admin**: Full system control (CLI only, hidden from UI)
- **Administrator**: User and role management
- **Standard User**: Basic access

### Logging

- Daily rotating log files with auto-cleanup
- Separate error logs (`error-YYYY-MM-DD.log`)
- Configurable retention period
- JSON format support for production

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Browser   │────▶│   Nginx     │────▶│  Frontend   │
│             │     │ (Gateway)   │     │  (Next.js)  │
└─────────────┘     └──────┬──────┘     └─────────────┘
                          │
                          │ /api/*
                          ▼
                   ┌─────────────┐     ┌─────────────┐
                   │   Backend   │────▶│ PostgreSQL  │
                   │  (FastAPI)  │     │   Database  │
                   └──────┬──────┘     └─────────────┘
                          │
                          ▼
                   ┌─────────────┐
                   │    Redis    │
                   │ (Cache/Queue)│
                   └─────────────┘
```

## Quick Start

### Prerequisites

- Docker & Docker Compose

### 1. Clone and Configure

```bash
# Copy environment files
cp .env.example .env
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env

# Edit files if needed (defaults work for development)
```

### 2. Start Development Environment

```bash
docker compose up -d
```

This starts:

| Service    | URL                   | Description      |
| ---------- | --------------------- | ---------------- |
| Nginx      | http://localhost      | API Gateway      |
| Backend    | http://localhost:8000 | FastAPI Backend  |
| Frontend   | http://localhost:3000 | Next.js Frontend |
| PostgreSQL | localhost:5432        | Database         |
| Redis      | localhost:6379        | Cache & Queue    |
| Mailpit    | http://localhost:8025 | Email Testing UI |

### 3. Access the Application

- **Frontend**: http://localhost/ (via nginx)
- **API Docs**: http://localhost:8000/docs
- **Email Inbox**: http://localhost:8025

**Default Super Admin:** (if not configured in ./.env)

- Email: `admin@example.com`
- Password: `Admin@123`

> Note: Migrations and seed data run automatically on startup.

## Email Testing with Mailpit

All emails sent by the application are captured by Mailpit in development.

- **Web UI**: http://localhost:8025
- **SMTP**: localhost:1025

Test the password reset flow:

1. Go to http://localhost:3000/forgot-password
2. Enter an email address
3. Check Mailpit at http://localhost:8025 for the reset email

## Project Structure

```
BaseApp/
├── .env.example           # Docker Compose ports config
├── docker-compose.yml     # Container orchestration
├── nginx.conf             # API Gateway configuration
├── README.md
├── CLAUDE.md              # Claude Code context
├── PROJECT_CONTEXT.md     # Project documentation
│
├── API/                   # REST Client test files
│   ├── auth.rest
│   ├── admin-users.rest
│   ├── admin-rbac.rest
│   └── admin-config.rest
│
├── backend/               # FastAPI Backend
│   ├── .env.example
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── logs/              # Application logs (git-ignored)
│   │   ├── app-YYYY-MM-DD.log
│   │   └── error-YYYY-MM-DD.log
│   ├── app/
│   │   ├── main.py        # FastAPI app entry
│   │   ├── config.py      # Settings (multi-DB support)
│   │   ├── database.py    # Async SQLAlchemy
│   │   ├── startup.py     # Auto-seed on startup
│   │   ├── api/           # API routes
│   │   │   ├── auth.py
│   │   │   ├── users.py
│   │   │   └── admin/
│   │   ├── models/        # SQLAlchemy models
│   │   ├── tasks/         # Celery background tasks
│   │   └── utils/
│   │       ├── logger.py  # Logging utility
│   │       ├── encryption.py
│   │       └── permissions.py
│   └── alembic/           # Database migrations
│
└── frontend/              # Next.js 16 Frontend
    ├── .env.example
    ├── Dockerfile
    ├── package.json
    ├── logs/              # Application logs (git-ignored)
    │   ├── app-YYYY-MM-DD.log
    │   └── error-YYYY-MM-DD.log
    └── src/
        ├── app/           # App Router pages
        │   ├── login/
        │   ├── signup/
        │   ├── dashboard/
        │   ├── admin/
        │   └── api/log/   # Client error logging endpoint
        ├── components/
        └── lib/
            ├── api.ts
            └── logger.ts  # Logging utility
```

## API Endpoints

For quick API testing in VSCode, use the [REST Client extension](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Pre-configured request files are available in the [`API/`](API/) folder:

### Authentication ([`API/auth.rest`](API/auth.rest))

| Method | Endpoint           | Description               |
| ------ | ------------------ | ------------------------- |
| POST   | `/signup`          | Register new user         |
| POST   | `/login`           | Login (returns JWT)       |
| GET    | `/me`              | Get current user          |
| PATCH  | `/profile`         | Update profile            |
| POST   | `/change-password` | Change password           |
| POST   | `/forgot-password` | Request password reset    |
| POST   | `/reset-password`  | Reset password with token |

### Admin Users ([`API/admin-users.rest`](API/admin-users.rest))

| Method | Endpoint | Description      |
| ------ | -------- | ---------------- |
| GET    | `/`      | List all users   |
| GET    | `/{id}`  | Get user details |
| PATCH  | `/{id}`  | Update user      |

### Admin: Roles & permissions ([`API/admin-rbac.rest`](API/admin-rbac.rest))

| Method | Endpoint                      | Description                              |
| ------ | ----------------------------- | ---------------------------------------- |
| GET    | `/roles`                      | List roles                               |
| POST   | `/roles`                      | Create role (supports copy_from_role_id) |
| PATCH  | `/roles/{id}`                 | Update role                              |
| DELETE | `/roles/{id}`                 | Delete role                              |
| GET    | `/permissions`                | List permissions                         |
| PUT    | `/roles/{id}/permissions`     | Set role permissions                     |
| POST   | `/users/{id}/roles/{role_id}` | Assign role to user                      |
| DELETE | `/users/{id}/roles/{role_id}` | Remove role from user                    |

### Admin: Configuration/settings ([`API/admin-config.rest`](API/admin-config.rest))

| Method | Endpoint       | Description                    |
| ------ | -------------- | ------------------------------ |
| GET    | `/`            | List all config items          |
| GET    | `/categories`  | List all categories            |
| GET    | `/by-category` | Get config grouped by category |
| GET    | `/{key}`       | Get single config item         |
| PUT    | `/{key}`       | Update single config item      |
| PUT    | `/`            | Bulk update config items       |
| POST   | `/clear-cache` | Clear cache and seed defaults  |

## Development Commands

```bash
# View logs
docker compose logs -f nginx
docker compose logs -f base-app-backend
docker compose logs -f base-app-frontend

# Restart services
docker compose restart base-app-backend
docker compose restart base-app-frontend

# Run backend shell
docker compose exec base-app-backend bash

# Create new migration
docker compose exec base-app-backend alembic revision --autogenerate -m "description"

# Access database
docker compose exec base-app-db psql -U sampleapp -d sampleapp

# Create additional super admin
docker compose exec base-app-backend python -m app.create_admin

# Rebuild containers
docker compose build --no-cache
docker compose up -d
```

## Environment Variables

Configuration is split across three `.env` files:

### Root `.env` (Docker Compose Ports)

| Variable            | Description      | Default |
| ------------------- | ---------------- | ------- |
| `NGINX_PORT`        | API Gateway port | 80      |
| `BACKEND_PORT`      | Backend API port | 8000    |
| `FRONTEND_PORT`     | Frontend port    | 3000    |
| `DB_PORT`           | PostgreSQL port  | 5432    |
| `REDIS_PORT`        | Redis port       | 6379    |
| `MAILPIT_HTTP_PORT` | Mailpit UI port  | 8025    |

### Backend `.env`

| Variable              | Description            | Default                   |
| --------------------- | ---------------------- | ------------------------- |
| `DATABASE_DIALECT`    | DB type                | postgresql                |
| `DATABASE_USER`       | DB username            | sampleapp                 |
| `DATABASE_SECRET`     | DB password            | sampleapp                 |
| `SECRET_KEY`          | JWT signing key        | (generate for production) |
| `LOG_LEVEL`           | Logging level          | INFO                      |
| `LOG_RETENTION_DAYS`  | Log cleanup after days | 30                        |
| `SUPERADMIN_EMAIL`    | Initial admin email    | admin@example.com         |
| `SUPERADMIN_PASSWORD` | Initial admin password | Admin@123                 |

### Frontend `.env`

| Variable              | Description     | Default                 |
| --------------------- | --------------- | ----------------------- |
| `NEXT_PUBLIC_API_URL` | Backend API URL | http://localhost:80/api |
| `LOG_LEVEL`           | Logging level   | info                    |
| `LOG_RETENTION_DAYS`  | Log cleanup     | 30                      |

## Tech Stack

- **Backend**: Python 3.12, FastAPI, SQLAlchemy 2.0, Celery
- **Frontend**: Next.js 16, React 19, TypeScript, Tailwind CSS
- **Database**: PostgreSQL 17 (supports MySQL, MariaDB, SQLite)
- **Cache/Queue**: Redis 8
- **API Gateway**: Nginx
- **Email Testing**: Mailpit
- **Logging**: Pino (frontend), Python logging (backend)
- **Infrastructure**: Docker Compose

## Production Deployment

For production:

1. Generate a secure `SECRET_KEY`:

   ```bash
   openssl rand -hex 32
   ```

2. Configure real SMTP settings in `backend/.env`

3. Set `ENVIRONMENT=production` and `DEBUG=false`

4. Use proper database credentials

5. Configure CORS origins for your domain

6. Set `JSON_LOGS=true` for structured logging

## License

MIT
