# Airbnb Clone Project – Backend System

## About the Project

The Airbnb Clone Project is a comprehensive, full-stack web application that replicates the core functionalities of Airbnb. This backend system is developed to handle user authentication, property listings, booking workflows, payments, and reviews. It is designed with scalability, performance, and security in mind, making it ideal for hands-on experience in real-world development.

## Objective

To develop a robust and secure backend system using modern web technologies that supports dynamic rental operations and can integrate with frontend interfaces. The system is containerized for easy deployment and automated through CI/CD pipelines.

## Project Goals

- Implement secure user registration, login, and role-based access
- Provide CRUD functionality for property listings
- Enable a complete booking management system
- Integrate payment processing for transactions
- Support user reviews and rating submissions
- Optimize performance with caching and database indexing
- Automate testing and deployment using CI/CD practices

---

## Feature Breakdown

1. **REST & GraphQL APIs**
   - RESTful endpoints implemented with Django REST Framework
   - GraphQL API layer for flexible querying
   - OpenAPI (Swagger) documentation support

2. **Authentication & User Management**
   - JWT-based authentication
   - Role-based access for guests, hosts, and admins
   - Secure profile update endpoints

3. **Property Management**
   - Hosts can create, update, delete, and retrieve listings
   - Property endpoints: `/properties/`, `/properties/{id}/`

4. **Booking System**
   - Users can make, update, cancel bookings
   - Booking endpoints: `/bookings/`, `/bookings/{id}/`

5. **Payment Integration**
   - Mock payment service with potential to integrate Stripe
   - Endpoint: `/payments/`

6. **Review System**
   - Users can rate and review properties
   - Review endpoints: `/reviews/`, `/reviews/{id}/`

7. **Performance Optimization**
   - PostgreSQL indexing
   - Redis caching for frequently queried data
   - Celery for background task execution

---

## Technology Stack

| Component         | Technology                       | Purpose                                                                 |
|------------------|----------------------------------|-------------------------------------------------------------------------|
| Framework         | Django                           | Server-side logic and MVC architecture                                 |
| API Layer         | Django REST Framework, GraphQL   | RESTful APIs and flexible query support                                |
| Database          | PostgreSQL                       | Relational data storage with indexing and constraints                  |
| Caching           | Redis                            | Reduces DB load and speeds up responses                                |
| Task Queue        | Celery                           | Handles background tasks like email notifications                      |
| Containerization  | Docker, Docker Compose           | Enables consistent dev and deployment environments                     |
| CI/CD             | GitHub Actions                   | Automates testing, building, and deployment workflows                  |
| Testing           | Pytest, DRF Test Tools           | Ensures code correctness and regression prevention                     |
| Code Quality      | Black, Flake8                    | Enforces consistent formatting and linting                             |

---

## Team Roles

This project follows an Agile team structure based on real-world software engineering models.

| Role                  | Responsibilities                                                                 |
|-----------------------|----------------------------------------------------------------------------------|
| Product Owner (PO)    | Defines product vision and priorities, manages the product backlog               |
| Business Analyst (BA) | Converts business needs into technical specs, works closely with the PO          |
| Project Manager (PM)  | Oversees timelines, communication, risk mitigation, and team coordination        |
| UI/UX Designer        | Designs intuitive user flows, wireframes, and interactive UI elements            |
| Software Architect    | Determines overall system structure, tech stack, and coding standards            |
| Backend Developer     | Builds APIs, integrates services, manages database logic                         |
| Frontend Developer    | (If applicable) Develops user interface and connects to APIs                     |
| QA Engineer           | Conducts manual and automated tests to verify system quality                     |
| Test Automation Eng.  | Develops and maintains test automation scripts and frameworks                    |
| DevOps Engineer       | Sets up CI/CD pipelines, Docker infrastructure, and monitoring tools             |

---

## Database Design

A relational schema is used to model all entities required for the system.

### Users

Represents both guests and hosts.

- `id` (Primary Key)
- `name`
- `email` (unique)
- `password_hash`
- `is_host` (Boolean)

**Relationships:**
- One user can list many properties
- One user can make many bookings
- One user can leave many reviews

### Properties

Represents listings created by hosts.

- `id`
- `user_id` (FK to Users)
- `title`
- `description`
- `location`

**Relationships:**
- Each property belongs to one host
- A property can have many bookings and reviews

### Bookings

Represents a reservation for a property.

- `id`
- `user_id` (FK to Users)
- `property_id` (FK to Properties)
- `start_date`
- `end_date`

**Relationships:**
- A booking belongs to one user and one property
- A booking has one associated payment

### Payments

Handles booking transactions.

- `id`
- `booking_id` (FK to Bookings)
- `amount`
- `payment_date`
- `status` (e.g., completed, pending)

**Relationships:**
- One-to-one with Booking

### Reviews

Captures user feedback on properties.

- `id`
- `user_id` (FK to Users)
- `property_id` (FK to Properties)
- `rating` (1-5)
- `comment`

**Relationships:**
- One user → many reviews
- One property → many reviews

---

## API Security

The backend applies several security measures to protect user data and prevent unauthorized access.

### Authentication & Authorization

- JWT tokens required for all protected routes
- Role-based access control (RBAC) for different user types

### Data Protection

- Passwords hashed with `bcrypt`
- Server-side validation for all input
- Enforced HTTPS in production

### Rate Limiting & Throttling

- Limits number of requests per IP to prevent abuse
- Implemented using Django REST Framework throttle classes

### Secure File Handling

- Files scanned and access URLs are tokenized or time-limited

### Logging & Monitoring

- Tracks failed logins, permission denials, and suspicious behavior
- Supports integration with tools like Sentry and Prometheus

### Security Testing

- Test cases include permission checks and invalid input
- Static code analysis planned via CI workflows

**Note:** Use `.env` files to secure credentials and secret keys.

---

## CI/CD Pipeline

### Overview

This project uses a Continuous Integration and Continuous Deployment pipeline to automate testing and deployment, increase development velocity, and reduce human error.

### Benefits of CI/CD

- Faster development and deployment cycles
- Reliable test automation for consistent quality
- Streamlined collaboration across environments

### Tools Used

- **GitHub Actions** – For automated workflows
- **Docker** – For packaging and environment consistency
- **Docker Compose** – For managing multi-container dev/staging setups
- **Black / Flake8** – For code formatting and linting
- **Pytest / unittest** – For automated testing

### Workflow Summary

1. **Push or PR Event**
   - Triggers linting and unit tests
2. **Docker Build**
   - Creates a container image for the app
3. **Run Tests**
   - Executes tests inside Docker
4. **Deployment**
   - Deploys to staging or production after build success
5. **Monitoring & Rollback**
   - Supports version rollback and error alerts

### Sample GitHub Actions Workflow

```yaml
name: Django CI/CD

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
          POSTGRES_DB: airbnb
        ports: [ '5432:5432' ]
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
    - uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Run tests
      run: |
        python manage.py test
