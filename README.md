# SwissInsure – Swiss Insurance Platform 🇨🇭

## Overview
SwissInsure is a comprehensive, secure, and scalable MVP designed specifically for the Swiss insurance market. The platform meets stringent Swiss regulatory requirements while providing an exceptional user experience through multilingual interfaces for both policyholders and administrators.

## Features

### Compliance & Security
- Full compliance with Swiss data protection laws (FADP/LPD), FINMA guidelines, and GDPR
- OAuth2/OpenID Connect (via Keycloak) for secure authentication
- Data encryption in transit and at rest
- Centralized logging using ELK stack with detailed audit trails
- Deployed on Google Cloud Platform (Zurich region) ensuring data residency

### Multi-Platform Experience
- React Native mobile app (TypeScript) for policyholders
- SSR-enabled Next.js web portal (TypeScript) with multilingual support (French, German, Italian)
- Java Spring Boot dashboard for internal analytics
- Next.js dashboard for real-time monitoring

### Data Management
- PostgreSQL database with transactional support and rollback capabilities
- Redis caching for performance optimization
- Robust data schema for users, policies, claims, logs, and auditing

### Infrastructure
- Containerized using Docker and orchestrated with Kubernetes
- CI/CD pipeline with strict testing and review requirements
- Disciplined Git Flow methodology
- Proactive alerting with Prometheus and Alertmanager

## Target Audiences

### Policyholders (B2C)
- Mobile app (React Native/TypeScript)
- Web portal (Next.js/TypeScript)
- Multilingual experience (French, German, Italian)
- SSR for optimal performance and improved SEO

### Internal Administrators
- Analytics dashboard (Java Spring Boot)
- Real-time monitoring dashboard (Next.js)
- Secure REST APIs for operations management

## Architecture

### 1. Front-End (B2C)

#### Web Portal (Next.js in TypeScript)
- GraphQL endpoints via Next.js API Routes
- Server-Side Rendering (SSR) and Static Site Generation (SSG)
- Full internationalization (i18n) for French, German, and Italian

#### Mobile Application (React Native in TypeScript)
- Consumes the same GraphQL APIs as the web portal
- Native, multilingual experience for policyholders

### 2. Back-End (Internal Dashboard)

#### Internal Dashboard (Java Spring Boot)
- Secure REST APIs for analytics, reporting, and operations
- OAuth2/OpenID Connect authentication (via Keycloak)
- Strict security measures (TLS/SSL, input validation)

### 3. API-Accessible Dashboard (Monitoring)

#### Monitoring Dashboard (Next.js in TypeScript)
- Real-time usage, performance metrics, and alerts
- Consumes REST and GraphQL APIs from Spring Boot backend
- Client-side rendering with effective caching

### 4. Shared Database

#### Database Schema
- USERS: Policyholder information
- POLICIES: Insurance policy details
- CLAIMS: Claims records and tracking
- USAGE_LOGS: User interaction tracking
- AUDIT: Compliance and security logs

#### Optimizations & Data Persistence
- PostgreSQL with full transactional support
- Indexing and partitioning for query optimization
- Redis caching for accelerated data access
- ORM tools (Prisma for Next.js, Hibernate for Spring Boot)

### 5. Infrastructure & Deployment
- Docker containerization
- Kubernetes orchestration
- Load balancing for traffic distribution
- GCP (Zurich region) deployment

### 6. Logging & Alerting
- ELK stack for centralized logging
- Prometheus with Alertmanager for proactive notifications

### 7. Testing Strategy & TDD
- Test-Driven Development approach
- Comprehensive test coverage: unit, integration, end-to-end, API
- Performance and security testing integrated into CI/CD

## Development Workflow – Git Flow

### Branching Strategy
- `master`: Stable, production-ready code
- `develop`: Integration branch for new features
- `feature/*`: Individual feature development
- `release/*`: Release preparation and final testing
- `hotfix/*`: Urgent production fixes

### Code Review & Merging
- Mandatory code reviews before merging
- CI/CD pipeline validation for all commits
- Only approved, tested code reaches the main branch
## Infrastructure Diagram

