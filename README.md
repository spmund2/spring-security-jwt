# Spring Security JWT - Exception Handling

A Spring Boot application that demonstrates JWT (JSON Web Token) authentication with Spring Security and comprehensive exception handling using an in-memory H2 database.

## Overview

This project showcases a complete authentication and authorization system using JWT tokens with Spring Security. It includes role-based access control (RBAC), custom exception handling for security-related errors, and an H2 in-memory database for user management. Perfect for development and testing purposes.

## Features

- **JWT Authentication**: Token-based authentication using JJWT library
- **Role-Based Access Control (RBAC)**: Two roles - ADMIN and USER with different permission levels
- **Custom Exception Handling**: Global exception handler for security exceptions including:
  - BadCredentialsException (Invalid username/password)
  - AccessDeniedException (Insufficient permissions)
  - SignatureException (Invalid JWT signature)
  - ExpiredJwtException (Expired JWT tokens)
- **User Registration**: New user registration with password encoding
- **Product API**: Secure REST endpoints for product management
- **H2 In-Memory Database**: Embedded database for easy testing and development (no external DB setup required)
- **Database Persistence**: Spring Data JPA with automatic schema creation

## Technology Stack

- **Java Version**: 21
- **Framework**: Spring Boot 3.2.0
- **Security**: Spring Security
- **JWT**: JJWT (JSON Web Token) 0.11.5
- **Database**: H2 (In-Memory Database)
- **ORM**: Spring Data JPA / Hibernate
- **Build Tool**: Maven
- **Utility**: Project Lombok 1.18.30

## Project Structure

```
spring-security-jwt/
├── src/main/java/com/javatechie/
│   ├── SpringSecurityLatestApplication.java    # Main application class
│   ├── controller/
│   │   └── ProductController.java              # REST endpoints for auth & products
│   ├── service/
│   │   ├── JwtService.java                     # JWT token generation & validation
│   │   └── ProductService.java                 # Product business logic
│   ├── filter/
│   │   └── JwtAuthFilter.java                  # JWT authentication filter
│   ├── config/
│   │   ├── SecurityConfig.java                 # Spring Security configuration
│   │   ├── UserInfoUserDetails.java            # Custom UserDetails implementation
│   │   └── UserInfoUserDetailsService.java     # Custom UserDetailsService
│   ├── entity/
│   │   └── UserInfo.java                       # User entity
│   ├── dto/
│   │   ├── AuthRequest.java                    # Authentication request DTO
│   │   └── Product.java                        # Product DTO
│   ├── repository/
│   │   └── UserInfoRepository.java             # User database operations
│   └── advice/
│       └── CustomExceptionHandler.java         # Global exception handler
└── src/main/resources/
    └── application.properties                   # Application configuration
```

## API Endpoints

### Authentication Endpoints

- **POST** `/products/welcome` - Public endpoint (no authentication required)
  - Returns: Welcome message

- **POST** `/products/new` - Register a new user
  - Request Body: UserInfo object
  - Returns: Success/Failure message

- **POST** `/products/authenticate` - Authenticate user and get JWT token
  - Request Body: AuthRequest (username & password)
  - Returns: JWT token

### Protected Endpoints

- **GET** `/products/all` - Get all products (ADMIN role required)
  - Returns: List of all products

- **GET** `/products/{id}` - Get product by ID (USER role required)
  - Returns: Product object

## Configuration

### H2 Database Setup

H2 is automatically configured with the following settings in `application.properties`:

```properties
# H2 Database Configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# H2 Console (optional - for debugging)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA Configuration
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create-drop
```

**Key Points:**
- **In-Memory Database**: Data is stored in memory and lost on application restart
- **Auto Schema Creation**: Tables are automatically created on startup
- **H2 Console**: Access at `http://localhost:9191/h2-console` for database inspection
- **Default Credentials**: Username: `sa`, Password: (empty)

### JWT Secret

The JWT service uses a predefined secret key for token signing. This can be customized in `JwtService.java`:

```java
public static final String SECRET = "5367566B59703373367639792F423F4528482B4D6251655468576D5A71347437";
```

## Exception Handling

The application includes a global exception handler (`CustomExceptionHandler`) that handles:

1. **BadCredentialsException** - HTTP 401
   - Reason: Authentication Failure

2. **AccessDeniedException** - HTTP 403
   - Reason: Not authorized

3. **SignatureException** - HTTP 403
   - Reason: JWT Signature not valid

4. **ExpiredJwtException** - HTTP 403
   - Reason: JWT Token already expired

## Build & Run

### Prerequisites
- Java 21+
- Maven 3.6+
- No database installation required (H2 is embedded)

### Build

```bash
mvn clean build
```

### Run

```bash
mvn spring-boot:run
```

The application will start on `http://localhost:9191`

### Access H2 Console (Optional)

Once the application is running, you can access the H2 database console at:
```
http://localhost:9191/h2-console
```

- **JDBC URL**: `jdbc:h2:mem:testdb`
- **User Name**: `sa`
- **Password**: (leave empty)

## Usage Example

1. **Register a new user**:
   ```bash
   curl -X POST http://localhost:9191/products/new \
   -H "Content-Type: application/json" \
   -d '{"username":"testuser","password":"test123","email":"test@example.com","roles":"ROLE_USER"}'
   ```

2. **Authenticate and get JWT token**:
   ```bash
   curl -X POST http://localhost:9191/products/authenticate \
   -H "Content-Type: application/json" \
   -d '{"username":"testuser","password":"test123"}'
   ```

