# ClientServer

ClientServer coursework

# Smart Campus API

**Student:** Ahmet Miah (W2071541)  
**Module:** 5COSC022W - Client-Server Architectures  
**University:** University of Westminster

---

## Overview

The Smart Campus API is a RESTful web service built with **Jakarta EE 10 / JAX-RS 3.1 (Jersey)** for managing rooms and sensors across a university campus. It provides endpoints for room management, sensor registration, sensor readings tracking, and comprehensive error handling.

### Key Features

- **Room Management:** Create, read, update, and delete rooms with capacity tracking
- **Sensor Registration:** Register sensors with type, status, and room assignment
- **Sensor Readings:** Track historical readings for each sensor
- **Filtering:** Query sensors by type using query parameters
- **Error Handling:** Custom exceptions with appropriate HTTP status codes (409, 422, 403, 500)
- **Logging:** Request/response logging via JAX-RS filters
- **HATEOAS:** API discovery endpoint with resource links

---

## Technology Stack

- **Framework:** Jakarta RESTful Web Services 3.1 (JAX-RS) with Jersey 3.1
- **Language:** Java 11+
- **Build Tool:** Maven 3.8+
- **Server:** Eclipse GlassFish 7.0+ (or any Jakarta EE 10 compatible server)
- **JSON:** Jackson (via Jersey Media)
- **Data Storage:** In-memory ConcurrentHashMap (thread-safe)

---

## Project Structure
