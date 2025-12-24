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

## Environment Files

| File                    | Purpose                |
| ----------------------- | ---------------------- |
| `.env.example`          | Docker Compose ports   |
| `backend/.env.example`  | Backend configuration  |
| `frontend/.env.example` | Frontend configuration |

### Phase 1

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

### Phase 2

- Security Enhancements
- Internationalization
- Multi-language support
- Email notifications (production SMTP)
- 2FA authentication
- Password Generator
- Session & device Management
- Additional microservices (auth-service, billing-service)
- Polish & Infrastructure
