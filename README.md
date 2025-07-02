<p align="center">
  <img src="./assets/arachne-logo-transparent.png" alt="Arachne Logo" width="400">
</p>

<h1 align="center">Arachne</h1>

<p align="center">
  A scalable, production-ready Go web scraping service with an asynchronous job API, persistent job state, and containerization.
</p>

<p align="center">
  <a href="https://goreportcard.com/report/github.com/kareemsasa3/arachne"><img src="https://goreportcard.com/badge/github.com/kareemsasa3/arachne" /></a>
  <a href="https://github.com/kareemsasa3/arachne/blob/main/LICENSE"><img src="https://img.shields.io/github/license/kareemsasa3/arachne?style=flat-square&color=blue" /></a>
  <a href="https://hub.docker.com/r/kareemsasa3/arachne"><img src="https://img.shields.io/docker/pulls/kareemsasa3/arachne.svg" /></a>
  <a href="https://github.com/kareemsasa3/arachne/releases/latest"><img src="https://img.shields.io/github/v/release/kareemsasa3/arachne" /></a>
</p>

## ✨ Features

- **🚀 Asynchronous scraping API**: Submit jobs and poll for results
- **💾 Persistent job state**: Uses Redis for resilience and multi-instance support
- **📈 Scalable architecture**: Ready for distributed job processing
- **🛡️ Circuit breaker pattern**: Protects against cascading failures
- **🔄 Retry logic**: Handles transient failures with exponential backoff
- **📊 Comprehensive metrics**: Real-time performance monitoring
- **🧪 Comprehensive API tests**: Uses `httptest` for handler coverage
- **🐳 Containerized**: Docker and Docker Compose for easy deployment
- **🏥 Health checks**: System monitoring and observability

## 🚀 Quick Start

### Prerequisites
- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)

### Run with Docker Compose

```bash
# Clone the repository
git clone <your-repo-url>
cd go-practice

# Start the service
docker-compose up --build
```

**Services Available:**
- **API Server**: http://localhost:8080
- **Redis**: localhost:6379
- **Redis Commander UI**: http://localhost:8081

- API: http://localhost:8080
- Redis: localhost:6379
- Redis Commander UI: http://localhost:8081

### 📡 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/scrape` | Submit a new scraping job |
| `GET` | `/scrape/status?id=<job_id>` | Get job status and results |
| `GET` | `/health` | Health check |
| `GET` | `/metrics` | System metrics (if enabled) |

### 📝 Example: Submit a Scrape Job

```bash
curl -X POST http://localhost:8080/scrape \
  -H 'Content-Type: application/json' \
  -d '{
    "urls": ["https://golang.org", "https://httpbin.org/get"]
  }'
```

### 📊 Example: Check Job Status

```bash
curl "http://localhost:8080/scrape/status?id=<job_id>"
```

### 🌐 Example: Scrape a Single Site

```bash
curl -X POST http://localhost:8080/scrape \
  -H 'Content-Type: application/json' \
  -d '{
    "site_url": "https://jsonplaceholder.typicode.com/posts"
  }'
```

## 🏗️ Architecture Overview

- **🌐 API Layer**: Handles HTTP requests, creates jobs, persists them in Redis
- **💾 Persistent Storage**: Redis stores all job state, making the system resilient to restarts and ready for distributed scaling
- **🛡️ Circuit Breaker**: Protects against cascading failures with automatic recovery
- **🔄 Retry Logic**: Handles transient failures with exponential backoff
- **📊 Metrics & Monitoring**: Real-time performance tracking and health checks
- **🐳 Containerization**: Dockerfile and docker-compose.yml for reproducible, portable deployment
- **🔧 Worker Model**: Ready for multiple scraper instances as workers, all pulling jobs from Redis

## ⚙️ Configuration

Environment variables (see `.env.example` and `docker-compose.yml`):

| Variable | Description | Default |
|----------|-------------|---------|
| `SCRAPER_API_PORT` | API server port | `8080` |
| `SCRAPER_REDIS_ADDR` | Redis host | `redis:6379` |
| `SCRAPER_REDIS_PASSWORD` | Redis password | (empty) |
| `SCRAPER_REDIS_DB` | Redis DB index | `0` |
| `SCRAPER_MAX_CONCURRENT` | Max concurrent requests | `5` |
| `SCRAPER_REQUEST_TIMEOUT` | Request timeout | `10s` |
| `SCRAPER_CIRCUIT_BREAKER_THRESHOLD` | Circuit breaker threshold | `3` |
| `SCRAPER_ENABLE_METRICS` | Enable metrics collection | `true` |

