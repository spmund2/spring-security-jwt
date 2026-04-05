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
