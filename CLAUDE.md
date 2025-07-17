# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a microservices-based e-commerce marketplace application built with Node.js and Express.js, following Domain-Driven Design (DDD) principles. The architecture consists of four main services:

- **auth-service** (port 4000): Handles user authentication with JWT
- **cart** (port 4001): Manages shopping cart operations
- **product** (port 4002): Product catalog management
- **gateway-service** (port 3000): API Gateway that routes requests to microservices

## Common Development Commands

### Running Services Locally
```bash
# From each service directory
npm install          # Install dependencies
npm run dev         # Run with nodemon (auto-reload)
npm start           # Run in production mode

# Only cart service has tests
cd cart && npm test # Run Jest tests with coverage
```

### Docker Commands
```bash
# Build and run all services
docker-compose up --build

# Individual service
docker build -t <service-name> ./<service-directory>
docker run -p <host-port>:<container-port> <service-name>
```

## Architecture Patterns

Each microservice follows DDD with this structure:
- **presentation/**: HTTP layer (controllers, routers)
- **application/**: Business logic and use cases
- **domain/**: Core business entities and repository interfaces
- **infrastructure/**: Database implementations (Sequelize/PostgreSQL)

### Service Communication
- Gateway forwards requests to microservices via HTTP
- Authentication uses JWT tokens passed in Authorization headers
- Services expect PostgreSQL connection via environment variables

### Testing Approach
- Only cart service has test implementation
- Uses Jest with dependency injection for unit testing
- Integration tests use Supertest
- Mock dependencies with `jest.fn()` and `jest.spyOn()`
- Coverage reports generated in `coverage/` directory

## Database Configuration

Each service requires PostgreSQL with these environment variables:
```
DB_NAME=<service>_db
DB_USER=<username>
DB_PASSWORD=<password>
DB_HOST=localhost
DB_PORT=5432
JWT_SECRET=<secret> (for auth-service)
```

## Deployment

The project deploys to Google Cloud Platform:
- GitHub Actions workflow in `.github/workflows/deploy.yml`
- Builds multi-platform Docker images
- Pushes to GCP Artifact Registry
- Deploys to VM via SSH with docker-compose

## Key Technical Decisions

1. **Express.js v5**: Using latest major version (note: different from v4 API)
2. **Sequelize ORM**: All database operations use Sequelize models
3. **JWT Authentication**: Shared JWT middleware in `/shared` directory
4. **No Centralized Testing**: Each service manages its own tests independently
5. **Environment-based Config**: Using dotenv for configuration management