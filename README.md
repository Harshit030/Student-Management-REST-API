# Student Management REST API

A RESTful backend application built using **Java 21, Spring Boot, Spring Data JPA, Hibernate, and MySQL** to manage student records through CRUD operations, with support for both soft deletion and permanent deletion.

## 🚀 Features

- Create new student records
- Retrieve a student by ID
- Retrieve all active students
- Update existing student details
- Permanently delete student records
- Soft-delete student records without removing them from the database
- Integrate MySQL using Spring Data JPA and Hibernate
- Test REST API endpoints using Postman

## 🛠️ Tech Stack

| Technology      | Purpose                                   |
| --------------- | ----------------------------------------- |
| Java 21         | Programming language                      |
| Spring Boot     | Backend application framework             |
| Spring Data JPA | Database access and repository operations |
| Hibernate       | Object-relational mapping (ORM)           |
| MySQL           | Relational database                       |
| Maven           | Build and dependency management           |
| Postman         | API testing                               |

## 🏗️ Project Architecture

The application follows a layered architecture that separates request handling, business logic, database operations, and entity mapping.

```text
                 Postman / Client
                        |
                        v
              +-------------------+
              | StudentController |
              +-------------------+
                        |
                        v
              +-------------------+
              |   StudentService  |
              +-------------------+
                        |
                        v
              +-------------------+
              | StudentRepository |
              +-------------------+
                        |
                        v
              +-------------------+
              | Spring Data JPA   |
              |    / Hibernate    |
              +-------------------+
                        |
                        v
                 +------------+
                 |   MySQL    |
                 +------------+
```

### Application layers

- **Controller — `StudentController`:** Exposes REST endpoints, receives HTTP requests, and returns responses.
- **Service — `StudentService`:** Implements the business logic for creating, retrieving, updating, and deleting student records.
- **Repository — `StudentRepository`:** Extends `JpaRepository<Student, Long>` and provides database access, including queries for active students.
- **Entity — `Student`:** Maps student objects to database records using fields such as `id`, `name`, `age`, `email`, `rollNo`, `subject`, and `deleted`.

The application uses constructor-based dependency injection to connect the layers.

### Request flow

**Postman → Controller → Service → Repository → Hibernate/JPA → MySQL**

The database result travels back through the application layers and is returned to the client as an HTTP response.

## 🗄️ Database Configuration

The application uses MySQL with the following database:

- **Database name:** `student_crud_db`
- **ORM:** Hibernate
- **Persistence framework:** Spring Data JPA
- **Schema management:** `spring.jpa.hibernate.ddl-auto=update`
- **SQL logging:** Enabled for development and debugging

### Create the database

```sql
CREATE DATABASE student_crud_db;
```

Configure your MySQL connection in `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/student_crud_db
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

Set `DB_USERNAME` and `DB_PASSWORD` in your local environment to match your MySQL configuration. Property names and connection settings should be aligned with your existing project configuration.

**Security note:** Do not commit database passwords or other secrets to the repository.

## ⚙️ Getting Started

### Prerequisites

Install the following before running the application:

- Java JDK 21
- MySQL Server
- Git
- Maven or the Maven Wrapper included in the project
- Postman (optional, for API testing)

### 1. Clone the repository

```bash
git clone https://github.com/Harshit030/Student-Management-REST-API.git
```

### 2. Navigate to the project directory

```bash
cd Student-Management-REST-API
```

### 3. Configure MySQL

Create the `student_crud_db` database and configure the database connection properties.

### 4. Build the project

Using Maven:

```bash
mvn clean install
```

Or, if the Maven Wrapper is available:

```bash
./mvnw clean install
```

On Windows:

```bat
mvnw.cmd clean install
```

### 5. Run the application

Using Maven:

```bash
mvn spring-boot:run
```

Or, using the Maven Wrapper:

```bash
./mvnw spring-boot:run
```

On Windows:

```bat
mvnw.cmd spring-boot:run
```

The default Spring Boot URL is:

`http://localhost:8080`

The actual port depends on your application configuration.

## 🔗 REST API Endpoints

The base path for all student endpoints is `/api/students`.

