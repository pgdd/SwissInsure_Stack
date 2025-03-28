# SwissInsure – Swiss Insurance Platform 🇨🇭

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Target Audiences](#target-audiences)
- [Architecture](#architecture)
- [Infrastructure Diagram](#infrastructure-diagram)
- [Development Workflow](#development-workflow)
- [MVP Development Checklist](#mvp-development-checklist)
- [Setup Instructions](#setup-instructions)
- [License](#license)

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

#### Web Portal with GraphQL (Next.js in TypeScript)
- Provides GraphQL API endpoints directly from the Next.js application
- Serves both the SSR web portal and the React Native mobile client
- Server-Side Rendering (SSR) and Static Site Generation (SSG)
- Full internationalization (i18n) for French, German, and Italian

#### Mobile Application (React Native in TypeScript)
- Consumes the GraphQL API provided by the Next.js Web Portal
- Native, multilingual experience for policyholders

### 2. Back-End (Internal Dashboard)

#### Internal Dashboard (Java Spring Boot)
- Secure REST APIs for analytics, reporting, and operations
- OAuth2/OpenID Connect authentication (via Keycloak)
- Strict security measures (TLS/SSL, input validation)
- Exposes REST endpoints exclusively for the internal Next.js Monitoring Dashboard
- Receives and processes alert data from Alertmanager for real-time dashboard display

### 3. API-Accessible Dashboard (Monitoring)

#### Monitoring Dashboard (Next.js in TypeScript)
- Real-time usage, performance metrics, and alerts
- Consumes REST APIs exclusively from the Spring Boot backend
- Displays alert data processed by Spring Boot from Alertmanager
- Client-side rendering with effective caching
- Internal-only access for administrators and operations team

### 4. Data Integration Layer

#### API Gateway
- Handles routing between GraphQL and REST endpoints
- Provides consistent API experience across services
- Implements rate limiting and request validation

### 5. Shared Database

#### Database Schema
- USERS: Policyholder information
- POLICIES: Insurance policy details
- CLAIMS: Claims records and tracking
- USAGE_LOGS: User interaction tracking
- AUDIT: Compliance and security logs

#### Data Management Strategy
- PostgreSQL: Primary database for all transactional data
- Redis: Caching layer for frequently accessed data and session management
- Data synchronization handled through transaction logs and cache invalidation
- ORM implementation details:
  - Prisma (Next.js): For web and monitoring dashboard access
  - Hibernate (Spring Boot): For internal dashboard and business logic

### 6. Infrastructure & Deployment
- Docker containerization: Each service runs in its own container
- Kubernetes orchestration: Containers are deployed as pods in Kubernetes (a Pod is the smallest deployable unit in Kubernetes)
- Load balancing for traffic distribution across all pods
- GCP (Zurich region) deployment ensuring Swiss data residency compliance

### 7. Logging & Alerting
- ELK stack for centralized logging
- Prometheus with Alertmanager for proactive notifications
- Alertmanager writes alert data to shared PostgreSQL database
- Spring Boot Dashboard reads alert data from the same PostgreSQL database
- Bi-directional data flow enables real-time monitoring and historical analysis

### 8. Testing Strategy & TDD
- Test-Driven Development approach
- Comprehensive test coverage: unit, integration, end-to-end, API
- Performance and security testing integrated into CI/CD

## Development Workflow

### Git Flow Branching Strategy
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
                +------------------------------------------+
                |   Google Cloud Platform - Zurich Region  |
                +------------------------------------------+
                                    |
                                    v
                        +------------------------+
                        |   Kubernetes Cluster   |
                        +------------------------+
                                    |
                                    v
                +------------------------------------------+
                |          Ingress/Load Balancer           |
                +------------------------------------------+
                    /           |            |            \
                   v            v            v             v
    +---------------+  +---------------+  +---------------+  +---------------+
    |   Next.js Web |  | React Native  |  |  Spring Boot  |  |    Next.js    |
    | Portal + GraphQL| |    Client    |  | Dashboard Pod |  | Monitoring Pod|
    +---------------+  +---------------+  +---------------+  +---------------+
            |                  |                  |                  |
            | GraphQL          | Consumes         | REST API         | Consumes
            | API              | GraphQL          |                  | REST API
            |                  |                  |                  |
            +------------------+                  +------------------+
            |                                     |
            v                                     v
            +---------------+                     +---------------+
            |     Docker    |                     |     Docker    |
            |    Container  |                     |    Container  |
            +---------------+                     +---------------+
                    |                                     |
                    v                                     v
            +---------------+                     +---------------+
            |   PostgreSQL  |                     |   PostgreSQL  |
            |  StatefulSet  |                     |  StatefulSet  |
            +---------------+                     +---------------+
            |                                           ^
            |                                           |
            |                +---------------+          |
            +------------>  |     Redis        |<---------+
                            |    StatefulSet   |
                             +---------------+
                                    |
                                    v
           +---------------+                     +---------------+
           |   Prometheus  |                     |   ELK Stack   |
           +---------------+                     +---------------+
                    \                                   /
                     \                                 /
                      v                               v
                        +------------------------+
                        |     Alertmanager       |
                        +------------------------+
                                    |
                                    | Writes alert data
                                    v
                        +------------------------+
                        |   Shared PostgreSQL    |
                        |   (Alert Database)     |
                        +------------------------+
                                    ^
                                    | Reads alert data
                                    |
                        +------------------------+
                        |  Spring Boot Dashboard |
                        +------------------------+
                                    |
                                    | REST API
                                    v
                        +------------------------+
                        | Next.js Monitoring Pod |
                        +------------------------+

                +------------------------------------------+
                |              CI/CD Pipeline               |
                +------------------------------------------+
                                    |
                                    v
                        +------------------------+
                        |     Git Repository     |
                        +------------------------+
                                    |
                                    v
                        +------------------------+
                        |      Build & Test      |
                        +------------------------+
                                    |
                                    v
                        +------------------------+
                        |  Docker Image Creation |
                        +------------------------+
                                    |
                                    v
                        +------------------------+
                        |     Image Registry     |
                        +------------------------+
                                    |
                                    v
                        +------------------------+
                        |    Deployment to K8s   |
                        +------------------------+
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

## Setup Instructions

### Prerequisites
- Node.js 18.x or later
- Java 17 or later
- Docker and Docker Compose
- Google Cloud SDK
- kubectl

### Local Development Setup
```bash
# Clone the repository
git clone https://github.com/swissinsure/swissinsure-platform.git
cd swissinsure-platform

# Install dependencies for web portal
cd web-portal
npm install

# Install dependencies for mobile app
cd ../mobile-app
npm install

# Start local development environment
cd ..
docker-compose up -d
```

### Configuration
Create a `.env` file in each project directory using the provided `.env.example` templates.


## License
This project is licensed under the [LICENSE.md](LICENSE.md) file for details.