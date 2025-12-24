# SampleApp - Project Context

## Project Overview

SampleApp is a Full-Stack Application Template with Authentication, RBAC, Admin Panel, and API Gateway architecture following the Database-per-Service microservices pattern.

## Architecture

```
                    ┌─────────────────────────────────────────┐
                    │              API Gateway                │
                    │               (Nginx)                   │
                    │  - Routes /api/* to Backend             │
                    │  - Routes /* to Frontend                │
                    │  - Security headers                     │
                    │  - WebSocket support                    │
                    └──────────────┬──────────────────────────┘
                                   │
            ┌──────────────────────┼──────────────────────┐
            │                      │                      │
            ▼                      ▼                      ▼
    ┌───────────────┐    ┌─────────────────┐    ┌───────────────┐
    │   Frontend    │    │    Backend      │    │    Future     │
    │  (Next.js 16) │    │   (FastAPI)     │    │  Microservice │
    │               │    │                 │    │               │
    │  - React 19   │    │  - Async I/O    │    │  - Own DB     │
    │  - TypeScript │    │  - SQLAlchemy   │    │  - Own API    │
    │  - Tailwind   │    │  - Celery       │    │               │
    └───────────────┘    └────────┬────────┘    └───────────────┘
                                  │
                    ┌─────────────┼─────────────┐
                    │             │             │
                    ▼             ▼             ▼
            ┌───────────┐ ┌───────────┐ ┌───────────┐
            │PostgreSQL │ │   Redis   │ │  Mailpit  │
            │ Database  │ │Cache/Queue│ │  (Dev)    │
            └───────────┘ └───────────┘ └───────────┘
```

## Tech Stack

| Layer       | Technology                                      |
| ----------- | ----------------------------------------------- |
| API Gateway | Nginx                                           |
| Backend     | Python 3.12 + FastAPI                           |
| Frontend    | Next.js 16 (React 19, TypeScript)               |
| Database    | PostgreSQL 17 (supports MySQL, MariaDB, SQLite) |
| Cache/Queue | Redis 8                                         |
| ORM         | SQLAlchemy 2.0 (async) + Alembic migrations     |
| Task Queue  | Celery with Redis broker                        |
| Logging     | Python logging (backend), Pino (frontend)       |
| Email       | Mailpit (development), SMTP (production)        |
| Deployment  | Docker Compose                                  |

## Project Structure

```
BaseApp/
├── .env.example              # Docker Compose variables
├── .gitignore
├── docker-compose.yml        # Container orchestration
├── nginx.conf                # API Gateway config
├── CLAUDE.md                 # Claude Code instructions
├── PROJECT_CONTEXT.md        # This file
├── README.md
│
├── API/                      # REST Client test files
│
├── backend/                  # FastAPI Backend
│   ├── .env                  # Environment variables
│   ├── .env.example          # Environment template
│   ├── Dockerfile            # Container configuration
│   ├── requirements.txt
│   ├── alembic.ini
│   │
│   ├── logs/                 # Application logs (git-ignored)
│   │   ├── app-YYYY-MM-DD.log
│   │   └── error-YYYY-MM-DD.log
│   │
│   ├── alembic/              # DB migrations
│   │   └── versions/
│   │
│   └── app/
│       ├── __init__.py
│       ├── main.py           # FastAPI app entry
│       ├── config.py         # Settings (pydantic-settings)
│       ├── database.py       # Async DB connection
│       ├── startup.py        # Auto-seed roles & config
│       ├── create_admin.py   # CLI admin creation
│       │
│       ├── models/           # SQLAlchemy models
│       │   ├── __init__.py
│       │   ├── user.py       # User model
│       │   ├── rbac.py       # Role & Permission models
│       │   ├── config.py     # AppConfig model
│       │   └── password_reset.py
│       │
│       ├── api/              # API routes
│       │   ├── __init__.py
│       │   ├── auth.py       # Authentication endpoints
│       │   ├── users.py      # User endpoints
│       │   └── admin/
│       │       ├── __init__.py
│       │       ├── users.py  # Admin user management
│       │       ├── rbac.py   # Roles & permissions
│       │       └── config.py # Configuration management
│       │
│       ├── tasks/            # Celery background tasks
│       │   ├── __init__.py
│       │   ├── celery_app.py
│       │   └── email.py
│       │
│       └── utils/
│           ├── __init__.py
│           ├── logger.py     # Logging with rotation
│           ├── encryption.py # Config value encryption
│           └── permissions.py
│
├── frontend/                 # Next.js 16 Frontend
│   ├── .env                  # Environment variables
│   ├── .env.example          # Environment template
│   ├── Dockerfile            # Container configuration
│   ├── package.json
│   ├── tsconfig.json
│   ├── tailwind.config.ts
│   ├── next.config.ts
│   │
│   ├── logs/                 # Application logs (git-ignored)
│   │   ├── app-YYYY-MM-DD.log
│   │   └── error-YYYY-MM-DD.log
│   │
│   └── src/
│       ├── app/              # App Router pages
│       │   ├── layout.tsx
│       │   ├── page.tsx
│       │   ├── globals.css
│       │   ├── login/
│       │   ├── signup/
│       │   ├── forgot-password/
│       │   ├── reset-password/
│       │   ├── profile/
│       │   ├── dashboard/
│       │   │   ├── layout.tsx
│       │   │   ├── page.tsx
│       │   │   └── profile/
│       │   ├── admin/
│       │   │   ├── layout.tsx
│       │   │   ├── page.tsx
│       │   │   ├── users/
│       │   │   ├── roles/
│       │   │   ├── settings/
│       │   │   └── profile/
│       │   └── api/
│       │       └── log/      # Client error logging endpoint
│       │           └── route.ts
│       │
│       ├── components/
│       │   ├── ui/           # UI components
│       │   └── ProfileContent.tsx
│       │
│       └── lib/
│           ├── api.ts        # API client
│           └── logger.ts     # Logging utility
│
└── .vscode/
    └── settings.json         # VS Code Setting with REST Client config
```

