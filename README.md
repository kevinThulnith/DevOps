![Django](https://img.shields.io/badge/Django-%23092E20.svg?logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/Django_REST-FF1709?logo=django&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?logo=JSON%20web%20tokens)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)
![React](https://img.shields.io/badge/React-%2320232a.svg?logo=react&logoColor=%2361DAFB)
![Vite](https://img.shields.io/badge/Vite-646CFF?logo=vite&logoColor=fff)
![Tailwind](https://img.shields.io/badge/Tailwind_CSS-38B2AC?logo=tailwind-css&logoColor=white)
![Axios](https://img.shields.io/badge/Axios-5A29E4?logo=axios&logoColor=white)
![NodeJS](https://img.shields.io/badge/Node.js-6DA55F?logo=node.js&logoColor=white)
![npm](https://img.shields.io/badge/npm-CB3837?logo=npm&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?logo=nginx&logoColor=white)
![Google Cloud](https://img.shields.io/badge/Google%20Cloud-%234285F4.svg?logo=google-cloud&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?logo=gnubash&logoColor=fff)
![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white)
![Loki](https://img.shields.io/badge/Loki-F46800?logo=grafana&logoColor=white)
![uv](https://img.shields.io/badge/uv-261230.svg?logo=uv&logoColor=#de5fe9)
![Ruff](https://custom-icon-badges.demolab.com/badge/Ruff-261230.svg?logo=ruff-logo)
![Postman](https://img.shields.io/badge/Postman-FF6C37?logo=postman&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?logo=github-actions&logoColor=white)

# DevOps

DevOps project with developer tool use. This branch contains main code + github action code. Built as a decoupled SPA + REST API system, containerized end-to-end, and shipped with a full observability stack.

## Table of Contents

- [Core Development Principles](#core-development-principles)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Security Model](#security-model)
- [Project Architecture (Repo Layout)](#project-architecture)
- [Setting Up Environment Variables](#setting-up-environment-variables)
- [Docker Compose](#docker-compose)
- [Seed Sample Data](#seed-sample-data)

## Clone repo

```sh
git clone https://github.com/kevinThulnith/DevOps.git
```

## Core Development Principles

These are the architectural and engineering principles the codebase is
built around:

- **Decoupled architecture** — the React SPA and Django API are entirely
  separate services communicating over a well-defined REST/WebSocket
  boundary, allowing each to scale and deploy independently.
- **Real-time first** — Django Channels + Redis push live updates
  (creates/updates/deletes) to connected clients over WebSockets, with
  permission-filtered broadcasting so users only ever receive data they're
  authorized to see.
- **RESTful API design** — a consistent, resource-oriented API built with
  Django REST Framework, using standard paginated list responses and
  uniform error formats, designed to support both the SPA and future
  third-party integrations.
- **Automation over manual bookkeeping** — key operational workflows are
  driven by business logic rather than manual steps: operator
  auto-assignment/removal, dynamic role promotion on supervisor/manager
  appointment, inventory quantity updates on order receipt, and automatic
  labor allocation on task assignment.
- **Security as a default, not an add-on** — short-lived rotating JWTs with
  blacklisting, CSRF and CORS enforcement, DRF permission classes on every
  endpoint, Nginx-level rate limiting and security headers, non-root
  containers, and secrets managed exclusively via environment variables.
- **Infrastructure as code / reproducible environments** — the entire
  stack (app + observability) is defined in Docker Compose with
  multi-stage builds, so `docker-compose up` produces an identical
  environment anywhere.
- **Consistent UI patterns** — every module follows the same page patterns
  (List / Add / Edit / View), the same custom hooks for data fetching and
  WebSocket state sync, and a single custom dark design system — no
  external UI component library.
- **Performance-conscious frontend** — lazy-loaded routes, memoized
  derived state, callback optimization, and background token refresh to
  avoid interrupting the user session.

## Tech Stack

### Backend

| Technology            | Purpose                               |
| --------------------- | ------------------------------------- |
| Python                | Core programming language             |
| Django                | Web framework                         |
| Django REST Framework | RESTful API toolkit                   |
| Django Channels       | WebSocket / async support             |
| Daphne                | ASGI application server               |
| PostgreSQL            | Primary relational database           |
| Redis                 | WebSocket channel layer & caching     |
| SimpleJWT             | JWT auth with rotation & blacklisting |
| dj-rest-auth          | Auth REST endpoints                   |
| django-allauth        | Google OAuth 2.0 social auth          |
| django-cors-headers   | CORS control                          |
| django-filter         | QuerySet filtering                    |
| WhiteNoise            | Static file serving in production     |
| Pillow                | Image processing for media files      |
| uv                    | Fast Python package manager           |

### Frontend

| Technology          | Purpose                           |
| ------------------- | --------------------------------- |
| React               | UI component framework            |
| Vite (SWC)          | Build tool / dev server           |
| React Router DOM    | Client-side routing (40+ routes)  |
| Axios               | HTTP client with interceptors     |
| TailwindCSS         | Utility-first CSS                 |
| Lucide React        | Primary icon library              |
| React Icons         | Additional icon set               |
| @react-oauth/google | Google OAuth frontend integration |
| jwt-decode          | Client-side JWT parsing           |

### Infrastructure & Observability

| Technology                | Purpose                                                 |
| ------------------------- | ------------------------------------------------------- |
| Docker / Docker Compose   | Containerization & multi-container orchestration        |
| Multi-stage Docker builds | Minimized, non-root production images                   |
| Nginx 1.25 (Alpine)       | Reverse proxy, SPA hosting, static files, rate limiting |
| Prometheus                | Metrics scraping and storage                            |
| Grafana                   | Metrics dashboards                                      |
| Loki + Promtail           | Log aggregation and shipping                            |
| GitHub Actions            | CI/CD pipelines                                         |

## System Architecture

FMS follows a **decoupled architecture** — frontend and backend are
independent services communicating over a defined API boundary.

| Decision                 | Rationale                                         |
| ------------------------ | ------------------------------------------------- |
| Decoupled SPA + REST API | Independent scaling, clean separation of concerns |
| Django Channels + Redis  | Real-time push updates via WebSockets             |
| JWT authentication       | Stateless, scalable, works across services        |
| Nginx as reverse proxy   | Single entry point, routing + static files        |
| Docker Compose           | One command brings up the entire system           |

**Real-time flow:** an event occurs → a Django signal fires → data is
serialized → published to the relevant Redis channel group → the Redis
channel layer fans it out to all subscribed Django Channels consumers →
connected clients receive filtered, permission-aware updates instantly, no
page refresh required.

## Security Model

- **Auth**: 30-minute JWT lifetimes with automatic rotation and refresh-token
  blacklisting; Google OAuth2 via server-side authorization code exchange;
  passwords hashed with PBKDF2/SHA256 (720,000 iterations).
- **API**: CSRF protection on state-changing requests, CORS restricted to
  allowed origins, Nginx rate limiting (10 req/s, burst 20), per-endpoint
  DRF role permission classes.
- **Infrastructure**: non-root container user, multi-stage builds that
  exclude build tooling from production images, Nginx security headers
  (`X-Frame-Options`, `X-Content-Type-Options`, `X-XSS-Protection`,
  `server_tokens off`), sensitive file types blocked at the Nginx level,
  all secrets via environment variables.
- **WebSockets**: token-authenticated connections, invalid/expired tokens
  rejected before any data exchange, permission-filtered message delivery.

## Project Architecture

```sh
DevOps/
├── backend/          # Django REST API (Python / uv)
├── frontend/         # React + Vite + Tailwind CSS
├── proxy/            # Nginx reverse proxy
├── observability/    # Monitoring and logging console
│   ├── grafana/
│   │   └── provisioning/
│   │       ├── dashboards/
│   │       └── datasources/
│   ├── prometheus/
│   └── promtail/
├── docker-compose.yml
├── .env              # have to create, instructions below
└── README.md
```

## Setting Up Environment Variables

The project requires a `.env` file before running in the project root directory. These files are **not committed** — create them manually.

### 🪄 Google OAuth Credentials

Required for both Google sign-in and the `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` / `VITE_CLIENT_ID` variables.

1. Go to [Google Cloud Console](https://console.cloud.google.com/) and create a project (or select an existing one).
2. Navigate to **APIs & Services → Credentials → Create Credentials → OAuth 2.0 Client ID**.
3. Set **Application type** to **Web application**.
4. Under **Authorized JavaScript origins** add:

   ```sh
   http://localhost
   http://localhost:5173
   ```

5. Under **Authorized redirect URIs** add:

   ```sh
   http://localhost
   http://localhost:5173
   http://localhost:8000/accounts/google/login/callback/
   ```

6. Click **Create** — copy the **Client ID** and **Client Secret** into the env file below.

> Only email addresses that already exist as users in the system can sign in via Google. New Google accounts are rejected by the custom adapter.

### 🔑 Django Secret Key

Generate a secure key with (assuming python is installed):

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Create a `.env` file in the project root with these values:

```env
# Postgres Settings
POSTGRES_DB=DbName
POSTGRES_USER=DbUser
POSTGRES_PASSWORD=DbPassword

# Redis Settings
REDIS_HOST=fms-prod-redis
REDIS_PORT=6379

# Database Settings
DATABASE_ENGINE=postgresql_psycopg2
DATABASE_NAME=DbName
DATABASE_USERNAME=DbUser
DATABASE_PASSWORD=DbPassword
DATABASE_HOST=fms-prod-database
DATABASE_PORT=5432
DATABASE_URL=postgresql://DbUser:DbPassword@fms-prod-database:5432/DbName

# Backend Settings
DEBUG=False
API_PORT=8000
JWT_SECRET_KEY=add-jwt-secret
SECRET_KEY=add-secret-key
CORS_ORIGINS=http://localhost,http://host.docker.internal,http://host.docker.internal:80
ALLOWED_HOSTS=localhost,host.docker.internal,127.0.0.1,localhost,localhost:5173,localhost:8000,127.0.0.1,127.0.0.1:8000,127.0.0.1:5173

# Google OAuth secrets
GOOGLE_CALLBACK_URL=http://localhost
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
VITE_CLIENT_ID=your-google-client-id

# Grafana Settings
GF_SECURITY_ADMIN_USER=admin
GF_SECURITY_ADMIN_PASSWORD=your-grafana-password
GF_SERVER_ROOT_URL=http://localhost/grafana/
GF_SERVER_SERVE_FROM_SUB_PATH=true

# Postgres Exporter
DATA_SOURCE_NAME=postgresql://DbUser:DbPassword@fms-prod-database:5432/DbName?sslmode=disable

# Redis Exporter
REDIS_ADDR=redis://fms-prod-redis:6379
```

## Docker Compose

This project uses Docker Compose to define and run multi-container Docker applications.

### Services

| Service           | Image                        | Port | Description                                 |
| ----------------- | ---------------------------- | ---- | ------------------------------------------- |
| Database          | `postgres:14-alpine`         | 5432 | PostgreSQL database with persistent storage |
| Redis             | `redis:7-alpine`             | 6379 | In-memory cache (LRU eviction, AOF enabled) |
| Backend           | Custom (Django)              | 8000 | Django REST API served by Daphne (ASGI)     |
| Frontend          | Custom (Vite/React)          | —    | One-shot builder; outputs static assets     |
| Proxy             | Custom (Nginx)               | 80   | Reverse proxy serving frontend & API routes |
| Prometheus        | `prom/prometheus:v2.52.0`    | 9090 | Metrics scraping and storage                |
| Grafana           | `grafana/grafana-oss:11.0.0` | 3000 | Metrics dashboards (via `/grafana/`)        |
| Loki              | `grafana/loki:3.0.0`         | 3100 | Log aggregation                             |
| Promtail          | `grafana/promtail:3.0.0`     | —    | Log shipper from backend logs to Loki       |
| Postgres Exporter | —                            | —    | Exposes Postgres metrics to Prometheus      |
| Redis Exporter    | —                            | —    | Exposes Redis metrics to Prometheus         |

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running

Run this to bring the stack up:

```sh
# Build | Start services in background
docker-compose --env-file .env up --build -d

# View logs
docker compose logs -f

# Stop services
docker-compose stop

# Stop and remove all services
docker-compose down

# Stop and remove volumes (⚠️ destroys data)
docker-compose down -v
```

Access the application at `http://localhost`.
Access Grafana dashboards at `http://localhost/grafana/`.

### Named Volumes

| Volume                     | Used By          | Mount Path                                 |
| -------------------------- | ---------------- | ------------------------------------------ |
| `fms-prod-database-data`   | Database         | `/var/lib/postgresql/data`                 |
| `fms-prod-redis-data`      | Redis            | `/data`                                    |
| `fms-prod-frontend-dist`   | Frontend → Proxy | `/frontend-dist` → `/usr/share/nginx/html` |
| `fms-prod-media-files`     | Backend → Proxy  | `/app/media`                               |
| `fms-prod-backend-logs`    | Backend          | `/app/logs`                                |
| `fms-prod-logs`            | Database, Redis  | Log directories                            |
| `fms-prod-prometheus-data` | Prometheus       | `/prometheus`                              |
| `fms-prod-grafana-data`    | Grafana          | `/var/lib/grafana`                         |
| `fms-prod-loki-data`       | Loki             | `/loki`                                    |

## Seed Sample Data

Run the seed scripts **in order** inside the backend container. Each script populates a different part of the database with realistic sample data. They live in `backend/scripts`.

| Script     | Data seeded                  |
| ---------- | ---------------------------- |
| `script1`  | Users                        |
| `script2`  | Departments                  |
| `script3`  | Workshops                    |
| `script4`  | Machines                     |
| `script5`  | Suppliers & Materials        |
| `script6`  | Production Lines             |
| `script7`  | Manufacturing Processes      |
| `script8`  | Products & Product Processes |
| `script9`  | Production Schedules         |
| `script10` | Projects & Tasks             |
| `script11` | Skill Matrix (Labor)         |

Run them one by one:

```sh
docker exec -it fms-prod-backend python scripts/script1.py
docker exec -it fms-prod-backend python scripts/script2.py
docker exec -it fms-prod-backend python scripts/script3.py
docker exec -it fms-prod-backend python scripts/script4.py
docker exec -it fms-prod-backend python scripts/script5.py
docker exec -it fms-prod-backend python scripts/script6.py
docker exec -it fms-prod-backend python scripts/script7.py
docker exec -it fms-prod-backend python scripts/script8.py
docker exec -it fms-prod-backend python scripts/script9.py
docker exec -it fms-prod-backend python scripts/script10.py
docker exec -it fms-prod-backend python scripts/script11.py
```
