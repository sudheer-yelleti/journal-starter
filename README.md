# Journal API — Cloud-Native Build from Linux to Production Security

> A production-grade REST API built end-to-end across 7 engineering disciplines: Linux, networking, Python/FastAPI, cloud deployment (Azure), DevOps, and security hardening. Built as part of the [Learn to Cloud](https://learntocloud.guide) curriculum.

[![CI](https://github.com/sudheer-yelleti/journal-starter/actions/workflows/ci.yml/badge.svg)](https://github.com/sudheer-yelleti/journal-starter/actions/workflows/ci.yml)
![Python](https://img.shields.io/badge/Python-3.12-blue)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115-green)
![Azure](https://img.shields.io/badge/Cloud-Azure-0078D4)
![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC)
![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes-326CE5)
![Docker](https://img.shields.io/badge/Container-Docker-2496ED)

---

## What This Is

A learning journal API that lets users record daily engineering progress like what they worked on, what they struggled with, and what they plan to do next. Simple domain, complex stack. The point isn't the CRUD, it's everything surrounding it.

The application starts as a local Python/FastAPI service and gets progressively hardened through cloud deployment, containerization, infrastructure-as-code, CI/CD, observability, and enterprise security controls. Each phase adds a real production concern on top of the last.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT                               │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTPS
┌────────────────────────▼────────────────────────────────────┐
│              Azure Load Balancer / Ingress                  │
└────────────────────────┬────────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────────┐
│         Kubernetes Cluster (AKS)                            │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  FastAPI Pod  │  FastAPI Pod  │  FastAPI Pod           │  │
│  └───────────────┴───────────────┴───────────────────────┘  │
│              ↓ OpenTelemetry traces/metrics                  │
└────────────┬────────────────────────────────────────────────┘
             │
┌────────────▼────────────────────────────────────────────────┐
│          Azure PostgreSQL Flexible Server                   │
│          (IAM auth + secrets via Key Vault)                 │
└─────────────────────────────────────────────────────────────┘
```

**Provisioned entirely via Terraform** — VMs, networking, databases, IAM roles, and Kubernetes manifests live in `infra/` and `k8s/`.

---

## Tech Stack

| Layer | Technology |
|---|---|
| API | Python 3.12, FastAPI, Pydantic v2 |
| Database | PostgreSQL 16 |
| AI Analysis | OpenAI SDK (Azure OpenAI / GitHub Models) |
| Container | Docker, Docker Compose |
| Orchestration | Kubernetes (AKS) |
| IaC | Terraform (HCL) |
| CI/CD | GitHub Actions |
| Observability | OpenTelemetry, structured logging |
| Cloud | Microsoft Azure |
| Security | Azure Key Vault, IAM, network security groups |
| Dev Tooling | uv, ruff, pyright, pre-commit |

---

## Project Structure

```
journal-starter/
├── api/                    # FastAPI application
│   ├── routers/            # Route handlers
│   ├── services/           # Business logic + LLM service
│   ├── models/             # Pydantic models with validation
│   └── config.py           # Settings via pydantic-settings
├── infra/                  # Terraform — Azure infrastructure
├── k8s/                    # Kubernetes manifests
├── scripts/                # Utility scripts (LLM verification, etc.)
├── tests/                  # Pytest test suite (50 tests)
├── .github/workflows/      # CI pipeline (lint + test)
├── .devcontainer/          # Dev container config (Docker + cloud CLI)
├── Dockerfile              # Production container image
├── docker-compose.yml      # Local dev environment
└── database_setup.sql      # Schema
```

---

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/entries` | Create a journal entry |
| `GET` | `/entries` | List all entries |
| `GET` | `/entries/{id}` | Get a single entry by ID |
| `PATCH` | `/entries/{id}` | Partially update an entry |
| `DELETE` | `/entries/{id}` | Delete an entry |
| `POST` | `/entries/{id}/analyze` | AI-powered sentiment + topic analysis |

### Data Schema

Each entry captures a daily engineering reflection:

```json
{
  "id": "uuid",
  "work": "What did you work on today?",
  "struggle": "What's one thing you struggled with?",
  "intention": "What will you study tomorrow?",
  "created_at": "2025-01-01T00:00:00Z",
  "updated_at": "2025-01-01T00:00:00Z"
}
```

All string fields are validated: non-empty, no whitespace-only values, max 256 characters, leading/trailing whitespace stripped.

---

## Phase-by-Phase Build Log

This project was built in layers, each phase adding a distinct engineering capability.

### Phase 1 — Linux & Bash
Set up developer environment, cloud CLI tooling, SSH configuration, and Infrastructure-as-Code fundamentals. Dev container configured in `.devcontainer/devcontainer.json` with optional Azure/AWS/GCP CLI bootstrapping.

### Phase 2 — Networking Fundamentals
Applied networking concepts (IP/subnetting, DNS, HTTP, ports) to understand how the API is exposed, routed, and secured in a cloud VPC. Directly informed cloud networking design in Phase 4.

### Phase 3 — Python & API Development
Built the Journal API from scratch:
- FastAPI with full CRUD endpoints
- PostgreSQL integration
- Pydantic v2 models with field validation
- AI-powered entry analysis via OpenAI SDK
- Structured logging
- Pytest test suite (50 tests, CI-verified)

### Phase 4 — Cloud Deployment (Azure)
Deployed the API to Azure:
- Virtual machine provisioning
- Azure PostgreSQL Flexible Server
- IAM and identity management
- Cloud networking (VNet, subnets, NSGs)
- Secure remote access
- Cost management and monitoring foundations
- Azure OpenAI / Azure AI service integration

### Phase 5 — DevOps
Containerized and automated the full delivery pipeline:
- Dockerized the FastAPI application
- Kubernetes manifests in `k8s/` for AKS deployment
- GitHub Actions CI/CD pipeline (lint → test → deploy)
- Terraform IaC in `infra/` for repeatable Azure provisioning
- OpenTelemetry instrumentation for distributed tracing and metrics
- AI tooling and MCP integration

### Phase 6 — Security Hardening
Hardened the application to production security posture:
- IAM roles and least-privilege access
- Secrets management via Azure Key Vault (no plaintext secrets in env or code)
- Network security controls (NSGs, private endpoints)
- Monitoring and alerting
- Incident response procedures
- Secure Your Journal API capstone — all controls verified end-to-end

---

## Running Locally

### Prerequisites
- Docker Desktop
- VS Code with Dev Containers extension

### Setup

```bash
# 1. Fork and clone your fork
git clone https://github.com/YOUR_USERNAME/journal-starter.git
cd journal-starter

# 2. Configure environment
cp .env-sample .env
# Edit .env with your values

# 3. Open in VS Code dev container
code .
# Then: Command Palette → "Dev Containers: Reopen in Container"

# 4. Start the API (inside container)
./start.sh
```

API docs available at: http://localhost:8000/docs

### Running Tests

```bash
# Install dev dependencies
uv sync --all-extras

# Run all tests
uv run pytest -v

# Lint
uv run ruff check .

# Format
uv run ruff format .

# Type check
uv run pyright
```

All 50 tests run against a real PostgreSQL instance in CI. The AI analysis tests use an injected mock — no real LLM calls in CI.

---

## CI Pipeline

Every push and PR runs two jobs via GitHub Actions:

| Job | Checks |
|---|---|
| `lint` | `ruff check`, `ruff format --check`, `pyright` |
| `test` | Full pytest suite against a live Postgres 16 service container |

No secrets required for CI — the test job uses a disposable database and a mock OpenAI client.

---

## Infrastructure (Terraform)

The `infra/` directory contains Terraform configuration for provisioning the full Azure environment:
- Resource groups, VNet, subnets
- Azure Kubernetes Service (AKS)
- Azure PostgreSQL Flexible Server
- Azure Key Vault
- IAM role assignments
- Network security groups

```bash
cd infra/
terraform init
terraform plan
terraform apply
```

---

## Kubernetes Deployment

The `k8s/` directory contains manifests for deploying to AKS:

```bash
# Apply all manifests
kubectl apply -f k8s/

# Check pod status
kubectl get pods

# View logs
kubectl logs -l app=journal-api
```

---

## AI Analysis

The `POST /entries/{id}/analyze` endpoint returns a structured analysis of a journal entry:

```json
{
  "entry_id": "uuid",
  "sentiment": "positive",
  "summary": "The engineer made progress on Kubernetes deployment and is planning to explore Terraform next.",
  "topics": ["Kubernetes", "AKS", "Terraform", "cloud deployment"],
  "created_at": "2025-01-01T10:30:00Z"
}
```

Compatible with any OpenAI SDK-compatible provider: GitHub Models (free), Azure OpenAI, OpenAI, Groq, or local models via Ollama.

Configure via `.env`:
```
OPENAI_API_KEY=your_token
OPENAI_BASE_URL=https://models.inference.ai.azure.com
OPENAI_MODEL=gpt-4o-mini
```

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

*Built as part of the [Learn to Cloud](https://learntocloud.guide) curriculum — 7 phases from Linux fundamentals to production cloud security.*
