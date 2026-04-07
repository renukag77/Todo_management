# Todo Management Application

A secure, full-featured Todo Management system built with Spring Boot 4, Spring Security 7, and JWT authentication. This application provides a robust REST API for managing todos with user authentication and authorization.

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Database Configuration](#database-configuration)
- [API Documentation](#api-documentation)
- [Project Structure](#project-structure)
- [Running the Application](#running-the-application)
- [Testing](#testing)
- [Security](#security)
- [Contributing](#contributing)
- [License](#license)

## ✨ Features

- **User Authentication & Authorization**
  - User registration with validation
  - JWT-based login authentication
  - Secure password encoding using BCrypt
  - Role-based access control (RBAC)

- **Todo Management**
  - Create, read, update, and delete todos
  - Assign todos to specific users
  - Track todo completion status
  - User-specific todo filtering

- **Security**
  - JWT token-based authentication
  - Spring Security integration
  - CORS configuration for cross-origin requests
  - Password encryption and validation
  - Role-based authorization

- **Error Handling**
  - Global exception handler
  - Custom exception types
  - Detailed error responses
  - Validation error messages

- **Database**
  - MySQL database with JPA/Hibernate ORM
  - Automatic schema generation via Hibernate DDL
  - Relationship mapping (Users, Roles, Todos)

## 🏗️ Architecture

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT (Web/Mobile)                      │
└────────────────────────┬────────────────────────────────────────┘
                         │ HTTP/REST
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Spring Boot Application                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │         Spring Security & JWT Authentication           │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌────────────────┐│   │
│  │  │ JWT Provider │  │ Auth Filter  │  │ Entry Point    ││   │
│  │  └──────────────┘  └──────────────┘  └────────────────┘│   │
│  └─────────────────────────────────────────────────────────┘   │
│                         ▲ Validates Token                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────┐       ┌──────────────────┐               │
│  │  Controllers     │       │  Services        │               │
│  ├──────────────────┤       ├──────────────────┤               │
│  │ AuthController   │──────▶│ AuthService      │               │
│  │ TodoController   │       │ TodoService      │               │
│  └──────────────────┘       └──────────────────┘               │
│                                   ▲                             │
│                                   │                             │
│  ┌──────────────────┐       ┌──────────────────┐               │
│  │  Repositories    │◀──────│   Entities       │               │
│  ├──────────────────┤       ├──────────────────┤               │
│  │ UserRepository   │       │ User             │               │
│  │ TodoRepository   │       │ Todo             │               │
│  │ RoleRepository   │       │ Role             │               │
│  └──────────────────┘       └──────────────────┘               │
│           ▲                                                      │
│           │ JPA/Hibernate                                       │
└───────────┼──────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     MySQL Database                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │
│  │  users   │  │  roles   │  │  todos   │  │ users_roles  │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Request Flow Diagram

```
Client Request
    │
    ▼
┌─────────────────────────┐
│ HTTP Request arrives    │
│ (POST /api/auth/login)  │
└───────┬─────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│ Spring DispatcherServlet            │
│ Route to appropriate Controller     │
└───────┬─────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│ AuthController.login()              │
│ Extract LoginDto from request body  │
└───────┬─────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│ AuthService.login()                 │
│ 1. Find user by username/email      │
│ 2. Validate password                │
│ 3. Generate JWT token               │
└───────┬─────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│ JwtTokenProvider.generateToken()    │
│ Create JWT with claims & expiry     │
└───────┬─────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────┐
│ Return JwtAuthResponse               │
│ { accessToken, tokenType: "Bearer" }│
└───────┬─────────────────────────────┘
        │
        ▼
  HTTP 200 Response
```

### Protected Endpoint Access Flow

```
Client sends request with Bearer Token
    │
    ▼
┌──────────────────────────────────┐
│ JwtAuthenticationFilter           │
│ Extract token from Authorization │
│ header                            │
└─────────┬────────────────────────┘
          │
          ▼
┌──────────────────────────────────┐
│ JwtTokenProvider.validateToken() │
│ - Verify signature               │
│ - Check expiration               │
│ - Extract username               │
└─────────┬────────────────────────┘
          │
      ┌───┴────┐
      │         │
   Valid    Invalid
      │         │
      ▼         ▼
   Process   Return 401
   Request   Unauthorized
```

## 🛠️ Tech Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| **Java Version** | Java | 23 |
| **Framework** | Spring Boot | 4.0.0-RC1 |
| **Security** | Spring Security | 7.0.0-RC2 |
| **Authentication** | JWT (JJWT) | 0.13.0 |
| **ORM** | Hibernate/JPA | 7.1.4 |
| **Database** | MySQL | 9.2+ |
| **Build Tool** | Maven | 3.8+ |
| **Additional Libraries** | Lombok | 1.18.42 |
| **Mapper** | ModelMapper | 3.2.5 |

## 📦 Prerequisites

- **Java 23+** - [Install Java](https://www.oracle.com/java/technologies/downloads/)
- **Maven 3.8+** - [Install Maven](https://maven.apache.org/install.html)
- **MySQL 8.0+** - [Install MySQL](https://dev.mysql.com/downloads/mysql/)
- **Git** - [Install Git](https://git-scm.com/downloads)
- **Postman or cURL** - For API testing (optional)

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/renukag77/Todo_management.git
cd Todo_management
```

### 2. Create MySQL Database

```bash
mysql -u root -p
```

```sql
CREATE DATABASE IF NOT EXISTS todo_management;
```

### 3. Update Database Configuration

Edit `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/todo_management
spring.datasource.username=root
spring.datasource.password=YOUR_PASSWORD
```

### 4. Build the Project

```bash
./mvnw clean install
```

## 🗄️ Database Configuration

### Database Schema

#### Users Table
```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    username VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);
```

#### Roles Table
```sql
CREATE TABLE roles (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) UNIQUE NOT NULL
);
```

#### Todos Table
```sql
CREATE TABLE todos (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    completed BOOLEAN DEFAULT FALSE,
    user_id BIGINT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

#### Users-Roles Mapping Table
```sql
CREATE TABLE users_roles (
    user_id BIGINT NOT NULL,
    role_id BIGINT NOT NULL,
    PRIMARY KEY (user_id, role_id),
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (role_id) REFERENCES roles(id)
);
```

### Entity Relationship Diagram (ERD)

```
┌────────────────┐
│     Users      │
├────────────────┤
│ id (PK)        │
│ name           │
│ username       │
│ email          │
│ password       │
└────────────────┘
        │
        │ Many-to-Many
        │
        ▼
┌────────────────────┐
│  Users_Roles       │
├────────────────────┤
│ user_id (FK)   ────┼─────┐
│ role_id (FK)   ────┼──┐  │
└────────────────────┘  │  │
        │               │  │
        │               │  │
        ▼               ▼  │
┌────────────────┐    │   │
│     Roles      │    │   │
├────────────────┤    │   │
│ id (PK)        │◀───┘   │
│ name           │        │
└────────────────┘        │
                          │
                          │ One-to-Many
                          │
                          ▼
                  ┌────────────────┐
                  │     Todos      │
                  ├────────────────┤
                  │ id (PK)        │
                  │ title          │
                  │ description    │
                  │ completed      │
                  │ user_id (FK)   │
                  └────────────────┘
```

## 📚 API Documentation

### Authentication Endpoints

#### 1. Register User

```bash
POST /api/auth/register
Content-Type: application/json

{
    "name": "John Doe",
    "username": "johndoe",
    "email": "john@example.com",
    "password": "SecurePass@123"
}
```

**Response (201 Created):**
```json
{
    "message": "User registered successfully!"
}
```

#### 2. Login User

```bash
POST /api/auth/login
Content-Type: application/json

{
    "usernameOrEmail": "johndoe",
    "password": "SecurePass@123"
}
```

**Response (200 OK):**
```json
{
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "tokenType": "Bearer"
}
```

### Todo Endpoints

#### 3. Get All Todos (Protected)

```bash
GET /api/todos
Authorization: Bearer <YOUR_JWT_TOKEN>
```

**Response (200 OK):**
```json
[
    {
        "id": 1,
        "title": "Buy groceries",
        "description": "Milk, eggs, bread",
        "completed": false
    }
]
```

#### 4. Create Todo (Protected)

```bash
POST /api/todos
Authorization: Bearer <YOUR_JWT_TOKEN>
Content-Type: application/json

{
    "title": "Complete project",
    "description": "Finish Spring Boot project",
    "completed": false
}
```

**Response (201 Created):**
```json
{
    "id": 2,
    "title": "Complete project",
    "description": "Finish Spring Boot project",
    "completed": false
}
```

#### 5. Update Todo (Protected)

```bash
PUT /api/todos/{id}
Authorization: Bearer <YOUR_JWT_TOKEN>
Content-Type: application/json

{
    "title": "Complete project review",
    "description": "Finish Spring Boot project and review code",
    "completed": true
}
```

#### 6. Delete Todo (Protected)

```bash
DELETE /api/todos/{id}
Authorization: Bearer <YOUR_JWT_TOKEN>
```

**Response (204 No Content)**

## 📁 Project Structure

```
todo-management-springboot4-springsecurity7-jwt/
│
├── src/
│   ├── main/
│   │   ├── java/net/javaguides/todo/
│   │   │   ├── TodoManagementApplication.java          # Main application entry point
│   │   │   │
│   │   │   ├── config/
│   │   │   │   └── SpringSecurityConfig.java           # Spring Security configuration
│   │   │   │
│   │   │   ├── controller/
│   │   │   │   ├── AuthController.java                 # Authentication endpoints
│   │   │   │   └── TodoController.java                 # Todo CRUD endpoints
│   │   │   │
│   │   │   ├── dto/
│   │   │   │   ├── LoginDto.java                       # Login request DTO
│   │   │   │   ├── RegisterDto.java                    # Registration request DTO
│   │   │   │   ├── JwtAuthResponse.java                # JWT response DTO
│   │   │   │   └── TodoDto.java                        # Todo response DTO
│   │   │   │
│   │   │   ├── entity/
│   │   │   │   ├── User.java                           # User entity with roles
│   │   │   │   ├── Todo.java                           # Todo entity
│   │   │   │   └── Role.java                           # Role entity
│   │   │   │
│   │   │   ├── exception/
│   │   │   │   ├── GlobalExceptionHandler.java         # Centralized exception handling
│   │   │   │   ├── ErrorDetails.java                   # Error response structure
│   │   │   │   ├── ResourceNotFoundException.java      # 404 exception
│   │   │   │   └── TodoAPIException.java               # Custom API exception
│   │   │   │
│   │   │   ├── repository/
│   │   │   │   ├── UserRepository.java                 # User data access
│   │   │   │   ├── TodoRepository.java                 # Todo data access
│   │   │   │   └── RoleRepository.java                 # Role data access
│   │   │   │
│   │   │   ├── security/
│   │   │   │   ├── JwtTokenProvider.java               # JWT token generation & validation
│   │   │   │   ├── JwtAuthenticationFilter.java        # JWT authentication filter
│   │   │   │   ├── JwtAuthenticationEntryPoint.java    # Unauthorized entry point
│   │   │   │   └── CustomUserDetailsService.java       # Custom user details service
│   │   │   │
│   │   │   ├── service/
│   │   │   │   ├── AuthService.java                    # Authentication service interface
│   │   │   │   ├── TodoService.java                    # Todo service interface
│   │   │   │   └── impl/
│   │   │   │       ├── AuthServiceImpl.java             # Auth service implementation
│   │   │   │       └── TodoServiceImpl.java             # Todo service implementation
│   │   │   │
│   │   │   └── utils/
│   │   │       └── PasswordEncoderImpl.java             # Password encoding utility
│   │   │
│   │   └── resources/
│   │       └── application.properties                  # Application configuration
│   │
│   └── test/
│       └── java/net/javaguides/todo/
│           └── TodoManagementApplicationTests.java     # Application tests
│
├── pom.xml                                              # Maven dependencies & build config
├── mvnw & mvnw.cmd                                      # Maven wrapper scripts
├── README.md                                            # This file
└── .gitignore                                           # Git ignore patterns
```

### Key Components Explanation

| Component | Purpose |
|-----------|---------|
| **Controllers** | Handle HTTP requests and route to services |
| **Services** | Business logic layer, processes data |
| **Repositories** | Database access layer using JPA |
| **Entities** | Database table mappings using JPA annotations |
| **DTOs** | Data Transfer Objects for request/response serialization |
| **Security** | JWT authentication, user details, authorization |
| **Exceptions** | Custom exception handling and error responses |

## ▶️ Running the Application

### 1. Start MySQL Server

```bash
# On macOS (using Homebrew)
brew services start mysql

# On Windows (using MySQL Shell)
MySQL Shell or MySQL Workbench

# On Linux
sudo systemctl start mysql
```

### 2. Run the Application

```bash
cd todo-management-springboot4-springsecurity7-jwt
./mvnw spring-boot:run
```

**Expected Output:**
```
2026-04-07T22:38:19.254+05:30  INFO 20183 --- [main] n.j.todo.TodoManagementApplication       
: Started TodoManagementApplication in 2.965 seconds

Application started on http://localhost:8080
```

### 3. Test the API

Use Postman, cURL, or any REST client:

```bash
# Register a user
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "username": "johndoe",
    "email": "john@example.com",
    "password": "SecurePass@123"
  }'

# Login
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "usernameOrEmail": "johndoe",
    "password": "SecurePass@123"
  }'

# Get all todos (replace TOKEN with your JWT)
curl -X GET http://localhost:8080/api/todos \
  -H "Authorization: Bearer TOKEN"
```

## 🧪 Testing

### Run Unit Tests

```bash
./mvnw test
```

### Run Integration Tests

```bash
./mvnw verify
```

### Test Coverage

```bash
./mvnw jacoco:report
```

## 🔒 Security Features

### JWT Authentication
- Token expiration: 7 days (configurable)
- Signed with HS256 algorithm
- Contains user roles and claims
- Validated on every protected request

### Password Security
- BCrypt hashing with salt
- Minimum complexity requirements
- Never stored in plain text

### Authorization
- Role-based access control
- Custom user details service
- Secure endpoints with @PreAuthorize

### CORS Configuration
- Allows cross-origin requests from authorized domains
- Configurable in SpringSecurityConfig

## 📝 Application Properties

```properties
# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/todo_management
spring.datasource.username=root
spring.datasource.password=Mysql@123

# JPA/Hibernate Configuration
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update

# JWT Configuration
app.jwt-secret=daf66e01593f61a15b857cf433aae03a005812b31234e149036bcc8dee755dbb
app.jwt-expiration-milliseconds=604800000

# Server Configuration
server.port=8080
spring.application.name=todo-management
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👨‍💻 Author

Created with ❤️ by [Your Name]

## 🙏 Acknowledgments

- Spring Boot documentation
- Spring Security guides
- JWT (jjwt) library
- Hibernate ORM documentation

---

**For support, email: support@example.com or create an issue in the repository.**

