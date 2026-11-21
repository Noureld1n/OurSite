<div align="center">

# 🧩 OurSite Backend

### The REST API that runs a company website: blog, careers, portfolio, team, and admin management.

A secure Spring Boot backend with JWT authentication, giving a company's admins one place to manage everything shown on the public site.

<br>

`☕ Java` &nbsp; `🍃 Spring Boot` &nbsp; `🔐 Spring Security + JWT` &nbsp; `🐬 MySQL` &nbsp; `📦 Maven`

<br>

## 🗺️ Quick Navigation

| | | |
|:---:|:---:|:---:|
| 🧭 [Overview](#-overview) | ✨ [Features](#-features) | 🧰 [Tech Stack](#-tech-stack) |
| 🏗️ [Architecture](#-architecture) | 🗄️ [Database](#-database) | 🚀 [Getting Started](#-getting-started) |
| 🔌 [API Modules](#-api-modules) | 🔐 [Security](#-security) | 📁 [Project Structure](#-project-structure) |
| 🔧 [Configuration](#-configuration) | 📦 [Build Commands](#-build-commands) | 📝 [Notes](#-notes) |

</div>

---

## 🧭 Overview

OurSite Backend powers the content and operations of a company website. Visitors read blog posts, browse the portfolio and team, apply for open positions, and send messages to the company. Admins manage all of it through a protected API.

- 📰 **Publish content:** blogs made of ordered parts (text and images), with reader comments.
- 💼 **Hire:** post job openings and receive applications with resumes.
- 🏆 **Showcase:** keep the portfolio and team pages up to date.
- 📬 **Stay in touch:** collect contact messages from visitors.
- 🔐 **Stay secure:** admin-only actions are protected with JWT authentication.

---

## ✨ Features

### 📰 Blog
- Blog posts with title, subtitle, tag, category, publish date, and main image
- Posts are built from **ordered parts**, each with its own text, type, and images
- Visitor **comments** on each post
- Deleting a post removes its parts, images, and comments automatically

### 💼 Careers and applications
- Job postings with description, requirements, type, salary, optional commission, working days and hours, location, and deadline
- Applicants submit name, photo, email, phone, a description, and a resume
- Applications are linked to the job they apply for

### 🏆 Portfolio and team
- Portfolio entries with title, subtitle, image, logo, and contract date
- Team member profiles with name, role, and photo

### 📬 Contact messages
- Visitors can send messages with their name, contact details, organization, organization type, and message content

### 🔐 Admin and accounts
- Admin accounts with active/inactive status
- Login with JWT, account profile updates
- Forgot-password and reset-password flow

---

## 🧰 Tech Stack

| Area | Technology |
|:-----|:-----------|
| ☕ Language | Java |
| 🍃 Framework | Spring Boot (REST, Spring Data JPA) |
| 🔐 Security | Spring Security, JWT |
| 🐬 Database | MySQL |
| 📦 Build tool | Maven |

---

## 🏗️ Architecture

The project follows a classic layered design, with DTOs separating the API from the database models:

```
 Client ──▶ Controller ──▶ Service ──▶ Repository ──▶ MySQL
              │  ▲
              ▼  │
        Request / Response DTOs
```

| Layer | Responsibility |
|:------|:---------------|
| `controller` | Handles HTTP requests and returns responses |
| `dto` | Request and response objects, so entities are never exposed directly |
| `service` | Business logic |
| `repository` | Data access with Spring Data JPA |
| `model` | JPA entities that map to the database tables |
| `security` | JWT filter and utilities, admin user details, security configuration |
| `projection` | Lightweight read-only views (for example, blog images) |

---

## 🗄️ Database

The schema is in `Smart_DLL.sql`, which creates the project's database and its 10 tables.

| Table | Purpose |
|:------|:--------|
| `blog` | Blog posts (title, subtitle, tag, category, publish date, main image) |
| `blog_part` | Ordered sections of a post (text, type, position) |
| `blog_image` | Images attached to a blog part |
| `sys_comment` | Visitor comments on a post |
| `career` | Job openings |
| `application` | Job applications, linked to a career |
| `sys_admin` | Admin accounts |
| `massage` | Contact messages from visitors |
| `portfolio` | Portfolio entries |
| `team_member` | Team member profiles |

### Relationships

```
blog ──< blog_part ──< blog_image
  └────< sys_comment

career ──< application
```

All child records are removed automatically when their parent is deleted (`ON DELETE CASCADE`). `sys_admin`, `massage`, `portfolio`, and `team_member` are standalone tables.

---

## 🚀 Getting Started

### Prerequisites

- Java (see `pom.xml` for the required version)
- Maven
- MySQL 8+

### 1. Create the database

```bash
mysql -u root -p < Smart_DLL.sql
```

> [!WARNING]
> The script starts with a `DROP DATABASE IF EXISTS` statement, so it **deletes any existing database with the same name**. Do not run it against a database you want to keep.

### 2. Configure the application

Open `src/main/resources/application.properties` and make sure the database connection matches your MySQL setup (see [Configuration](#-configuration)).

### 3. Run the application

```bash
mvn spring-boot:run
```

The API starts on `http://localhost:8080` by default.

---

## 🔌 API Modules

Each controller exposes the endpoints for one area of the site:

| Controller | Manages |
|:-----------|:--------|
| `AuthController` | Login, token issuing, forgot and reset password |
| `AccountController` | The logged-in admin's own account and profile |
| `AdminController` | Admin accounts |
| `BlogController` | Blog posts, their parts and images, and comments |
| `CareerController` | Job openings and job applications |
| `PortfolioController` | Portfolio entries |
| `TeamMemberController` | Team members |
| `MassageController` | Contact messages from visitors |

<!-- Add a table of exact routes (method, path, auth required) here, or link to API docs / a Postman collection. -->

---

## 🔐 Security

- **JWT authentication:** admins log in and receive a token, which is checked on every protected request by `JwtFilter`.
- **Admin accounts:** loaded through a custom `AdminDetailsService`, and an account can be switched inactive with `is_active`.
- **Password handling:** password encoding is configured in `PasswordConfig`.
- **Access rules:** which endpoints are public and which require a token are defined in `SecurityConfig`.

> [!NOTE]
> Never commit real secrets. Keep the JWT secret and database password out of version control, or load them from environment variables.

---

## 📁 Project Structure

```
.
├── src/
│   ├── main/
│   │   ├── java/com/smart/ourSite/
│   │   │   ├── controller/       # REST controllers
│   │   │   ├── dto/
│   │   │   │   ├── request/      # Incoming request objects
│   │   │   │   └── response/     # Outgoing response objects
│   │   │   ├── model/            # JPA entities
│   │   │   ├── projection/       # Read-only projections
│   │   │   ├── repository/       # Spring Data repositories
│   │   │   ├── security/         # JWT, security config, admin user details
│   │   │   ├── service/          # Business logic
│   │   │   └── OurSiteApplication.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/                     # Tests
├── Smart_DLL.sql                 # Database schema
└── pom.xml                       # Maven build file
```

---

## 🔧 Configuration

Settings live in `src/main/resources/application.properties`. At a minimum, the database connection must match your local MySQL:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/YOUR_DATABASE_NAME
spring.datasource.username=YOUR_DB_USER
spring.datasource.password=YOUR_DB_PASSWORD
```

The same file holds any other settings the app needs, such as the JWT secret and token lifetime. Check it for the exact property names.

---

## 📦 Build Commands

| Command | Description |
|:--------|:------------|
| `mvn spring-boot:run` | Run the application in development |
| `mvn clean package` | Build the runnable JAR into `target/` |
| `java -jar target/<artifact-name>.jar` | Run the built JAR |
| `mvn test` | Run the tests |

---

## 📝 Notes

- The contact messages table and classes are named `massage` throughout the code and database, and are documented under that name here for consistency.
- Uploaded files (blog images, resumes, photos) are stored as file paths or URLs in the database.

<div align="center">

<br>

**Built with ☕ and Spring Boot**

</div>
