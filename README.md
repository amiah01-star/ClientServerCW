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



---

## Build Instructions

### Prerequisites

- Java 11 or higher
- Maven 3.8 or higher
- Eclipse GlassFish 7.0+ (or any Jakarta EE 10 server)
- curl (for testing)
- Postman (optional, for video demonstration)

### Building the Project

```bash
# Navigate to the project directory
cd smart-campus-api

# Clean and compile
mvn clean compile

# Package as WAR file
mvn clean package

The WAR file will be created at target/smart-campus-api.war

Deployment on GlassFish (NetBeans)
Method 1: Using NetBeans IDE
Open the project in NetBeans:
File → Open Project → Select smart-campus-api folder
Configure GlassFish Server:
Services → Servers → Add GlassFish Server (if not already added)
Point to your GlassFish 7 installation
Set Project Properties:
Right-click project → Properties → Run
Server: Select your GlassFish server
Context Path: /smart-campus-api
Run the Project:
Right-click project → Run
Or press F6
Access the API:
The API will be available at: http://localhost:8080/smart-campus-api/api/v1
Method 2: Manual Deployment
# Build the WAR file
mvn clean package

# Copy WAR to GlassFish autodeploy directory
cp target/smart-campus-api.war $GLASSFISH_HOME/glassfish/domains/domain1/autodeploy/

# Or use GlassFish admin console
# http://localhost:4848 → Applications → Deploy
API Endpoints
| Endpoint                                         | Method | Description            |
| ------------------------------------------------ | ------ | ---------------------- |
| `/smart-campus-api/api/v1`                       | GET    | API Discovery          |
| `/smart-campus-api/api/v1/health`                | GET    | Health Check           |
| `/smart-campus-api/api/v1/rooms`                 | GET    | List all rooms         |
| `/smart-campus-api/api/v1/rooms`                 | POST   | Create a new room      |
| `/smart-campus-api/api/v1/rooms/{id}`            | GET    | Get a specific room    |
| `/smart-campus-api/api/v1/rooms/{id}`            | PUT    | Update a room          |
| `/smart-campus-api/api/v1/rooms/{id}`            | DELETE | Delete a room          |
| `/smart-campus-api/api/v1/sensors`               | GET    | List all sensors       |
| `/smart-campus-api/api/v1/sensors?type=CO2`      | GET    | Filter sensors by type |
| `/smart-campus-api/api/v1/sensors`               | POST   | Create a new sensor    |
| `/smart-campus-api/api/v1/sensors/{id}`          | GET    | Get a specific sensor  |
| `/smart-campus-api/api/v1/sensors/{id}`          | PUT    | Update a sensor        |
| `/smart-campus-api/api/v1/sensors/{id}`          | DELETE | Delete a sensor        |
| `/smart-campus-api/api/v1/sensors/{id}/readings` | GET    | Get sensor readings    |
| `/smart-campus-api/api/v1/sensors/{id}/readings` | POST   | Add a reading          |

Sample curl Commands
1. Discovery Endpoint

# Get API discovery information
curl -X GET http://localhost:8080/smart-campus-api/api/v1 \
 -H "Accept: application/json"

Expected Response:

{
  "name": "Smart Campus API",
  "version": "1.0.0",
  "basePath": "/api/v1",
  "status": "operational",
  "contact": {
    "name": "Ahmet Miah",
    "email": "w2071541@westminster.ac.uk",
    "studentId": "W2071541"
  },
  "_links": {
    "rooms": {"href": "/api/v1/rooms"},
    "sensors": {"href": "/api/v1/sensors"}
  }
}
2. Create a Room
# Create a new room
curl -X POST http://localhost:8080/smart-campus-api/api/v1/rooms \
 -H "Content-Type: application/json" \
 -d '{
   "id": "CONF-101",
   "name": "Conference Room A",
   "capacity": 20
 }'

Expected Response (201 Created):

{
  "message": "Room created successfully",
  "room": {
    "id": "CONF-101",
    "name": "Conference Room A",
    "capacity": 20,
    "sensorIds": []
  },
  "location": "http://localhost:8080/smart-campus-api/api/v1/rooms/CONF-101"
}
3. Create a Sensor (with room validation)
# Create a sensor linked to an existing room
curl -X POST http://localhost:8080/smart-campus-api/api/v1/sensors \
 -H "Content-Type: application/json" \
 -d '{
   "id": "TEMP-002",
   "type": "Temperature",
   "status": "ACTIVE",
   "currentValue": 22.0,
   "roomId": "CONF-101"
 }'
4. Filter Sensors by Type
# Get only CO2 sensors
curl -X GET "http://localhost:8080/smart-campus-api/api/v1/sensors?type=CO2" \
 -H