```code
+--------------------------------------+
| Google Cloud Platform - Zurich Region|
+--------------------------------------+
                 |
                 v
        +-------------------+
        | Kubernetes Cluster |
        +-------------------+
                 |
                 v
       +--------------------------+
       | Ingress/Load Balancer    |
       +--------------------------+
           /        |        |      \
          v         v        v       v
+----------------+ +----------------+ +------------------+ +------------------+
| Next.js Web    | | React Native   | | Spring Boot      | | Next.js Monitoring|
| Portal Pod     | | API Services   | | Dashboard Pod    | | Pod              |
+----------------+ +----------------+ +------------------+ +------------------+
          |             |              |               |
          v             v              v               v
+----------------+ +----------------+ +------------------+ +------------------+
| Docker Container| | Docker Container| | Docker Container | | Docker Container |
+----------------+ +----------------+ +------------------+ +------------------+
          |             |               |                |
          v             v               v                v
  +------------------+ +------------------+ +------------------+ +------------------+
  | PostgreSQL       | | Redis            | | PostgreSQL       | | Redis            |
  | StatefulSet      | | StatefulSet      | | StatefulSet      | | StatefulSet      |
  +------------------+ +------------------+ +------------------+ +------------------+
          |              |
          v              v
  +------------------+  +------------------+
  | Prometheus       |  | ELK Stack        |
  +------------------+  +------------------+
          |               |
          v               v
    +-------------------+
    | Alertmanager      |
    +-------------------+

+--------------------------------------+
| CI/CD Pipeline                      |
+--------------------------------------+
          |
          v
    +-------------+
    | Git Repository|
    +-------------+
          |
          v
    +-------------+
    | Build & Test |
    +-------------+
          |
          v
    +-------------------+
    | Docker Image Creation |
    +-------------------+
          |
          v
    +------------------+
    | Image Registry   |
    +------------------+
          |
          v
    +-----------------------+
    | Deployment to K8s     |
    +-----------------------+
          |
          v
    +-------------------+
    | Kubernetes Cluster |
    +-------------------+

```

## MVP Development Checklist

### 1. Infrastructure & DevOps Foundation
- [ ] Set up Google Cloud Platform environment (Zurich region)
- [ ] Configure network architecture and security groups
- [ ] Set up Kubernetes cluster on GCP
- [ ] Implement CI/CD pipeline with automated testing
- [ ] Configure blue-green deployment strategy
- [ ] Establish Git Flow branching strategy and repositories

### 2. Security & Compliance Framework
- [ ] Implement OAuth2/OpenID Connect with Keycloak
- [ ] Configure data encryption (in transit and at rest)
- [ ] Set up centralized logging (ELK stack)
- [ ] Configure Prometheus and Alertmanager
- [ ] Document compliance measures for FADP/LPD, FINMA, and GDPR

### 3. Database & Data Layer
- [ ] Design and implement database schema
- [ ] Set up PostgreSQL with transactional support
- [ ] Configure Redis caching layer
- [ ] Implement indexing and query optimization
- [ ] Integrate ORM tools (Prisma, Hibernate)
- [ ] Develop data backup and recovery procedures

### 4. Containerization & Service Mesh
- [ ] Containerize all services using Docker
- [ ] Configure Kubernetes service deployments
- [ ] Set up load balancer and ingress controllers
- [ ] Implement service discovery and communication
- [ ] Configure horizontal pod autoscaling

### 5. Back-End Services
- [ ] Initialize Spring Boot project for internal dashboard
- [ ] Develop secure REST APIs for analytics and operations
- [ ] Implement service layer business logic
- [ ] Write comprehensive unit and integration tests
- [ ] Set up API documentation with Swagger/OpenAPI

### 6. Front-End Web Portal
- [ ] Initialize Next.js project in TypeScript
- [ ] Implement SSR/SSG architecture
- [ ] Develop GraphQL endpoints via API Routes
- [ ] Configure i18n for French, German, and Italian
- [ ] Create responsive UI components with comprehensive test coverage

### 7. Mobile Application
- [ ] Set up React Native project in TypeScript
- [ ] Implement GraphQL client integration
- [ ] Develop offline-first capabilities
- [ ] Configure multilingual support
- [ ] Implement push notifications

### 8. Monitoring Dashboard
- [ ] Develop Next.js monitoring dashboard
- [ ] Integrate data visualization components
- [ ] Implement real-time metrics display
- [ ] Create alerting UI and notification system
- [ ] Test monitoring accuracy and performance

## Contributing
Please read our [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## License
This project is licensed under the [LICENSE.md](LICENSE.md) file for details.