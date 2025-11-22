# Cloud-Cashflow
Multi-tenant Expense &amp; Billing Platform

# Cloud CashFlow – Expense & Billing Platform

Cloud CashFlow is a cloud-native, multi-tenant expense and billing platform built with Java, Spring Boot, and Azure.  
It demonstrates a realistic backend system with microservices, secure APIs, containerization, CI/CD, and Azure deployment.

---

## 1. High-Level Overview

**Goal:** Track business expenses, group them into invoices, and manage the invoice lifecycle (draft → sent → paid/overdue).

Core capabilities:

- User registration and login with JWT-based authentication.
- Role-based access control (`ADMIN`, `BUSINESS_OWNER`, `ACCOUNTANT`).
- Expense management (CRUD, filtering, aggregation).
- Invoice generation from expenses for a client and date range.
- Invoice lifecycle: `DRAFT`, `SENT`, `PAID`, `OVERDUE`.
- Optional notifications when invoices are generated, sent, or become overdue.
- Azure-hosted infrastructure: Azure SQL, AKS, Dockerized microservices.

---

## 2. Architecture

### Services

- **Auth Service**
  - Handles user registration, login, and token issuance.
  - Issues JWT access tokens used by other services.
  - Stores users and roles in Azure SQL.

- **Expense Service**
  - Stores and manages expenses for each user/tenant.
  - Provides filters (date range, client, category, project).
  - Exposes aggregation endpoints (e.g., totals by month/category).

- **Billing Service**
  - Generates invoices from billable expenses.
  - Tracks invoice status: `DRAFT`, `SENT`, `PAID`, `OVERDUE`.
  - Optionally publishes invoice events for notifications.

- **Notification Service (optional)**
  - Listens to invoice events (generated, sent, overdue).
  - Sends email notifications (or logs in dev mode).

### Data Flow (Simplified)

1. User registers and logs in via **Auth Service** → receives JWT.
2. User creates expenses via **Expense Service** (authorized with JWT).
3. User generates an invoice via **Billing Service**, which fetches relevant expenses.
4. Billing Service persists invoice and updates status over time.
5. Optionally, Notification Service sends emails on invoice events.

---

## 3. Tech Stack

**Languages & Frameworks**

- Java 17
- Spring Boot 3.3.x
- Spring Web, Spring Data JPA
- Spring Security (JWT / OAuth2 Resource Server)
- JUnit 5, Testcontainers

**Cloud & Infra**

- Azure Kubernetes Service (AKS)
- Azure SQL Database
- Azure Key Vault (optional – for secrets)
- Azure Service Bus or Kafka (optional – for events)

**Other**

- Maven (multi-module)
- Docker
- OpenAPI/Swagger (springdoc-openapi)
- Flyway or Liquibase for DB migrations
- Lombok (optional)

---

## 4. Repository Structure

```text
cloud-cashflow/
  pom.xml                      # parent pom
  auth-service/
    pom.xml
    src/main/java/...
    src/main/resources/...
  expense-service/
    pom.xml
    src/main/java/...
    src/main/resources/...
  billing-service/
    pom.xml
    src/main/java/...
    src/main/resources/...
  notification-service/        # optional
    pom.xml
    src/main/java/...
    src/main/resources/...
  k8s/                         # Kubernetes manifests
    auth-deployment.yaml
    expense-deployment.yaml
    billing-deployment.yaml
    notification-deployment.yaml
    ingress.yaml
  docs/
    architecture-diagram.png   # (optional)
    api-specs.md               # (optional, detailed contracts)
  docker-compose.yml           # local dev (DB + services if needed)
  README.md