3. **Access protected endpoint with token**:
   ```bash
   curl -X GET http://localhost:9191/products/1 \
   -H "Authorization: Bearer <your-jwt-token>"
   ```

## Dependencies

- spring-boot-starter-security
- spring-boot-starter-web
- spring-boot-starter-data-jpa
- h2 (embedded database)
- jjwt-api, jjwt-impl, jjwt-jackson
- lombok
- spring-security-test

## Database Schema

The application automatically creates the following tables:

- **user_info**: Stores user credentials and roles
- **product**: Stores product information

## Getting Started

### Quick Start (5 minutes)

1. **Clone/Download the project**
   ```bash
   cd spring-security-jwt
   ```

2. **Build the project**
   ```bash
   mvn clean install
   ```

3. **Run the application**
   ```bash
   mvn spring-boot:run
   ```

4. **Test the API**
   - Visit: `http://localhost:9191/products/welcome`
   - Access H2 Console: `http://localhost:9191/h2-console`

### Sample Users

The application can be extended to seed default users. Consider adding:
- Admin User: username: `admin`, password: `admin123`, role: `ROLE_ADMIN`
- Regular User: username: `user`, password: `user123`, role: `ROLE_USER`

## Security Features

### Authentication Flow
1. User registers via `/products/new` endpoint
2. User authenticates via `/products/authenticate` endpoint with username/password
3. Server returns a JWT token valid for subsequent requests
4. Client includes token in `Authorization: Bearer <token>` header
5. Server validates token via `JwtAuthFilter` on each request

### JWT Token Details
- **Algorithm**: HS256 (HMAC SHA256)
- **Secret Key**: Configurable in `JwtService.java`
- **Claims**: Username (subject) and issue/expiration times
- **Scope**: Runtime configuration in `SecurityConfig.java`

### Role-Based Access Control
```
ADMIN Role: Can access /products/all (view all products)
USER Role:  Can access /products/{id} (view specific products)
PUBLIC:     Can access /products/welcome (no authentication needed)
```

## Troubleshooting

### Issue: Build fails with Lombok error
**Solution**: Ensure Java 21 is installed and pom.xml has Maven compiler plugin configured
```bash
java -version  # Should show Java 21
```

### Issue: H2 Console shows "Database Locked"
**Solution**: H2 in-memory database sometimes locks. Restart the application:
```bash
mvn spring-boot:run
```

### Issue: JWT Token Expired Error (HTTP 403)
**Solution**: Generate a new token:
```bash
curl -X POST http://localhost:9191/products/authenticate \
-H "Content-Type: application/json" \
-d '{"username":"testuser","password":"test123"}'
```

### Issue: AccessDeniedException (HTTP 403)
**Solution**: User doesn't have required role. Use admin account for `/products/all`

### Issue: NoSuchFieldError during compilation
**Solution**: This was fixed in the current version. Update to:
- Spring Boot 3.2.0+
- Lombok 1.18.30+
- Java 21

## Testing

### Unit Tests
Run existing tests:
```bash
mvn test
```

### Manual API Testing with Postman/cURL

**1. Welcome Endpoint (Public)**
```bash
curl -X GET http://localhost:9191/products/welcome
```

**2. Register New User**
```bash
curl -X POST http://localhost:9191/products/new \
-H "Content-Type: application/json" \
-d '{
  "username":"newuser",
  "password":"password123",
  "email":"newuser@example.com",
  "roles":"ROLE_USER"
}'
```

**3. Authenticate (Get Token)**
```bash
curl -X POST http://localhost:9191/products/authenticate \
-H "Content-Type: application/json" \
-d '{"username":"newuser","password":"password123"}'
```
Response: JWT token string

**4. Access Protected Endpoint (USER)**
```bash
curl -X GET http://localhost:9191/products/1 \
-H "Authorization: Bearer <your-jwt-token>"
```

**5. Access Admin Endpoint**
```bash
# First create an admin user with roles="ROLE_ADMIN"
curl -X GET http://localhost:9191/products/all \
-H "Authorization: Bearer <admin-jwt-token>"
```

### Database Inspection

Access H2 Console at `http://localhost:9191/h2-console`:
- Query tables: `SELECT * FROM user_info;`
- Check products: `SELECT * FROM product;`

## Project Highlights

✨ **Modern Stack**: Java 21 with Spring Boot 3.2.0
🔒 **Production-Ready Security**: JWT + Spring Security + Custom Exception Handling
💾 **Easy Development**: H2 in-memory database (no setup needed)
📝 **Well-Documented**: Comprehensive code comments and this README
🧪 **Test-Friendly**: Perfect for learning and testing JWT implementations

## Notes

- H2 database is in-memory and will be reset each time the application restarts
- Perfect for development, testing, and demos
- For production use, consider switching to MySQL or PostgreSQL
- Database schema is automatically created/dropped based on `ddl-auto=create-drop` setting
- JWT secret should be externalized to environment variables in production
- Always use HTTPS in production for JWT transmission

## Useful Links

- [Spring Security Documentation](https://spring.io/projects/spring-security)
- [JWT Introduction](https://tools.ietf.org/html/rfc7519)
- [H2 Database Console](https://www.h2database.com/html/features.html#console)
- [JJWT Documentation](https://github.com/jwtk/jjwt)

## Author

JavaTechie

## License

This project is open source and available for educational purposes.
