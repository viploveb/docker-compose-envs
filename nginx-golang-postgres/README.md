# nginx-golang-postgres Docker Compose Setup

This repository demonstrates a full-stack application using Docker Compose with Nginx as a reverse proxy, a Golang backend API server, and PostgreSQL database. It's ideal for local development, testing microservices, or learning container orchestration in a cloud-native environment like AWS EKS.

## Project Structure
```
.
├── backend/ # Golang app source
│ ├── Dockerfile
│ ├── go.mod
│ ├── go.sum
│ └── main.go
├── db/ # Postgres init data
│ └── password.txt
├── proxy/ # Nginx config
│ └── nginx.conf
└── docker-compose.yaml # Orchestration file
```

The setup defines three services: `proxy` (Nginx), `backend` (Golang), and `db` (Postgres). Nginx routes traffic to the Golang app on port 8000, while the app connects to Postgres.

## Prerequisites

- Docker and Docker Compose installed
- Port 80 free on host (maps to Nginx)
- Go 1.13+ (for rebuilding backend if editing code)


## Quick Start

1. Clone and navigate:
`
git clone <repo-url>
cd nginx-golang-postgres
`

2. Start services:
`
docker compose up -d
`

3. View status:
`
docker compose ps
`

Expected output:
```
NAME STATUS PORTS
nginx-golang-postgres-backend-1 running 8080/tcp
nginx-golang-postgres-db-1 running (healthy) 5432/tcp
nginx-golang-postgres-proxy-1 running 0.0.0.0:80->80/tcp
```

Access the app at `http://localhost`.

## Service Details

### Backend (Golang)
- Builds multi-stage from `golang:alpine`.
- Exposes API on port 8080.
- Connects to `db` via `host=postgres`.

### Database (Postgres)
- Uses official `postgres` image.
- Password from `./db/password.txt`.
- Persists data in `./postgres-data` volume.

### Proxy (Nginx)
- Custom config proxies `/` to `backend:8080`.
- Maps host port 80 to container 80.

## Customization

- Edit `backend/main.go` and rebuild: `docker compose up --build`.
- Scale backend: `docker compose up -d --scale backend=3`.
- View logs: `docker compose logs -f backend`.
- Connect to DB: `docker compose exec db psql -U postgres`.

For production, add healthchecks, secrets, and deploy to Kubernetes with ArgoCD.

## Troubleshooting

- Port conflict: Change `ports: - "80:80"` in `docker-compose.yaml`.
- Rebuild: `docker compose down && docker compose up --build`.
- Volumes override issues: Avoid mounting source code over built binaries.
