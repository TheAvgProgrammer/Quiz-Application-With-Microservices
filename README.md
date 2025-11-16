# Quiz Application with Microservices

[![Java](https://img.shields.io/badge/Java-17%2B-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.1.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2022.0.3-blue.svg)](https://spring.io/projects/spring-cloud)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

## 📋 Overview

This project showcases a cloud-ready, microservices-based Quiz Application engineered for scalability, modularity, and future extensibility. Core services are cleanly separated by responsibility: a **Service Registry** (dynamic discovery), an **API Gateway** (centralized routing and cross-cutting concerns), a **Question Service** (domain-focused question management), and a **Quiz Service** (orchestrating quiz composition and scoring). This decomposition illustrates domain-driven design principles: each bounded context can evolve independently, scale horizontally, and undergo isolated lifecycle changes without impacting others.

The system is structured for modern DevOps workflows: containerization via Docker, orchestration with docker-compose, and CI automation ensure reproducible builds and rapid feedback. The inclusion of OpenAPI documentation, test scaffolding, and observability hooks (Micrometer/Actuator) prepares the platform for production hardening—layering in distributed tracing, metrics aggregation, and resilience patterns (circuit breakers, retries) is straightforward.

The architecture naturally supports future value-add features such as authentication, personalized recommendations, leaderboards, adaptive difficulty, and analytics. By emphasizing clear contracts, separation of concerns, and automated quality gates, the project demonstrates engineering maturity and readiness for iterative delivery. Its intentional design communicates professional competency in microservices, Spring Boot, API design, containerization, and foundational platform operations—making it an effective portfolio piece. Overall, it balances simplicity with forward-compatible structure, signaling capability to build maintainable, scalable backend systems.

## 🏗️ Architecture

### System Architecture Diagram

```
                                    ┌─────────────────────┐
                                    │  External Client    │
                                    │  (Browser/Mobile)   │
                                    └──────────┬──────────┘
                                               │
                                               ▼
                                    ┌──────────────────────┐
                                    │    API Gateway       │
                                    │   (Port: 8765)       │
                                    │  - Routing           │
                                    │  - Load Balancing    │
                                    └──────────┬───────────┘
                                               │
                       ┌───────────────────────┼───────────────────────┐
                       │                       │                       │
                       ▼                       ▼                       ▼
            ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
            │  Quiz Service    │   │ Question Service │   │ Service Registry │
            │  (Port: 8090)    │   │  (Port: 8080)    │   │  (Port: 8761)    │
            │                  │   │                  │   │                  │
            │ - Quiz Creation  │   │ - Manage Qs      │   │  Eureka Server   │
            │ - Quiz Retrieval │◄──│ - Random Select  │   │  - Discovery     │
            │ - Score Calc     │   │ - Submit Score   │   │  - Registration  │
            └────────┬─────────┘   └──────────────────┘   └──────────────────┘
                     │                       ▲
                     │                       │
                     └───────────────────────┘
                        OpenFeign Client
                           
            ┌──────────────────┐
            │   PostgreSQL     │
            │  (Port: 5432)    │
            │  - Quiz Data     │
            │  - Question Data │
            └──────────────────┘
```

### Service Responsibilities

#### 1. **Service Registry** (Eureka Server)
- **Port**: 8761
- **Purpose**: Service discovery and registration
- **Technology**: Netflix Eureka
- **Key Features**:
  - Dynamic service discovery
  - Health monitoring
  - Load balancing support
  - Zero configuration for clients

#### 2. **API Gateway**
- **Port**: 8765
- **Purpose**: Single entry point for all client requests
- **Technology**: Spring Cloud Gateway
- **Key Features**:
  - Request routing to microservices
  - Load balancing across service instances
  - Cross-cutting concerns (logging, security, rate limiting)
  - Service discovery integration
  - TODO: Add authentication/authorization filters
  - TODO: Implement rate limiting

#### 3. **Quiz Service**
- **Port**: 8090
- **Purpose**: Quiz orchestration and management
- **Technology**: Spring Boot, Spring Data JPA
- **Key Features**:
  - Create quizzes from question pool
  - Retrieve quiz questions for users
  - Calculate quiz scores
  - Integrates with Question Service via OpenFeign
  - PostgreSQL database for quiz persistence
  - TODO: Add caching layer (Redis)
  - TODO: Implement retry and circuit breaker patterns (Resilience4j)

#### 4. **Question Service**
- **Port**: 8080 (Planned)
- **Purpose**: Question bank management
- **Technology**: Spring Boot, Spring Data JPA
- **Key Features**:
  - CRUD operations for questions
  - Random question selection by category
  - Answer validation
  - PostgreSQL database for question persistence
  - TODO: Full implementation (currently referenced as external service)

## 🚀 Tech Stack

- **Backend Framework**: Spring Boot 3.1.x
- **Microservices**: Spring Cloud 2022.0.3
  - Eureka Server (Service Discovery)
  - Spring Cloud Gateway
  - OpenFeign (Inter-service Communication)
- **Database**: PostgreSQL
- **Build Tool**: Maven
- **Containerization**: Docker, Docker Compose
- **API Documentation**: OpenAPI 3.0 (Springdoc)
- **Observability**: Micrometer, Spring Boot Actuator, Prometheus (Ready)
- **Testing**: JUnit 5, Spring Boot Test, RestAssured
- **Java Version**: 17 (Compatible with 21)

## 🏃 Quick Start

### Prerequisites

- Java 17 or higher ([Download](https://adoptium.net/))
- Maven 3.6+ or use included Maven wrapper
- Docker & Docker Compose ([Download](https://www.docker.com/))
- PostgreSQL 15+ (or use Docker Compose)

### Local Development Setup

#### Option 1: Using Docker Compose (Recommended)

```bash
# Clone the repository
git clone https://github.com/TheAvgProgrammer/Quiz-Application-With-Microservices.git
cd Quiz-Application-With-Microservices

# Build all services
mvn clean package -DskipTests

# Start all services with Docker Compose
docker-compose up -d

# Check service health
docker-compose ps
```

#### Option 2: Manual Setup

```bash
# 1. Start PostgreSQL (or use Docker)
docker run -d \
  --name quiz-postgres \
  -e POSTGRES_DB=quizdb \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=0000 \
  -p 5432:5432 \
  postgres:15

# 2. Build all services from root
mvn clean package -DskipTests

# 3. Start services in order (different terminals)

# Terminal 1: Service Registry (MUST START FIRST)
cd MicroserviceTutorials/service-registry
./mvnw spring-boot:run

# Wait for Eureka to be ready (http://localhost:8761)

# Terminal 2: API Gateway
cd MicroserviceTutorials/api-gateway
./mvnw spring-boot:run

# Terminal 3: Quiz Service
cd MicroserviceTutorials/quiz-service
./mvnw spring-boot:run
```

### Verify Installation

1. **Service Registry**: http://localhost:8761
   - Should show registered services (API Gateway, Quiz Service)

2. **API Gateway**: http://localhost:8765/actuator/health
   - Should return `{"status":"UP"}`

3. **Quiz Service (via Gateway)**: http://localhost:8765/quiz-service/actuator/health

4. **OpenAPI Documentation**:
   - Gateway: http://localhost:8765/v3/api-docs
   - Quiz Service: http://localhost:8090/v3/api-docs
   - Swagger UI: http://localhost:8090/swagger-ui.html

## 📖 API Examples

### Create a Quiz

```bash
curl -X POST http://localhost:8765/quiz-service/quiz/create \
  -H "Content-Type: application/json" \
  -d '{
    "categoryName": "Java",
    "numQuestions": 5,
    "title": "Java Basics Quiz"
  }'
```

**Response**: `"Success"` (Status: 201)

### Get Quiz Questions

```bash
curl -X POST http://localhost:8765/quiz-service/quiz/get/1
```

**Response**:
```json
[
  {
    "id": 1,
    "questionTitle": "What is JVM?",
    "option1": "Java Virtual Machine",
    "option2": "Java Variable Method",
    "option3": "Java Verification Module",
    "option4": "None of the above"
  }
]
```

### Submit Quiz Answers

```bash
curl -X POST http://localhost:8765/quiz-service/quiz/submit/1 \
  -H "Content-Type: application/json" \
  -d '[
    {"id": 1, "response": "Java Virtual Machine"}
  ]'
```

**Response**: `5` (Score out of total questions)

## 🧪 Testing

```bash
# Run all tests from root
mvn test

# Run tests for specific service
cd MicroserviceTutorials/quiz-service
./mvnw test

# Run integration tests
mvn verify

# Generate test reports
mvn surefire-report:report
```

Test coverage includes:
- Unit tests for service layer logic
- Integration tests for REST endpoints
- Contract tests between Quiz and Question services
- Spring Boot context loading tests

## 📊 Observability & Monitoring

All services expose Actuator endpoints for health, metrics, and diagnostics:

```bash
# Health check
curl http://localhost:8090/actuator/health

# Prometheus metrics
curl http://localhost:8090/actuator/prometheus

# Application info
curl http://localhost:8090/actuator/info
```

### Future Observability Enhancements (TODO)

- **Distributed Tracing**: OpenTelemetry/Zipkin integration
- **Centralized Logging**: ELK Stack (Elasticsearch, Logstash, Kibana)
- **Metrics Dashboard**: Grafana with Prometheus data source
- **Alerting**: Prometheus Alertmanager rules

## 🔒 Security Considerations

### Current State
- Basic input validation with `@Valid` annotations
- Global exception handling for standardized error responses
- Security headers at gateway level (planned)

### Future Enhancements (TODO)
- **Authentication/Authorization**: OAuth2/JWT token validation
- **API Rate Limiting**: Redis-backed rate limiter at gateway
- **Secret Management**: Vault or Kubernetes secrets
- **HTTPS/TLS**: Certificate management for production
- **CORS Configuration**: Whitelist for allowed origins
- **Input Sanitization**: XSS and SQL injection prevention

See [SECURITY.md](./SECURITY.md) for vulnerability reporting.

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines on:
- Code style and conventions
- Pull request process
- Development workflow
- Testing requirements

By participating, you agree to abide by our [Code of Conduct](./CODE_OF_CONDUCT.md).

## 🗺️ Roadmap

### Near Term
- [ ] Complete Question Service implementation
- [ ] Add comprehensive integration tests
- [ ] Implement caching layer (Redis) for quiz questions
- [ ] Add API request/response validation
- [ ] Implement retry and circuit breaker patterns

### Mid Term
- [ ] User authentication and authorization (JWT/OAuth2)
- [ ] User profiles and quiz history tracking
- [ ] Leaderboards and ranking system
- [ ] Quiz timer and session management
- [ ] Adaptive difficulty based on user performance
- [ ] API rate limiting at gateway level

### Long Term
- [ ] Centralized configuration with Spring Cloud Config
- [ ] Distributed tracing (OpenTelemetry/Zipkin)
- [ ] Event-driven architecture with Kafka/RabbitMQ
- [ ] Canary deployments and blue-green deployment strategies
- [ ] Multi-tenancy support
- [ ] AI-powered question recommendations
- [ ] Real-time quiz multiplayer mode
- [ ] Analytics and reporting dashboard
- [ ] Mobile app (React Native/Flutter)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

## 🙏 Acknowledgments

- Built with Spring Boot and Spring Cloud ecosystem
- Service discovery powered by Netflix Eureka
- API documentation with Springdoc OpenAPI
- Containerization with Docker

## 📞 Contact

For questions, suggestions, or issues, please open an issue on GitHub or contact the maintainer.

---

**Note**: This is a portfolio/demonstration project showcasing microservices architecture patterns and modern DevOps practices. It is under active development.
