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

```
smart-campus-api/
├── pom.xml                          # Maven configuration
├── README.md                        # This file
├── VIDEO_SCRIPT.md                  # Video demonstration script
├── src/
│   └── main/
│       ├── java/com/smartcampus/
│       │   ├── config/
│       │   │   └── ApplicationConfig.java    # JAX-RS configuration
│       │   ├── model/
│       │   │   ├── Room.java        # Room entity
│       │   │   ├── Sensor.java      # Sensor entity
│       │   │   └── SensorReading.java        # Reading entity
│       │   ├── resource/
│       │   │   ├── DiscoveryResource.java    # API discovery
│       │   │   ├── RoomResource.java         # Room endpoints
│       │   │   ├── SensorResource.java       # Sensor endpoints
│       │   │   └── SensorReadingResource.java # Sub-resource for readings
│       │   ├── service/
│       │   │   ├── RoomService.java          # Room business logic
│       │   │   ├── SensorService.java        # Sensor business logic
│       │   │   └── SensorReadingService.java # Reading business logic
│       │   ├── exception/
│       │   │   ├── RoomNotEmptyException.java
│       │   │   ├── LinkedResourceNotFoundException.java
│       │   │   └── SensorUnavailableException.java
│       │   ├── mapper/
│       │   │   ├── RoomNotEmptyExceptionMapper.java      # 409
│       │   │   ├── LinkedResourceNotFoundExceptionMapper.java # 422
│       │   │   ├── SensorUnavailableExceptionMapper.java # 403
│       │   │   └── GlobalExceptionMapper.java            # 500
│       │   └── filter/
│       │       └── LoggingFilter.java  # Request/response logging
│       └── webapp/WEB-INF/
│           └── web.xml              # Servlet configuration
```

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
```

The WAR file will be created at `target/smart-campus-api.war`

---

## Deployment on GlassFish (NetBeans)

### Method 1: Using NetBeans IDE

1. **Open the project in NetBeans:**
   - File → Open Project → Select `smart-campus-api` folder

2. **Configure GlassFish Server:**
   - Services → Servers → Add GlassFish Server (if not already added)
   - Point to your GlassFish 7 installation

3. **Set Project Properties:**
   - Right-click project → Properties → Run
   - Server: Select your GlassFish server
   - Context Path: `/smart-campus-api`

4. **Run the Project:**
   - Right-click project → Run
   - Or press F6

5. **Access the API:**
   - The API will be available at: `http://localhost:8080/smart-campus-api/api/v1`

### Method 2: Manual Deployment

```bash
# Build the WAR file
mvn clean package

# Copy WAR to GlassFish autodeploy directory
cp target/smart-campus-api.war $GLASSFISH_HOME/glassfish/domains/domain1/autodeploy/

# Or use GlassFish admin console
# http://localhost:4848 → Applications → Deploy
```

---

## API Endpoints

Once deployed on GlassFish with context path `/smart-campus-api`:

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/smart-campus-api/api/v1` | GET | API Discovery |
| `/smart-campus-api/api/v1/health` | GET | Health Check |
| `/smart-campus-api/api/v1/rooms` | GET | List all rooms |
| `/smart-campus-api/api/v1/rooms` | POST | Create a new room |
| `/smart-campus-api/api/v1/rooms/{id}` | GET | Get a specific room |
| `/smart-campus-api/api/v1/rooms/{id}` | PUT | Update a room |
| `/smart-campus-api/api/v1/rooms/{id}` | DELETE | Delete a room |
| `/smart-campus-api/api/v1/sensors` | GET | List all sensors |
| `/smart-campus-api/api/v1/sensors?type=CO2` | GET | Filter sensors by type |
| `/smart-campus-api/api/v1/sensors` | POST | Create a new sensor |
| `/smart-campus-api/api/v1/sensors/{id}` | GET | Get a specific sensor |
| `/smart-campus-api/api/v1/sensors/{id}` | PUT | Update a sensor |
| `/smart-campus-api/api/v1/sensors/{id}` | DELETE | Delete a sensor |
| `/smart-campus-api/api/v1/sensors/{id}/readings` | GET | Get sensor readings |
| `/smart-campus-api/api/v1/sensors/{id}/readings` | POST | Add a reading |

---

## Sample curl Commands

### 1. Discovery Endpoint

```bash
# Get API discovery information
curl -X GET http://localhost:8080/smart-campus-api/api/v1 \
  -H "Accept: application/json"