See `config.go` for all available options.

## 🧪 Testing

Run all tests:

```bash
go test ./...
```

Run tests with coverage:

```bash
go test -v -cover ./...
```

## 🔄 CI/CD with GitHub Actions

This project uses GitHub Actions for automated testing, building, and deployment. The CI/CD pipeline ensures code quality and automates the release process.

### 🤖 Automated Workflows

#### 1. **Test Workflow** (`.github/workflows/test.yml`)
- **Trigger**: Runs on every Pull Request to `main` branch
- **Purpose**: Ensures no broken code gets merged
- **Actions**:
  - Runs `go test -v ./...`
  - Runs tests with coverage
  - Provides immediate feedback on code quality

#### 2. **Release Workflow** (`.github/workflows/release.yml`)
- **Trigger**: Runs when code is pushed to `main` branch
- **Purpose**: Automatically builds and pushes Docker images
- **Actions**:
  - Builds Docker image from `Dockerfile`
  - Pushes to Docker Hub with `latest` and commit hash tags
  - Updates your Docker Hub repository automatically

#### 3. **Versioned Release Workflow** (`.github/workflows/release-versioned.yml`)
- **Trigger**: Runs when you create a Git tag (e.g., `v1.0.1`)
- **Purpose**: Creates official releases with semantic versioning
- **Actions**:
  - Builds and pushes versioned Docker image
  - Creates GitHub Release with release notes
  - Tags Docker image with version number

### 🛠️ Setup Instructions

#### Step 1: Add Docker Hub Secrets
1. Go to your GitHub repository → Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Add these secrets:
   - `DOCKERHUB_USERNAME`: Your Docker Hub username (`kareemsasa3`)
   - `DOCKERHUB_TOKEN`: Your Docker Hub Access Token (not password)

#### Step 2: Create Docker Hub Access Token
1. Go to [Docker Hub](https://hub.docker.com/) → Account Settings → Security
2. Click "New Access Token"
3. Give it a name (e.g., "GitHub Actions")
4. Set permissions to "Read, Write, Delete"
5. Copy the token and add it as `DOCKERHUB_TOKEN` secret

#### Step 3: Enable Branch Protection (Recommended)
1. Go to Settings → Branches
2. Add rule for `main` branch
3. Enable "Require status checks to pass before merging"
4. Select the "Run Go Tests" workflow as required

### 🚀 Workflow Usage

#### Daily Development
1. Create feature branch: `git checkout -b feature/new-feature`
2. Make changes and commit: `git commit -m "Add new feature"`
3. Push and create PR: `git push origin feature/new-feature`
4. GitHub Actions automatically runs tests
5. Merge when tests pass ✅

#### Creating a Release
```bash
# Create and push a version tag
git tag v1.0.1
git push origin v1.0.1

# GitHub Actions automatically:
# - Builds Docker image with v1.0.1 tag
# - Pushes to Docker Hub
# - Creates GitHub Release
```

### 📊 Benefits

- **🛡️ Quality Gate**: Tests run automatically on every PR
- **🚀 Zero-Downtime Deployments**: Automated Docker builds
- **📦 Consistent Releases**: Standardized release process
- **🔍 Transparency**: All builds and tests are visible in GitHub
- **⚡ Speed**: No manual testing or deployment steps

## 🚀 Extending for Distributed Job Queue

- **Message Queue**: Swap the in-process goroutine for a message queue (RabbitMQ, Kafka, or Redis Streams/Lists)
- **Worker Scaling**: Run multiple scraper workers, each pulling jobs from the queue and updating job state in Redis
- **Load Balancing**: Distribute scraping load across multiple instances
- **High Availability**: Enable true horizontal scaling and fault tolerance

## 📈 Performance Features

- **Circuit Breaker**: Automatic failure detection and recovery
- **Rate Limiting**: Domain-specific and global rate limiting
- **Retry Logic**: Exponential backoff for transient failures
- **Metrics Collection**: Real-time performance monitoring
- **Health Checks**: System health monitoring

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details. 