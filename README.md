# Horse Market

A REST API for a horse marketplace, built with Spring Boot. Users can register, log in, and manage horse listings with search and filtering capabilities.

## Tech Stack

- **Java 25** / **Spring Boot 4.0.6**
- **Spring Security** with JWT authentication (jjwt 0.12.6)
- **Spring Data JPA** with PostgreSQL
- **Lombok**
- **Docker** / **Docker Compose**
- **Jenkins** CI/CD

## Project Structure

```
horse_market/
├── api/                          # Spring Boot application
│   ├── src/main/java/com/gen_4/horse_market/
│   │   ├── models/               # JPA entities (User, Role, Horse)
│   │   ├── controllers/          # REST controllers
│   │   ├── services/             # Business logic
│   │   ├── repositories/         # Data access
│   │   ├── configuration/        # Security, JWT, app config
│   │   ├── dtos/                 # Request/response DTOs
│   │   └── exceptions/           # Custom exceptions & global handler
│   ├── src/test/
│   ├── Dockerfile
│   ├── dev-docker-compose.yml
│   └── pom.xml
├── Jenkinsfile
└── LICENSE
```

## Getting Started

### Prerequisites

- Java 25+
- Maven 3.9+
- Docker & Docker Compose

### Running the Database

Start the PostgreSQL container:

```bash
cd api
docker compose -f dev-docker-compose.yml up -d
```

### Running the Application

```bash
cd api
./mvnw spring-boot:run
```

The API will be available at `http://localhost:8080/api/0.1.0`.

### Running Tests

```bash
cd api
./mvnw test
```

## API Endpoints

All endpoints are served under the context path `/api/0.1.0`.

### Authentication

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/auth/register` | Public | Register a new user |
| `POST` | `/auth/login` | Public | Log in and receive a JWT |
| `POST` | `/auth/login-with-token` | JWT | Re-authenticate with existing token |

**Request body** for register/login:
```json
{ "username": "string", "password": "string" }
```

**Response** returns `{ "token": "jwt...", "user": { ... } }`.

### Horse Catalog

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/horse` | USER | Create a horse listing |
| `DELETE` | `/horse/{horseId}` | USER | Delete own horse listing |
| `GET` | `/horses` | USER | Search horses (paginated) |

**Create horse** body:
```json
{ "name": "string", "description": "string", "height": 1.5, "weight": 450.0, "isActive": true }
```

**Search** supports query parameters: `minWeight`, `maxWeight`, `minHeight`, `maxHeight`, `description` (full-text search), and pagination via `page`, `size`, `sort`.

### Admin

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/admin/users` | ADMIN | List users (not yet implemented) |

## Authentication

Requests to protected endpoints must include the JWT token in the header:

```
Authorization: Bearer <token>
```

Tokens are valid for **2 hours** and encode the user ID, username, and roles.

## Authorization

- **USER** role: access to horse catalog endpoints (create, delete own, search)
- **ADMIN** role: access to admin endpoints

## Environment Profiles

| Profile | Database | DDL Strategy | Notes |
|---------|----------|-------------|-------|
| `dev` | Local PostgreSQL (`localhost:5432`) | `update` | Debug logging enabled |
| `prod` | External PostgreSQL (env vars) | `validate` | Requires env vars: `MARKET_HOST`, `MARKET_SECRET_KEY`, `MARKET_DB_HOST`, `MARKET_DB_PORT`, `MARKET_DB_NAME`, `MARKET_DB_USER`, `MARKET_DB_PASS` |
| `test` | H2 in-memory | `create-drop` | Used for unit tests |

## License

[MIT](LICENSE)
