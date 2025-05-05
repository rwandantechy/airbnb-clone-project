# Airbnb Clone Project – Backend System

## About the Project

The Airbnb Clone Project is a full-stack web development initiative that simulates the core functionalities of Airbnb. The backend is built with scalability, performance, and security in mind, supporting user management, property listings, booking workflows, payments, and reviews. This project allows developers to engage in real-world practices including API design, database modeling, and CI/CD automation.

## Objective

To build a robust backend system that powers a rental platform similar to Airbnb, using modern tools and frameworks. The backend handles data storage, user interactions, secure transactions, and supports multiple frontend integrations.

## Project Goals

- Implement secure user registration, login, and profile management
- Create, update, retrieve, and delete property listings
- Enable booking, availability checks, and reservation management
- Integrate secure payment handling for transactions
- Allow user-generated reviews with moderation capabilities
- Optimize performance through indexing and caching strategies

## Feature Breakdown

**1. REST & GraphQL APIs**
- Documented using OpenAPI (Swagger)
- Built with Django REST Framework and GraphQL support

**2. Authentication & User Management**
- JWT-based authentication
- Secure endpoints: `/users/`, `/users/{id}/`

**3. Property Management**
- Endpoints: `/properties/`, `/properties/{id}/`
- Full CRUD support for property listings

**4. Booking System**
- Endpoints: `/bookings/`, `/bookings/{id}/`
- Availability checks, cancellation policies

**5. Payment Integration**
- Endpoint: `/payments/`
- Mock payment gateway with upgrade path to Stripe/PayPal

**6. Review System**
- Endpoints: `/reviews/`, `/reviews/{id}/`
- Star ratings, feedback, moderation

**7. Performance & Optimization**
- PostgreSQL with indexing and relationships
- Redis caching and Celery background jobs

## Technology Stack

| Component         | Technology                       |
|------------------|----------------------------------|
| Framework         | Django                           |
| API Layer         | Django REST Framework, GraphQL   |
| Database          | PostgreSQL                       |
| Caching           | Redis                            |
| Task Queue        | Celery                           |
| Containerization  | Docker                           |
| DevOps / CI/CD    | GitHub Actions                   |
| Testing           | Pytest, DRF Test Tools           |

## Team Roles

A well-structured development team ensures smooth delivery and product quality. Below are the core team roles based on Agile practices.

| Role                  | Responsibilities                                                                 |
|-----------------------|----------------------------------------------------------------------------------|
| Product Owner (PO)    | Defines product vision, manages backlog, ensures business alignment              |
| Business Analyst (BA) | Translates business needs into technical specifications                          |
| Project Manager (PM)  | Coordinates tasks, timelines, communication, and delivery                        |
| UI/UX Designer        | Designs user interfaces and user journeys for optimal experience                 |
| Software Architect    | Defines system architecture, tech stack, and enforces coding standards           |
| Backend Developer     | Builds core logic, APIs, and handles integrations and database interaction       |
| Frontend Developer    | (If applicable) Builds client-side interface and integrates APIs                 |
| QA Engineer           | Verifies functionality, runs manual and automated tests                          |
| Test Automation Eng.  | Builds automated testing frameworks and scripts                                  |
| DevOps Engineer       | Manages CI/CD pipelines, containerization, monitoring, and environment config    |

Note: For smaller teams, roles may overlap depending on team member expertise.

## Database Design

This project uses a relational database model to represent key components of the platform.

### Users
Represents both guests and hosts.

- `id`, `name`, `email`, `password_hash`, `is_host`

Relationships:
- A user can list multiple properties, make bookings, and write reviews

### Properties
Listings created by hosts.

- `id`, `user_id`, `title`, `description`, `location`

Relationships:
- Belongs to one user
- Has multiple bookings and reviews

### Bookings
Details of reservations.

- `id`, `user_id`, `property_id`, `start_date`, `end_date`

Relationships:
- A booking is made by one user for one property
- Has one associated payment

### Payments
Transaction details.

- `id`, `booking_id`, `amount`, `payment_date`, `status`

Relationships:
- Linked to one booking

### Reviews
User feedback on properties.

- `id`, `user_id`, `property_id`, `rating`, `comment`

Relationships:
- Written by one user for one property

**Entity Relationship Summary**:
- One-to-Many: User → Properties, Bookings, Reviews
- One-to-One: Booking → Payment
- Many-to-One: Bookings, Reviews → Property

## API Security

The backend applies best practices in API security:

**Authentication & Authorization**
- JWT tokens for secure user sessions
- Role-based access control (RBAC)

**Data Protection**
- Password hashing with `bcrypt`
- Input validation with Django serializers
- HTTPS enforced in production

**Rate Limiting**
- Throttle requests to protect against abuse

**Secure File Handling**
- Uploaded files are sanitized and access-controlled

**Logging & Monitoring**
- All auth errors and permission denials are logged
- Integration with monitoring tools like Sentry or Prometheus (planned)

**Security Testing**
- Includes test cases for security validations
- Future support for static code analysis in CI/CD

Note: Use `.env` files to securely store API keys and secrets.

## CI/CD Pipeline

The project follows a CI/CD pipeline to ensure consistent delivery and testing.

### Objectives
- Automatically run tests on push or PR
- Build Docker images for deployment
- Ensure high code quality and reliability

### Tools Used
- GitHub Actions for automation
- Docker & Docker Compose for containerization
- Pytest/Unittest for automated testing
- Black / Flake8 for linting

### Workflow Steps

1. **Code Push or PR**
   - GitHub Actions triggered
   - Run linter and tests

2. **Docker Build**
   - App containerized
   - Image pushed to registry

3. **Testing**
   - Automated tests validate the application

4. **Deploy**
   - Deployed to staging or production
   - Manual approval for production if needed

5. **Monitoring & Rollback**
   - Logs monitored
   - Rollback supported via container versions

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