## Commands Reference

```bash
# Start development
docker compose up -d    # handle default database seeding

# Create super admin
docker compose exec base-app-backend python -m app.create_admin

# Stop all services
docker compose down

# View logs
docker compose logs -f nginx
docker compose logs -f base-app-backend
docker compose logs -f base-app-frontend

# Run migrations
docker compose exec base-app-backend alembic upgrade head

# Create new migration
docker compose exec base-app-backend alembic revision --autogenerate -m "description"

# Access DB
docker compose exec base-app-db psql -U sampleapp -d sampleapp

# Run backend tests
docker compose exec base-app-backend pytest

# Rebuild containers
docker compose build --no-cache
docker compose up -d
```

## Code Style

### Python (Backend)

- Use type hints everywhere
- Use async/await for IO operations
- Use Pydantic v2 for validation
- Use SQLAlchemy 2.0 style (not legacy 1.x)
- Follow PEP 8

### TypeScript (Frontend)

- Strict mode enabled
- Use interfaces for API responses
- Use React Query or SWR for data fetching
- Use Tailwind CSS for styling
- Use App Router (not Pages Router)

## Database Configuration

The backend supports multiple database dialects:

| Dialect    | Driver    | Default Port |
| ---------- | --------- | ------------ |
| PostgreSQL | asyncpg   | 5432         |
| MySQL      | aiomysql  | 3306         |
| MariaDB    | aiomysql  | 3306         |
| SQLite     | aiosqlite | N/A          |

Configure via environment variables:

```env
DATABASE_DIALECT=postgresql
DATABASE_USER=sampleapp
DATABASE_SECRET=sampleapp
DATABASE_HOST=base-app-db
DATABASE_PORT=5432
DATABASE_NAME=sampleapp
```

## Logging

### Backend Logs

- Location: `backend/logs/`
- Files: `app-YYYY-MM-DD.log`, `error-YYYY-MM-DD.log`
- Auto-cleanup: Configurable via `LOG_RETENTION_DAYS`

### Frontend Logs

- Location: `frontend/logs/`
- Server-side: Pino with file rotation
- Client-side: Errors sent to `/api/log` endpoint

## MVP Features

### Implemented

- API Gateway (Nginx) with microservices routing
- Multi-database support (PostgreSQL, MySQL, MariaDB, SQLite)
- Database-driven configuration
- User signup/login with JWT authentication
- Password reset via email (Mailpit for testing)
- Role-Based Access Control (RBAC)
- Permission-based access management
- Profile management (update details, change password)
- Admin panel with dark sidebar theme
- User management (view, edit, activate/deactivate)
- Role management (create, edit, delete, copy permissions)
- Settings management (admin)
- Application logging with rotation and auto-cleanup

### Planned

- Multi-language support
- Email notifications (production SMTP)
- 2FA authentication
- Password Generator
- Session & device Management
- Additional microservices (auth-service, billing-service)

## Environment Files

| File                    | Purpose                |
| ----------------------- | ---------------------- |
| `.env.example`          | Docker Compose ports   |
| `backend/.env.example`  | Backend configuration  |
| `frontend/.env.example` | Frontend configuration |
