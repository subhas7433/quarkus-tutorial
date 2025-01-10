**Module 2: Building RESTful APIs with JAX-RS and RESTEasy (8-12 hours)**

**Goal:** To understand the principles of RESTful APIs and learn how to implement them in Quarkus using JAX-RS and RESTEasy. By the end of this module, students will be able to create, test, and understand various aspects of RESTful services.

**2.1 Introduction to RESTful Web Services (1 - 1.5 hours)**

*   **What is an API?**
    *   API stands for Application Programming Interface.
    *   Think of it as a menu for an application, allowing other applications to interact with it.
    *   In web development, APIs are a way for applications to communicate with each other over the internet.
*   **What is REST?**
    *   Representational State Transfer (REST) is an architectural style for building APIs.
    *   It's a set of guidelines, not a strict rulebook.
    *   It’s all about using HTTP in the right way.
*   **Key Principles of REST:**
    *   **Client-Server:** Clear separation between the client (e.g., a web browser) and the server (where the data lives).
    *   **Stateless:** The server doesn’t remember the client from one request to the next (each request is independent).
    *   **Cacheable:** Clients can cache responses to improve performance.
    *   **Layered System:** Allows intermediate layers like proxies or load balancers.
    *   **Uniform Interface:** Consistent way of interacting with the server using HTTP methods, URIs, and representations.
        *   **Resource Identification:** Resources are identified using URIs (Uniform Resource Identifiers) such as `/products/123`.
        *   **Manipulation of Resources:** Resources are manipulated using standard HTTP methods (GET, POST, PUT, DELETE).
        *   **Self-Descriptive Messages:** Response messages should contain enough information for the client to understand them.
        *   **HATEOAS (Hypermedia as the Engine of Application State):** Optional but helpful - responses include links to other related resources, allowing the client to navigate the API.
*   **HTTP Methods and Their Purpose:**
    *   **GET:** Retrieve data from a resource (e.g., getting details of a product).
    *   **POST:** Create a new resource (e.g., adding a new product).
    *   **PUT:** Update an existing resource (e.g., modifying product details).
    *   **DELETE:** Remove a resource (e.g., deleting a product).
    *   (Brief mention of other methods like PATCH, OPTIONS, HEAD)
*   **Resource Representation:**
    *   Data formats (e.g., JSON, XML) used to send and receive information.
    *   Why JSON is a common choice for web APIs.
*   **HTTP Status Codes:**
    *   Meaning of status codes in response headers.
        *   `200 OK`: Success
        *   `201 Created`: Resource created successfully.
        *   `204 No Content`: Request was successful, but there's no response data.
        *   `400 Bad Request`: Client sent something the server can't process.
        *   `401 Unauthorized`: Client is not authenticated to access the resource.
        *   `403 Forbidden`: Client is authenticated but not allowed to access.
        *   `404 Not Found`: The resource doesn't exist.
        *   `500 Internal Server Error`: Something went wrong on the server.

**2.2 JAX-RS Fundamentals (2 - 3 hours)**

*   **What is JAX-RS?**
    *   Jakarta RESTful Web Services (formerly Java API for RESTful Web Services)
    *   A standard Java API for creating RESTful APIs.
*   **Key JAX-RS Annotations:**
    *   `@Path`: Defines the URI path for a resource (e.g., `@Path("/products")`).
    *   `@GET`, `@POST`, `@PUT`, `@DELETE`: Map HTTP methods to Java methods within a resource class.
    *   `@Produces`: Specifies the response content type (e.g., `@Produces(MediaType.APPLICATION_JSON)`).
    *   `@Consumes`: Specifies the request content type (e.g., `@Consumes(MediaType.APPLICATION_JSON)`).
    *   `@PathParam`: Extracts values from path parameters (e.g., `@Path("/{id}") @GET public Product getProduct(@PathParam("id") int id)`).
    *   `@QueryParam`: Extracts values from query parameters (e.g., `@QueryParam("category") String category`).
*   **Resource Classes:**
    *   Java classes that implement RESTful endpoints.
    *   These classes are where the logic for handling requests and responses goes.
*   **Returning Different Response Types:**
    *   Returning Java objects that will be automatically converted to JSON.
    *   Using `javax.ws.rs.core.Response` for fine-grained control over responses (e.g., setting headers, status codes).

**2.3 Implementing REST Endpoints with Quarkus (2 - 3 hours)**

*   **Creating Resource Classes:**
    *   Creating a new Java class and annotating it with `@Path`.
    *   Using `@Inject` to inject dependencies (covered in more detail in Module 3).
*   **Defining REST Endpoints:**
    *   Using `@GET`, `@POST`, `@PUT`, `@DELETE` annotations to map methods to HTTP operations.
    *   Creating simple endpoints for basic CRUD (Create, Read, Update, Delete) operations.
*   **Handling JSON Requests and Responses:**
    *   Quarkus uses Jackson (or JSON-B) for converting Java objects to JSON automatically.
    *   How to correctly read and parse JSON data from requests using classes.
*   **RESTEasy: Quarkus's JAX-RS Implementation:**
    *   RESTEasy is the implementation used by Quarkus, but developers don't need to interact directly with it often.
*   **Hands-on Lab:**
    *   Create a `Product` resource class with fields like `id`, `name`, and `price`.
    *   Implement a REST API for managing products, including:
        *   `GET /products`: Retrieve all products
        *   `GET /products/{id}`: Retrieve a single product by ID
        *   `POST /products`: Create a new product
        *   `PUT /products/{id}`: Update an existing product
        *   `DELETE /products/{id}`: Delete a product

**2.4 Advanced JAX-RS Features (2 - 3 hours)**

*   **Content Negotiation:**
    *   How to handle different content types requested by the client.
    *   Using `@Produces` and the `Accept` header.
*   **Exception Handling and Mapping:**
    *   Handling exceptions in a RESTful way.
    *   Creating exception mappers to convert Java exceptions to appropriate HTTP responses.
*   **Filters and Interceptors:**
    *   Using JAX-RS filters to intercept incoming requests or outgoing responses.
    *   Using interceptors to perform actions before or after method execution.
    *   Examples: logging, authentication, request transformation
*   **Asynchronous Processing with JAX-RS:**
    *   Using `@Suspended` annotation for non-blocking request handling (especially for long-running operations).
    *   Introducing the concept of async processing using threads.
*   **Hands-on Lab:**
    *   Add content negotiation to the product API to support both JSON and XML.
    *   Implement exception handling to return appropriate error responses for invalid requests.
    *   Add a filter that logs all requests to the `/products` endpoint.
    *   Modify one of the `PUT` or `POST` methods to be asynchronous using `@Suspended`.

**Key takeaways from Module 2:**

*   Understanding REST principles and applying them in building web services.
*   Using JAX-RS annotations to map HTTP operations to Java methods.
*   Creating resource classes to handle requests and return responses.
*   Handling JSON data in requests and responses.
*   Understanding how to use advanced features like content negotiation, exception handling, and asynchronous processing.

**Throughout this module, we should also emphasize:**

*   **Testing Your APIs:** We will use tools like `curl` or Postman to test the created API endpoints.
*   **Code Organization:** Best practices for organizing resource classes and methods.
*   **Practical Application:** Focus on solving real-world problems with RESTful services.

This detailed breakdown of Module 2 provides a thorough understanding of building RESTful APIs with JAX-RS and RESTEasy in Quarkus. It combines theoretical knowledge with hands-on experience, ensuring students can effectively implement and use REST APIs in their projects.