```

**Expected Response:**
```json
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
```

### 2. Create a Room

```bash
# Create a new room
curl -X POST http://localhost:8080/smart-campus-api/api/v1/rooms \
  -H "Content-Type: application/json" \
  -d '{
    "id": "CONF-101",
    "name": "Conference Room A",
    "capacity": 20
  }'
```

**Expected Response (201 Created):**
```json
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
```

### 3. Create a Sensor (with room validation)

```bash
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
```

### 4. Filter Sensors by Type

```bash
# Get only CO2 sensors
curl -X GET "http://localhost:8080/smart-campus-api/api/v1/sensors?type=CO2" \
  -H "Accept: application/json"

# Get only Temperature sensors
curl -X GET "http://localhost:8080/smart-campus-api/api/v1/sensors?type=Temperature" \
  -H "Accept: application/json"
```

### 5. Add a Sensor Reading (Sub-Resource)

```bash
# Add a new reading to a sensor
curl -X POST http://localhost:8080/smart-campus-api/api/v1/sensors/TEMP-002/readings \
  -H "Content-Type: application/json" \
  -d '{
    "value": 23.5
  }'
```

**Expected Response (201 Created):**
```json
{
  "message": "Reading added successfully",
  "reading": {
    "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "timestamp": 1712345678901,
    "value": 23.5
  },
  "sensorCurrentValue": 23.5
}
```

---

## Error Handling Examples

### 409 Conflict - Room Not Empty

```bash
# Try to delete a room that still has sensors
curl -X DELETE http://localhost:8080/smart-campus-api/api/v1/rooms/LIB-301 \
  -H "Accept: application/json"
```

**Expected Response (409):**
```json
{
  "error": "Conflict",
  "statusCode": 409,
  "message": "Conflict: Room 'LIB-301' contains 2 active sensor(s). Remove all sensors before deleting this room.",
  "roomId": "LIB-301",
  "sensorCount": 2,
  "suggestedAction": "Remove all sensors from this room before attempting deletion."
}
```

### 422 Unprocessable Entity - Invalid Room Reference

```bash
# Try to create a sensor with non-existent room
curl -X POST http://localhost:8080/smart-campus-api/api/v1/sensors \
  -H "Content-Type: application/json" \
  -d '{
    "id": "TEMP-999",
    "type": "Temperature",
    "roomId": "NON-EXISTENT-ROOM"
  }'
```

**Expected Response (422):**
```json
{
  "error": "Unprocessable Entity",
  "statusCode": 422,
  "message": "Unprocessable Entity: The Room 'NON-EXISTENT-ROOM' referenced in field 'roomId' does not exist.",
  "field": "roomId",
  "rejectedValue": "NON-EXISTENT-ROOM",
  "resourceType": "Room"
}
```

### 403 Forbidden - Sensor in Maintenance

```bash
# First, put a sensor in maintenance mode
curl -X PUT http://localhost:8080/smart-campus-api/api/v1/sensors/TEMP-001 \
  -H "Content-Type: application/json" \
  -d '{
    "id": "TEMP-001",
    "type": "Temperature",
    "status": "MAINTENANCE",
    "currentValue": 22.5,
    "roomId": "LIB-301"
  }'

# Then try to add a reading
curl -X POST http://localhost:8080/smart-campus-api/api/v1/sensors/TEMP-001/readings \
  -H "Content-Type: application/json" \
  -d '{"value": 25.0}'
