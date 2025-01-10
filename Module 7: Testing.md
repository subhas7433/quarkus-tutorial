**7.1 Unit Testing with JUnit 5 (2 - 3 hours)**

*   **What is Unit Testing?**
    *   Testing individual units (e.g., methods, classes) of code in isolation.
    *   Focus on validating that each part of the application works as intended.
    *   The goal is to identify bugs and defects as early as possible.
*   **JUnit 5:**
    *   The most common framework for writing unit tests in Java.
    *   Features:
        *   Annotations for defining test methods (`@Test`).
        *   Annotations for setup and teardown methods (`@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`).
        *   Assertions for verifying expected outcomes.
        *   Parameterized tests for testing with different sets of inputs.
*   **Why Unit Tests are Important:**
    *   Find bugs early and reduce the cost of fixing them.
    *   Ensure code quality and maintainability.
    *   Enable faster development cycles (due to the possibility of refactoring with confidence).
    *   Increase confidence in code changes.
    *   Serve as documentation for how the code should behave.
*   **Testing with Quarkus:**
    *   Using `@QuarkusTest` annotation to bootstrap Quarkus for unit tests.
        *   `io.quarkus.test.junit.QuarkusTest`
    *   Injecting dependencies with `@Inject` in tests (similar to how it's done in regular application classes).
    *   Running tests using Maven or Gradle (`mvn test` or `gradle test`).
    *   Unit tests can interact with services and repositories in isolation.
*   **Mocking Dependencies with Mockito:**
    *   Why use mocks?
        *   Isolate the unit under test.
        *   Replace real dependencies with controlled behavior.
        *   Simulate specific scenarios (success, failure, etc.).
    *   Mockito: A mocking framework for Java.
    *   Key Mockito concepts:
        *   Creating mock objects using `Mockito.mock()`.
        *   Defining the behavior of mocks using `when().thenReturn()` and `when().thenThrow()`.
        *   Verifying that methods were called using `Mockito.verify()`.
    *   Using `@InjectMock` annotation from Mockito to inject a mocked dependency.
        *   `org.mockito.InjectMocks`
    *   Using `@Mock` annotation from Mockito to create a mocked dependency.
         *  `org.mockito.Mock`
*   **Examples:**

```java
import io.quarkus.test.junit.QuarkusTest;
import org.junit.jupiter.api.Test;
import javax.inject.Inject;
import static org.junit.jupiter.api.Assertions.assertEquals;

import static org.mockito.Mockito.*;
import io.quarkus.test.junit.mockito.InjectMock;
import org.mockito.Mock;


@QuarkusTest
public class ProductServiceTest {
    @Inject
    ProductService productService;

    @InjectMock
    ProductRepository productRepository;


    @Test
   public void testFindProductById() {

        Product product = new Product();
        product.setId(1L);
        product.setName("Test Product");
        product.setPrice(10.0);

        when(productRepository.findById(1L)).thenReturn(product);

        Product productReturned = productService.findById(1L);

        assertEquals(1L, productReturned.getId());
        assertEquals("Test Product", productReturned.getName());
        assertEquals(10.0, productReturned.getPrice());
        verify(productRepository, times(1)).findById(1L);
    }

}
```

*   **Hands-on Lab:**
    *   Create unit tests for the `ProductService` from previous modules.
    *   Mock the `ProductRepository` and verify that the methods are being called with the correct parameters.
    *   Use different assertions to test the expected behavior of the `ProductService` methods.
    *   Write unit tests for edge cases and error scenarios.

**7.2 Integration Testing with REST-Assured (2 - 3 hours)**

*   **What is Integration Testing?**
    *   Testing the interaction between different components of the application (e.g., REST endpoints, data access layer).
    *   Focus on validating that the components work together correctly and that the application functions as a whole.
    *   Ensuring that communication between layers is correct.
*   **REST-Assured:**
    *   A Java library for testing REST APIs.
    *   Features:
        *   Fluent API for creating and sending HTTP requests.
        *   Easy to verify responses (status codes, headers, JSON body).
        *   Supports various request methods (GET, POST, PUT, DELETE).
        *   Support for different data formats (JSON, XML).
*   **Why Integration Tests are Important:**
    *   Validate that components work together correctly.
    *   Verify end-to-end functionality.
    *   Identify integration issues that might not be found in unit tests.
    *   Help with confidence when testing complex workflows and interactions.
*   **Testing REST Endpoints with REST-Assured:**
    *   Sending HTTP requests to your application endpoints using REST-Assured.
    *   Verifying the HTTP response status code (200, 201, 400, 404, 500 etc).
    *   Asserting the content of the response body (JSON or XML content).
    *   Using `given()`, `when()`, and `then()` methods for request and response handling.
    *   Using BDD style syntax (e.g., `body()`, `statusCode()`, `contentType()`).
*   **Examples:**
    *   First, we need to configure the tests to call the application at a certain port. We do this on the `application.properties`
```properties
quarkus.http.test-port=8181
```

```java
import io.quarkus.test.junit.QuarkusTest;
import org.junit.jupiter.api.Test;

import static io.restassured.RestAssured.given;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.CoreMatchers.containsString;

@QuarkusTest
public class ProductResourceTest {

  @Test
    public void testGetAllProducts() {
      given()
        .when().get("/products")
        .then()
        .statusCode(200)
        .contentType("application/json")
        .body(containsString("name")); //Verify if the body contains a "name" attribute
  }
    @Test
    public void testGetProductById() {
        given()
          .pathParam("id", 1)
         .when().get("/products/{id}")
         .then()
         .statusCode(200)
         .contentType("application/json")
          .body("name", is("My New Product")); //Verify the value of the name property is "My New Product"

    }

    @Test
    public void testCreateProduct() {
        given()
          .contentType("application/json")
        .body("{\"name\":\"new Product\", \"price\":20.00}")
      .when()
          .post("/products")
       .then()
          .statusCode(201);

    }


      @Test
    public void testGetProductByIdNotFound() {
        given()
          .pathParam("id", 99)
         .when().get("/products/{id}")
         .then()
         .statusCode(404);

    }
}
```

*   **Hands-on Lab:**
    *   Write integration tests for the `/products` endpoints in the `ProductResource` from previous modules.
    *   Test different HTTP methods (GET, POST, PUT, DELETE) and verify that the responses are correct (status codes, response bodies).
    *   Test different scenarios (e.g., successful operations, invalid inputs, error cases).
    *   Test that the endpoints are secured correctly (if you have implemented security in previous modules).





**7.3 Testing Native Images (1 - 1.5 hours)**

*   **What are Native Images?**
    *   A way to compile Java bytecode into standalone executables using GraalVM.
    *   Native images start up much faster and use less memory than traditional Java applications.
    *   Designed for running on smaller hardware and environments, as well as on serverless platforms.
    *   Native images have some limitations:
        *  Reflection usage should be registered
        *  Dynamic proxies should be registered
        *  Resources needed by the application should be included.
*   **Why Test Native Images?**
    *   To verify that the application runs correctly after being compiled to a native executable.
    *   To ensure there are no compatibility issues after the native image build.
    *   To find any problems related to the native-image limitations.
    *   Native image limitations may cause specific errors that are not present when the application is running on the JVM.
*   **`@NativeImageTest` Annotation:**
    *   An annotation from Quarkus that can be used to run integration tests against a native image build.
         *   `io.quarkus.test.junit.NativeImageTest`
    *   When you use this annotation, Quarkus will build a native image of your application and run the tests on it.
        *  You need to have installed GraalVM and configured the environment variables properly to run native image tests.
        *   It is needed to have the `native` profile to use native image tests.
    *   The tests should be similar to the integration tests that you are already running (e.g., HTTP endpoint calls).
    *   The annotation is used along with `@QuarkusTest`
*   **Differences from `@QuarkusTest`:**
    *   `@QuarkusTest` runs the tests on a JVM environment
    *   `@NativeImageTest` runs tests on a native image executable.
    *   `@QuarkusTest` will not detect issues related to reflection and other limitations.
*   **Configuring Native Image Testing:**
    *   Ensure GraalVM is correctly installed and configured.
    *   Configuring the `application.properties` to define the necessary configurations.
    *   You should also configure the native profile configurations (native profile properties should be prefixed with `native-` ).
*   **Examples:**
    *  Using the `ProductResourceTest` example, we add the `@NativeImageTest` annotation to perform the same tests for the native image execution.

```java
import io.quarkus.test.junit.QuarkusTest;
import org.junit.jupiter.api.Test;

import static io.restassured.RestAssured.given;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.CoreMatchers.containsString;
import io.quarkus.test.junit.NativeImageTest;

@QuarkusTest
@NativeImageTest
public class ProductResourceTest {

  @Test
    public void testGetAllProducts() {
      given()
        .when().get("/products")
        .then()
        .statusCode(200)
        .contentType("application/json")
        .body(containsString("name")); //Verify if the body contains a "name" attribute
  }
    @Test
    public void testGetProductById() {
        given()
          .pathParam("id", 1)
         .when().get("/products/{id}")
         .then()
         .statusCode(200)
         .contentType("application/json")
          .body("name", is("My New Product")); //Verify the value of the name property is "My New Product"

    }

    @Test
    public void testCreateProduct() {
        given()
          .contentType("application/json")
        .body("{\"name\":\"new Product\", \"price\":20.00}")
      .when()
          .post("/products")
       .then()
          .statusCode(201);

    }


      @Test
    public void testGetProductByIdNotFound() {
        given()
          .pathParam("id", 99)
         .when().get("/products/{id}")
         .then()
         .statusCode(404);

    }
}
```
*   **Hands-on Lab:**
    *   Add the `@NativeImageTest` annotation to one of your integration test classes from section 7.2.
    *   Ensure that GraalVM is properly configured on your machine.
    *   Run the native image tests using Maven or Gradle (using the `-Dnative` profile and `-Dquarkus.native.debug-build=true` if you need to debug)
    *   Observe that the application runs correctly as a native image.
    *  Fix any native image specific errors that might appear (if any).

**7.4 Dev Services (1 - 1.5 hours)**

*   **What are Dev Services?**
    *   A feature in Quarkus that automatically provisions dependencies (e.g., databases, message brokers) for development and testing.
    *   Instead of manually configuring and running dependencies, Quarkus will start them in Docker containers on demand when you start the application in dev mode or during tests.
    *   It helps avoid the "it works on my machine" issue because the dependencies are guaranteed to be in the same version and configuration.
*   **Why Use Dev Services?**
    *   Simplified development setup.
    *   Avoid manual configuration of dependencies during development and testing.
    *   Consistent environment for all developers.
    *   Makes it easier to start working on a new application.
    *   Automatically provides the necessary configurations for connecting to the dependencies.
*   **How Dev Services Work:**
    *   Quarkus automatically detects the dependencies required by your application (based on extensions included).
    *   It downloads and starts corresponding Docker containers on demand.
    *   It configures the application with the necessary properties to connect to the dependencies.
    *   The containers are automatically removed after you stop your application (for example when you stop the dev mode).
*   **Supported Dev Services:**
    *   Databases (PostgreSQL, MySQL, MariaDB, Microsoft SQL Server, Oracle, DB2).
    *   Message brokers (Kafka, RabbitMQ, ActiveMQ).
    *   Key-value stores (Redis).
    *   Other services (Vault, Cassandra, etc.).
    *   Full list can be seen in Quarkus documentation
*   **Configuring Dev Services:**
    *   Enable or disable Dev Services for specific dependencies using the `application.properties` file.
    *   Set specific configuration for each Dev Service (e.g., version, port mappings, etc.).
    *   Using the `testcontainers` extension can add more specific configurations.
    *  The property to configure Dev Service has the form of `quarkus.<extension-name>`.devservice.<properties>.
*   **Example:**

```properties
# Enable Dev Services for postgresql
quarkus.datasource.devservices.enabled=true
quarkus.datasource.devservices.image-name=postgres:15
quarkus.datasource.devservices.port=5432
# Enable Dev Services for Kafka
quarkus.kafka.devservices.enabled=true
quarkus.kafka.devservices.port=9092
```
*   **Hands-on Lab:**
    *   Ensure that Docker is installed and running on your machine.
    *   Add the `quarkus-postgresql-client` extension to your project (or other database client of your choice).
    *   Enable Dev Services for PostgreSQL by configuring the `application.properties`.
    *   Run your application in dev mode.
    *   Observe that a PostgreSQL container is automatically started.
    *   Change the database credentials and port and see that the application is configured automatically.
    *   Add the Kafka extension and configure Dev Service for kafka, and see the broker running in docker.
    *   Verify that your application can now connect to the database automatically without needing to configure the connection.