| HTTP Method | Endpoint                            | Description                  |
| ----------- | ----------------------------------- | ---------------------------- |
| POST        | `/api/students/create`              | Create a student             |
| GET         | `/api/students/get?id={id}`         | Retrieve a student by ID     |
| GET         | `/api/students/getAll`              | Retrieve all active students |
| PUT         | `/api/students/update?id={id}`      | Update an existing student   |
| DELETE      | `/api/students/delete?id={id}`      | Permanently delete a student |
| PATCH       | `/api/students/delete-soft?id={id}` | Soft-delete a student        |

### 1. Create a student

**Request**

```http
POST /api/students/create
Content-Type: application/json
```

Example request body:

```json
{
  "name": "Rahul Sharma",
  "age": 21,
  "email": "rahul@example.com",
  "rollNo": 101,
  "subject": "Computer Science"
}
```

The Service layer sets `deleted` to `false` before saving the record. The example values are illustrative; use the actual entity fields and required request format.

### 2. Retrieve a student by ID

**Request**

```http
GET /api/students/get?id=1
```

Retrieves a student by ID, provided the record is active.

### 3. Retrieve all active students

**Request**

```http
GET /api/students/getAll
```

Returns students that have not been soft-deleted.

**Current behavior:** The endpoint returns HTTP `404 Not Found` when the active student list is empty.

### 4. Update a student

**Request**

```http
PUT /api/students/update?id=1
Content-Type: application/json
```

Example request body:

```json
{
  "name": "Rahul Sharma",
  "age": 22,
  "email": "rahul.updated@example.com",
  "rollNo": 101,
  "subject": "Information Technology"
}
```

Updates the supported fields of an existing active student. This endpoint is not intended as a general partial-update operation.

### 5. Permanently delete a student

**Request**

```http
DELETE /api/students/delete?id=1
```

Removes the student record from the database rather than simply marking it as deleted.

### 6. Soft-delete a student

**Request**

```http
PATCH /api/students/delete-soft?id=1
```

Sets the student's `deleted` field to `true`, preserving the database record while excluding it from active-student queries.

## 🔍 Soft Deletion Implementation

Soft deletion is implemented using a boolean field named `deleted` in the `Student` entity.

The repository includes these derived query methods:

```java
findByIdAndDeletedIsFalse(Long id)
findByDeletedIsFalse()
```

These methods allow the application to retrieve individual active students and lists of active students without including soft-deleted records.

The project supports two distinct deletion approaches:

- **Soft delete:** Marks the record as deleted while retaining it in the database.
- **Permanent delete:** Removes the record from the database.

## 🧪 Testing with Postman

Use Postman to test the API endpoints.

1. Start the Spring Boot application.
2. Set the request method and URL.
3. Add a JSON request body for create and update operations.
4. Send the request.
5. Inspect the response body and HTTP status code.
6. Verify the corresponding changes in MySQL when necessary.

Test the following scenarios:

- Create a student and retrieve the record.
- Retrieve all active students.
- Update an existing student.
- Soft-delete a student and confirm it is excluded from active queries.
- Permanently delete a student.
- Request a student that does not exist.
- Retrieve the active-student list when no active students remain.

## 📌 Implementation Notes

- Uses constructor-based dependency injection.
- Uses `JpaRepository` for standard persistence operations.
- Uses Hibernate for ORM and database interaction.
- Uses derived repository queries to filter soft-deleted students.
- Does not currently implement explicit request validation with `@Valid`.
- Does not currently implement a global exception handler.
- Returns `404 Not Found` when the active-student list is empty.
- Does not implement a general partial-update operation.

## 🎯 Learning Outcomes

This project demonstrates practical experience with:

- Developing RESTful APIs using Spring Boot.
- Implementing CRUD operations in a Java backend.
- Designing a layered application architecture.
- Using Spring Data JPA and Hibernate for database persistence.
- Integrating a Java application with MySQL.
- Implementing soft deletion and permanent deletion.
- Testing REST endpoints using Postman.

## 👨‍💻 Author

**Harshit**

- GitHub: [@Harshit030](https://github.com/Harshit030)
- Repository: [Student Management REST API](https://github.com/Harshit030/Student-Management-REST-API)