```

**Expected Response (403):**
```json
{
  "error": "Forbidden",
  "statusCode": 403,
  "message": "Forbidden: Sensor 'TEMP-001' is currently 'MAINTENANCE'. new readings operations are not allowed.",
  "sensorId": "TEMP-001",
  "currentStatus": "MAINTENANCE"
}
```

---

## Report Questions and Answers

### Part 1: Service Architecture & Setup

#### Q1: Explain the default lifecycle of a JAX-RS Resource class. Is a new instance instantiated for every incoming request, or does the runtime treat it as a singleton? Elaborate on how this architectural decision impacts the way you manage and synchronize your in-memory data structures.

**Answer:**

By default, JAX-RS resource classes are **request-scoped**, meaning a new instance is created for every incoming HTTP request. This is the standard lifecycle defined by the JAX-RS specification. When a request arrives, the JAX-RS runtime (Jersey in this case) instantiates a new resource class instance, injects any dependencies, processes the request, and then the instance is eligible for garbage collection.

However, the default behavior can be changed using the `@Singleton` annotation, which makes the resource class a singleton shared across all requests.

**Impact on In-Memory Data Management:**

This architectural decision has significant implications for thread safety:

1. **Concurrent Access:** Since multiple requests can arrive simultaneously, multiple resource instances may be processing requests concurrently. This means our in-memory data structures must be thread-safe.

2. **ConcurrentHashMap:** In this project, I use `ConcurrentHashMap` for storing rooms, sensors, and readings. This provides thread-safe operations without requiring explicit synchronization for simple operations like `get()` and `put()`.

3. **Compound Operations:** For compound operations (check-then-act patterns), I use synchronized blocks. For example, when adding a sensor to a room, we need to check if the room exists and then add the sensor ID atomically.

4. **Service Layer:** I implemented a service layer (`RoomService`, `SensorService`, `SensorReadingService`) that encapsulates all data access. These services are registered as singletons in the `ApplicationConfig`, ensuring consistent state across all requests.

5. **Race Condition Prevention:** The combination of request-scoped resources and singleton services with thread-safe data structures prevents race conditions while maintaining good performance.

#### Q2: Why is the provision of "Hypermedia" (links and navigation within responses) considered a hallmark of advanced RESTful design (HATEOAS)? How does this approach benefit client developers compared to static documentation?

**Answer:**

**HATEOAS (Hypermedia as the Engine of Application State)** is considered a hallmark of advanced RESTful design because it makes APIs self-discoverable and self-documenting. Instead of hardcoding URLs, clients can navigate the API dynamically by following links provided in responses.

**Benefits for Client Developers:**

1. **Loose Coupling:** Clients don't need to hardcode URLs. If the API changes its URL structure, clients continue to work as long as they follow the links provided.

2. **Self-Documentation:** The API tells clients what actions are available at each state.

3. **Discoverability:** New clients can explore the API starting from the root endpoint without reading extensive documentation.

4. **State Transitions:** Links indicate valid state transitions.

---

### Part 2: Room Management

#### Q3: When returning a list of rooms, what are the implications of returning only IDs versus returning the full room objects? Consider network bandwidth and client-side processing.

**Answer:**

**Returning Only IDs:**
- *Advantages:* Bandwidth efficient, fast response
- *Disadvantages:* Multiple requests needed (N+1 problem)

**Returning Full Objects:**
- *Advantages:* Single request, simpler clients
- *Disadvantages:* Bandwidth heavy, over-fetching

I chose to return full objects for simplicity in this coursework.

#### Q4: Is the DELETE operation idempotent in your implementation? Provide a detailed justification.

**Answer:**

**Yes, the DELETE operation is idempotent.**

An operation is idempotent if performing it multiple times has the same effect as performing it once.

- First DELETE: Resource is deleted (returns 204 No Content)
- Subsequent DELETEs: Resource is already gone (returns 404 Not Found)

The key insight is that while the HTTP status codes differ (204 vs 404), the **server state** after each request is the same - the room doesn't exist.

---

### Part 3: Sensor Operations & Linking

#### Q5: We explicitly use the @Consumes(MediaType.APPLICATION_JSON) annotation on the POST method. Explain the technical consequences if a client attempts to send data in a different format, such as text/plain or application/xml.

**Answer:**

When a resource method is annotated with `@Consumes(MediaType.APPLICATION_JSON)`, JAX-RS will only invoke that method if the request's `Content-Type` header matches the specified media type.

**Technical Consequences:**

1. **HTTP 415 Unsupported Media Type:** If a client sends `Content-Type: text/plain` or `Content-Type: application/xml` to a method that only accepts `application/json`, JAX-RS will return HTTP 415 Unsupported Media Type.

2. **Request Not Processed:** The resource method is never invoked. The JAX-RS runtime intercepts the request at the content negotiation phase.

3. **No Deserialization Attempted:** JAX-RS doesn't attempt to deserialize the body because it doesn't have a message body reader registered for that media type.

#### Q6: You implemented this filtering using @QueryParam. Contrast this with an alternative design where the type is part of the URL path (e.g., /api/v1/sensors/type/CO2). Why is the query parameter approach generally considered superior for filtering and searching collections?

**Answer:**

**Query Parameter Approach:** `GET /api/v1/sensors?type=CO2`
**Path Parameter Alternative:** `GET /api/v1/sensors/type/CO2`

**Why Query Parameters Are Superior:**

1. **Resource Identity vs. Query Semantics:** Path parameters identify a specific resource; query parameters specify how to filter a collection.

2. **Multiple Filters:** Query params easily support multiple filters: `?type=CO2&status=ACTIVE`

3. **Optional Filters:** Query params are naturally optional.

4. **RESTful Semantics:** `GET /sensors` always returns the sensors collection; query params modify how it's presented.

5. **Flexibility:** Easy to add new filter criteria without changing URL structure.

---

### Part 4: Sub-Resources

#### Q7: Discuss the architectural benefits of the Sub-Resource Locator pattern.

**Answer:**

**Architectural Benefits:**

1. **Separation of Concerns:** `SensorResource` handles sensor CRUD; `SensorReadingResource` handles reading operations.

2. **Code Organization:** Related operations are grouped in dedicated classes.

3. **Reduced Class Size:** Without sub-resource locators, `SensorResource` would need methods for all nested paths.

4. **Reusability:** Sub-resource classes can potentially be reused in different contexts.

5. **Path Context Inheritance:** Sub-resources automatically have access to parent path parameters.

6. **Independent Testing:** Each resource class can be unit tested independently.

---

### Part 5: Error Handling

#### Q8: Why is HTTP 422 often considered more semantically accurate than a standard 404 when the issue is a missing reference inside a valid JSON payload?

**Answer:**

HTTP 422 is more accurate because:
- The endpoint exists (`/api/v1/sensors`)
- The request body is syntactically valid JSON
- The problem is **semantic** - the `roomId` references a non-existent room

RFC 4918: "The 422 status code means the server understands the content type and syntax is correct, but was unable to process the contained instructions."

#### Q9: From a cybersecurity standpoint, explain the risks associated with exposing internal Java stack traces to external API consumers.

**Answer:**

**Security Risks:**

1. **Technology Stack Exposure:** Stack traces reveal frameworks, libraries, and versions used.

2. **Internal Package Structure:** Class names, method names, and line numbers are exposed.

3. **File System Structure:** Deployment directory structure may be revealed.

4. **Database Information:** SQL errors may expose table/column names.

5. **Targeted Exploits:** Attackers can use this information to craft exploits for known vulnerabilities.

**My Implementation:** The `GlobalExceptionMapper` returns a generic 500 response with no stack traces, while logging full details server-side.

#### Q10: Why is it advantageous to use JAX-RS filters for cross-cutting concerns like logging?

**Answer:**

**Advantages:**

1. **Centralized Logging:** All requests logged consistently in one place.

2. **No Code Duplication:** Don't need Logger statements in every resource method.

3. **Automatic Coverage:** All endpoints are logged without manual effort.

4. **Separation of Concerns:** Logging is separated from business logic.

5. **Easy to Modify:** Change log format in one place.

---

## Video Demonstration Script

See [VIDEO_SCRIPT.md](VIDEO_SCRIPT.md) for the complete 10-minute demonstration script with timestamps.

---

## Troubleshooting

### 404 Not Found Error

If you get a 404 error:

1. **Check the URL:** Make sure you're using the correct context path:
   - `http://localhost:8080/smart-campus-api/api/v1`

2. **Check GlassFish Console:**
   - Go to `http://localhost:4848`
   - Applications → Check if `smart-campus-api` is deployed

3. **Check Server Logs:**
   - `$GLASSFISH_HOME/glassfish/domains/domain1/logs/server.log`

4. **Rebuild and Redeploy:**
   ```bash
   mvn clean package
   # Redeploy the WAR file
   ```

---

## License

This project is submitted as coursework for 5COSC022W - Client-Server Architectures at the University of Westminster.

**Author:** Ahmet Miah (W2071541)  
**Date:** April 2026
