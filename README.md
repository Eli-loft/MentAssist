# MentAssist

A clinical counselor workflow platform for managing client profiles, appointment scheduling, session notes, and treatment plans. Built as a production-structured capstone project at the University of Tennessee at Chattanooga.

## Tech Stack

| Layer | Technology |
|---|---|
| Backend API | FastAPI (Python) |
| ORM | SQLAlchemy |
| Database | PostgreSQL |
| Auth | JWT (python-jose), bcrypt |
| Containerization | Docker Compose |
| Reverse Proxy | Nginx with self-signed TLS |
| Frontend | React |

## Architecture

The backend is organized as a modular FastAPI application with one package per domain: `users`, `clients`, `appointments`, `notes`, `treatment_plans`. Each module owns its own models, schemas, CRUD logic, and router. SQLAlchemy handles all ORM operations against a PostgreSQL instance running in a separate container. JWT authentication enforces role-based access control across all six API modules. Audit logging is applied at the database level via PostgreSQL triggers. The full stack runs under Docker Compose with Nginx proxying HTTPS traffic to the FastAPI backend and React frontend.

## Project Structure

```
MentAssist/
├── backend/
│   ├── app/
│   │   ├── api/v1/
│   │   ├── core/          # JWT auth, config, security
│   │   ├── db/            # SQLAlchemy base, session
│   │   ├── clients/
│   │   ├── appointments/
│   │   ├── notes/
│   │   ├── treatment_plans/
│   │   └── users/
│   └── requirements.txt
├── mentassistdb/
│   ├── schema.sql
│   └── scripts/seed_dev.sql
├── frontend/
├── ops/
│   └── nginx/
├── docker-compose.yml
└── setup.py
```

## Prerequisites

| Requirement | Notes |
|---|---|
| Docker Desktop | Must be running before setup |
| Python 3.8+ | Used to run `setup.py` |
| OpenSSL | Windows: ships with Git for Windows or Miniconda |

## Setup

```bash
git clone https://github.com/Eli-loft/MentAssist.git
cd MentAssist
```

Copy environment files:

```bash
cp mentassistdb/.env.example mentassistdb/.env
cp backend/.env.example backend/.env
```

Generate a secret key and add it to `backend/.env`:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
# Copy output → SECRET_KEY=<generated_value>
```

Start all services:

```bash
python setup.py
```

## Verify

```bash
docker compose ps
```

All services should show `running`.

| Endpoint | URL |
|---|---|
| Backend API (Swagger UI) | http://localhost:8000/docs |
| Application | https://localhost |

> Browser will warn about the self-signed TLS certificate — this is expected.

## Seed Accounts

| Email | Password | Role |
|---|---|---|
| counselor1@mentassist.com | Mentor123! | counselor |
| counselor2@mentassist.com | Mentor123! | counselor |

`counselor2` owns three pre-seeded clients: Maya Johnson, Daniel Reyes, and Priya Nair.

## Reset

```bash
docker compose down -v
python setup.py
```

## Notes

- Seed password hashes are pre-generated bcrypt — no manual hash generation needed
- Backend connects to PostgreSQL as `app_user`
- Do not commit `.env` files
