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

DevOps project with developer tool use. This branch contains github action code.

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
├── .env              # have to create instuctions are provided
└── README.md
```

### Setting up environment variables

The project requires a `.env` file before running in the project root directory. These files are **not committed** — create them manually.

#### 🪄 Google OAuth Credentials

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

6. Click **Create** — copy the **Client ID** and **Client Secret** into the env files below.

> Only email addresses that already exist as users in the system can sign in via Google. New Google accounts are rejected by the custom adapter.

#### 🔑 Django Secret Key

Generate a secure key with (assuming python is installed):

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Create `.env` file in project root add these values.

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
JWT_SECRET=add-jwt-secret
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

## Docker-Compose

This project uses Docker Compose to define and run multi-container Docker applications. 5 images are used in 5 separate services.

### Services

| Service    | Image                        | Port | Description                                 |
| ---------- | ---------------------------- | ---- | ------------------------------------------- |
| Database   | `postgres:14-alpine`         | 5432 | PostgreSQL database with persistent storage |
| Redis      | `redis:7-alpine`             | 6379 | In-memory cache (LRU eviction, AOF enabled) |
| Backend    | Custom (Django)              | 8000 | Django REST API with Gunicorn               |
| Frontend   | Custom (Vite/React)          | —    | One-shot builder; outputs static assets     |
| Proxy      | Custom (Nginx)               | 80   | Reverse proxy serving frontend & API routes |
| Prometheus | `prom/prometheus:v2.52.0`    | 9090 | Metrics scraping and storage                |
| Grafana    | `grafana/grafana-oss:11.0.0` | 3000 | Metrics dashboards (via /grafana/)          |
| Loki       | `grafana/loki:3.0.0`         | 3100 | Log aggregation                             |
| Promtail   | `grafana/promtail:3.0.0`     | —    | Log shipper from backend logs to Loki       |

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running

Run this to create docker-compose setup on pc.

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
Access Grafana dashboards at `http://localhost/grafana/`

### Named Volumes

| Volume                     | Used By          | Mount Path                                 |
| -------------------------- | ---------------- | ------------------------------------------ |
| `fms-prod-database-data`   | Database         | `/var/lib/postgresql/data`                 |
| `fms-prod-redis-data`      | Redis            | `/data`                                    |
| `fms-prod-frontend-dist`   | Frontend → Proxy | `/frontend-dist` → `/usr/share/nginx/html` |
| `fms-prod-media-files`     | Backend → Proxy  | `/app/media`                               |
| `fms-prod-backend-logs`    | Backend          | `/app/logs`                                |
| `fms-prod-logs`            | Database, Redis  | Log directories                            |
| `fms-prod-prometheus-data` | Prometheus       | /prometheus                                |
| `fms-prod-grafana-data`    | Grafana          | /var/lib/tgrafana                          |
| `fms-prod-loki-data`       | Loki             | /loki                                      |

## Seed Sample Data

Run the seed scripts **in order** inside the backend container. Each script populates a different part of the database with realistic sample data. They are backend/scripts.

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
