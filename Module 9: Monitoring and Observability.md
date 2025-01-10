
**9.1 Logging (1.5 - 2 hours)**

*   **Why Logging is Important?**
    *   Troubleshooting and debugging issues.
    *   Monitoring application behavior.
    *   Auditing access and changes.
    *   Analyzing trends and patterns.
    *   Understanding user behavior.
    *   To have historical data about execution of the application.
*   **Quarkus Logging Configuration:**
    *   Using `application.properties` (or `application.yaml`) to configure logging levels, appenders, and formats.
    *   Setting log levels for different components or packages (`quarkus.log.level`, `quarkus.log.handler.json.level`).
    *   Configuring log format patterns using the `quarkus.log.console.format` property.
*   **Logging Frameworks:**
    *   Quarkus supports popular logging frameworks:
        *   **Logback (default):** A powerful and flexible logging framework.
        *   **Log4j2:** Another popular and high-performance logging framework.
        *   SLF4j is the abstraction layer that makes it possible to use both frameworks.
    *   Switching between frameworks is easy, you just need to add the corresponding extension and properties.
*   **Log Appenders:**
    *   Destinations where logs are sent (e.g., console, file, database).
    *   Using different appenders for different purposes.
        *   Console appender (for development and testing).
        *   File appender (for production).
        *   JSON appender (for structured logging).
*   **Log Levels:**
    *   Different levels of log messages (TRACE, DEBUG, INFO, WARN, ERROR, FATAL).
    *   Setting different levels for different environments.
    *   Using different log levels when writing log statements.
        *   `logger.info()`, `logger.debug()`, `logger.error()`.
*   **Structured Logging:**
    *   Using JSON formatted logs to make logs easier to parse and process (especially when using log aggregators).
    *   Adding additional context to log entries (e.g., request IDs, user IDs).
*   **Example:**

`application.properties`

```properties
quarkus.log.console.format=%d{yyyy-MM-dd HH:mm:ss} %-5p [%c{1.}] (%t) %s%e%n
quarkus.log.level=INFO
quarkus.log.handler.json.enable=true
quarkus.log.handler.json.level=INFO
quarkus.log.handler.json.format={ "timestamp": "%d{yyyy-MM-dd HH:mm:ss.SSS}", "level":"%p", "class":"%c{1.}", "message":"%s%e" }
```
`ProductService`:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;

@ApplicationScoped
public class ProductService {

    private static final Logger log = LoggerFactory.getLogger(ProductService.class);

    @Inject
    ProductRepository productRepository;

    public Product findById(Long id) {
      log.info("Fetching product with ID: {}", id);
      return productRepository.findById(id);
    }
}
```

*   **Hands-on Lab:**
    *   Add log statements to different parts of your application (e.g., REST endpoints, service classes).
    *   Configure different logging levels and observe the behavior.
    *   Configure a file appender to save logs to a file.
    *   Configure the JSON log appender to see how log entries are structured in JSON.
    *   Use different log levels (debug, info, error) when logging.
    *   Test that log messages are correctly formatted and written to the desired destinations.





**9.2 Metrics (2 - 2.5 hours)**

*   **What are Metrics?**
    *   Numerical measurements of application performance and behavior over time.
    *   Metrics provide valuable insights into:
        *   Response times.
        *   Error rates.
        *   Request counts.
        *   Resource usage.
    *   Helps to understand the health and performance of your application.
    *   Used for monitoring, alerting, and capacity planning.
*   **MicroProfile Metrics:**
    *   A standard API for defining and exposing metrics in Java applications.
    *   Quarkus uses MicroProfile Metrics to expose metrics.
        *  It is implemented by the smallrye metrics library.
    *   Defines the different types of metrics (counters, gauges, histograms, meters, timers).
    *   Provides annotations for easily exposing metrics from your application.
        * `@Counted`, `@Gauge`, `@Timed`, `@Metered`
*   **Types of Metrics:**
    *   **Counter:** A value that can only increase (e.g., number of requests).
    *   **Gauge:** A value that can increase or decrease (e.g., current memory usage).
    *   **Histogram:** A distribution of values over time (e.g., response time distribution).
    *   **Meter:** Measures the rate of events over time (e.g., requests per second).
    *  **Timer:** measures the duration of operations.
*   **Exposing Application Metrics:**
    *   Using annotations from MicroProfile Metrics:
        *   `@Counted`: Count the number of method invocations.
        *   `@Gauge`: Expose a value retrieved from a method.
        *   `@Timed`: Measure the duration of method execution.
        *   `@Metered`: Measure the rate at which a method is invoked.
    *   Adding `@ApplicationScoped` or other CDI annotations to classes that publish metrics.
    *   Quarkus automatically exposes metrics via HTTP at the `/metrics` endpoint.
        *   Using `application.properties` to configure the metrics endpoint.
        *   Default endpoint: `/metrics`
    *   Metrics are exposed in Prometheus format (plain text or JSON).
*   **Integration with Monitoring Systems:**
    *   **Prometheus:** A popular open-source monitoring system.
        *   Prometheus can be configured to scrape metrics from your Quarkus application.
        *  Usually prometheus is configured using a configuration file where it defines the targets to be scraped.
    *   **Grafana:** A popular open-source data visualization tool.
        *   Grafana can connect to Prometheus and visualize metrics in dashboards.
*   **Examples:**

`ProductService`:

```java
import org.eclipse.microprofile.metrics.annotation.Counted;
import org.eclipse.microprofile.metrics.annotation.Gauge;
import org.eclipse.microprofile.metrics.annotation.Timed;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;

@ApplicationScoped
public class ProductService {

    @Inject
    ProductRepository productRepository;

    @Counted(name="findProductByIdCount", description = "Count of method invocation for findProductById")
    @Timed(name="findProductByIdTimer", description = "Timer of method execution for findProductById")
    public Product findById(Long id) {
      return productRepository.findById(id);
    }

     @Gauge(name="productsCount", description = "Number of products in the database")
    public int getProductsCount(){
        return productRepository.listAll().size();
    }

}
```

*   **Hands-on Lab:**
    *   Add metrics to your Quarkus application using annotations from MicroProfile Metrics.
    *   Expose metrics for your application's service methods.
    *   Access the `/metrics` endpoint to view exposed metrics.
    *   Set up Prometheus and Grafana and integrate them with your application (optional).
    *   Create a simple Grafana dashboard to visualize your application's metrics.

**9.3 Tracing (2 - 2.5 hours)**

*   **What is Tracing?**
    *   A technique for tracking requests as they flow through different components of a distributed system.
    *   Helps to understand the call path of a request, identify bottlenecks, and debug issues.
    *   In a microservices architecture, a single user request can invoke multiple services.
        *   Tracing shows how the request is routed between microservices and its execution time in each service.
    *   Especially important in microservices environments.
*   **MicroProfile OpenTracing:**
    *   A standard API for distributed tracing in Java applications.
    *   Quarkus uses MicroProfile OpenTracing to trace requests.
    *   Requires an implementation (e.g., Jaeger or Zipkin).
*   **Key Concepts of Tracing:**
    *   **Trace:** Represents a single end-to-end request, it is a set of spans.
    *   **Span:** Represents a single unit of work within a trace (e.g., a method call, database query).
    *   **Span Context:** Includes information about the trace and current span.
        *  The context propagates between services, so the trace can continue in a different service.
    *   **Trace ID:** A unique identifier for a trace.
    *   **Span ID:** A unique identifier for a span.
    *   **Parent Span ID:** A pointer to the span that created the current span.
*   **Tracing Implementations:**
    *   **Jaeger:** An open-source distributed tracing system developed by Uber.
    *   **Zipkin:** Another popular open-source distributed tracing system developed by Twitter.
    *  Both Jaeger and Zipkin can be used with MicroProfile OpenTracing,
*   **Integrating Tracing with Quarkus:**
    *   Adding the appropriate tracing extension (e.g., `quarkus-jaeger`, `quarkus-opentracing-zipkin`).
    *   Configuring the tracing provider's address (e.g., Jaeger or Zipkin server URL) on your application.properties.
    *   Quarkus automatically traces requests that enter the application by default, but it can be configured.
    *   Using the `@Traced` annotation to trace specific methods or classes.
         *  `org.eclipse.microprofile.opentracing.Traced`
    *  Using `Tracer` bean to create custom spans programmatically.
*   **Visualizing Traces:**
    *   Using the Jaeger or Zipkin web UI to visualize traces.
    *   Understanding the relationships between spans.
    *  Identifying bottlenecks or performance issues.

*   **Examples:**

`application.properties`:
```properties
quarkus.jaeger.enabled=true
quarkus.jaeger.service-name=my-quarkus-app
quarkus.jaeger.sampler-type=const
quarkus.jaeger.sampler-param=1
quarkus.jaeger.reporter-host=localhost
quarkus.jaeger.reporter-port=14250
```

`ProductService`
```java
import org.eclipse.microprofile.opentracing.Traced;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;

@ApplicationScoped
@Traced
public class ProductService {

    @Inject
    ProductRepository productRepository;

   // Tracing all the methods of the service class because of class level annotation
    public Product findById(Long id) {
      return productRepository.findById(id);
    }
}
```

*   **Hands-on Lab:**
    *   Set up a local Jaeger or Zipkin server.
    *   Add the corresponding tracing extension to your project.
    *   Configure your Quarkus application to connect to the tracing provider.
    *   Trace all methods from the service layer class using the `@Traced` annotation.
    *  Test your application and then access Jaeger UI or Zipkin to see the trace.
    *   Add some manual instrumentation using the `Tracer` bean, to create a custom span.
    *  Observe how a request flow between services when using the tracing feature, if you have multiple services.




**9.4 Health Checks (1.5 - 2 hours)**

*   **What are Health Checks?**
    *   Endpoints that report the health status of an application.
    *   Used by orchestration platforms (e.g., Kubernetes) to determine if an application is running properly.
    *   Used by load balancers to verify if a server is ready to receive new requests.
    *   Can be used by other systems to monitor the health of an application.
*   **Types of Health Checks:**
    *   **Liveness Probe:** Checks if the application is running and responding to requests.
        *   Used to determine if a pod or container should be restarted (when it fails).
        *  It answers the question: "Is the application alive?".
    *   **Readiness Probe:** Checks if the application is ready to receive traffic.
        *   Used to determine if the application is ready to accept incoming requests and if the service is ready to route traffic to it.
        *  It answers the question "Is the application ready to receive traffic?".
    *   Both liveness and readiness probes can verify dependencies and resources.
        * For example, that database connections are ok and that other services are available.
*   **MicroProfile Health:**
    *   A standard API for defining and exposing health checks in Java applications.
    *   Quarkus uses MicroProfile Health to implement health checks.
    *   Provides annotations for creating health check procedures.
        *  `@Liveness`, `@Readiness`, `@Health`.
    *   Health checks are exposed via HTTP endpoints.
        *   `/health/live` for liveness checks.
        *   `/health/ready` for readiness checks.
        *   `/health` for general health checks.
*   **Implementing Health Checks with Quarkus:**
    *   Using annotations to create custom health checks.
        * `@Liveness`: To create a liveness probe.
        * `@Readiness`: To create a readiness probe.
        * `@Health` : To create a general health check.
        *  `org.eclipse.microprofile.health.*`
    *   Implementing `HealthCheck` interfaces in a class.
        *  Used if you need more control over the health status.
    *  Returning a `HealthCheckResponse` from the methods to specify the result of the healthcheck.
    *   Checking dependencies (e.g., database connection, other services).
    *  Quarkus exposes default health checks (e.g., disk space, memory).
*   **Kubernetes Integration with Health Checks:**
    *   Kubernetes uses liveness and readiness probes to manage pods.
    *   Configuring health check probes in your Kubernetes manifest files.
    *   Kubernetes probes are configured using a path to access the healthcheck endpoints, e.g. `health/ready`.
*   **Examples:**

`ProductHealthCheck`

```java
import org.eclipse.microprofile.health.HealthCheck;
import org.eclipse.microprofile.health.HealthCheckResponse;
import org.eclipse.microprofile.health.Liveness;

import javax.enterprise.context.ApplicationScoped;

@Liveness
@ApplicationScoped
public class ProductHealthCheck implements HealthCheck {

  @Override
    public HealthCheckResponse call() {
      boolean isHealthy = true; // your logic to verify health
        if(isHealthy)
            return HealthCheckResponse.up("Product is Ok");
        else
             return HealthCheckResponse.down("Product is Down");
    }
}
```
`DatabaseHealthCheck`:

```java
import org.eclipse.microprofile.health.HealthCheck;
import org.eclipse.microprofile.health.HealthCheckResponse;
import org.eclipse.microprofile.health.Readiness;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;
import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@Readiness
@ApplicationScoped
public class DatabaseHealthCheck  implements HealthCheck {
   @Inject
    DataSource dataSource;

    @Override
    public HealthCheckResponse call() {
        try(Connection connection = dataSource.getConnection()){
           if(connection!=null)
                return HealthCheckResponse.up("Database Connection ok");
          else
                return HealthCheckResponse.down("Database not connected");

        } catch (SQLException e) {
             return HealthCheckResponse.down("Database connection error");
        }
    }
}
```

*   **Hands-on Lab:**
    *   Create custom liveness and readiness probes for your application (e.g., verify database connection, check the status of a dependency).
    *   Expose these probes using MicroProfile Health annotations.
    *   Access the `/health/live` and `/health/ready` endpoints.
    *   Simulate a failure and see how probes respond to it.
    *   Configure your Kubernetes deployment to use liveness and readiness probes.

**9.5 Debugging (1.5 - 2 hours)**

*   **Debugging in Development Mode:**
    *   Quarkus provides features for live coding, which speeds up development cycles (every change is recompiled and the application is restarted).
    *   Debugging Java code with breakpoints using your IDE.
    *   Using the remote debug feature of Java.
         *  You can configure the port where the application is running and attach it to your IDE.
    *   Observing logs to identify issues.
*   **Debugging in Production:**
    *   Debugging native images (more complex compared to JVM debugging).
        * You need to add special parameters to enable debug during build process.
         * e.g., `-Dquarkus.native.debug-build=true`.
    *   Attaching a debugger using the remote debug feature for applications deployed in containers.
         * You need to expose the port using the docker `-p` argument.
        *  You can also configure remote debugging in Kubernetes.
    *   Using logging extensively for debugging (and understanding the behavior of the application).
    *   Using metrics and tracing for performance monitoring and troubleshooting.
    *  Using tools for monitoring the resource consumption of the application.
*   **Common Debugging Techniques:**
    *   Adding breakpoints to your code.
    *   Stepping through the execution.
    *   Inspecting variables.
    *   Viewing the call stack.
    *  Using logs to understand the flow of the application and any error messages.
    *   Using tracing to understand how a request flows between services.
*   **Debugging Tools:**
    *   IDE debuggers (e.g., IntelliJ IDEA, VS Code, Eclipse).
    *   Command-line tools (`jps`, `jstack`, `jmap`).
    *  Monitoring Tools (e.g. JConsole or VisualVM)

*   **Examples:**
    *   Configuring your IDE for debugging Quarkus applications.
    *   Attaching a debugger remotely to a Quarkus application in a container.
    *   Using jstack to print the stack of the running threads.

*   **Hands-on Lab:**
    *   Start your Quarkus application in dev mode and use the debugger in your IDE to step through the code.
    *   Configure remote debugging for your containerized application.
    *   Simulate an error scenario in your application and debug it using a combination of logging, debugging and metrics (if available).
    *  Use JConsole or VisualVM to see memory usage and garbage collection.


Okay, let's dive into **Module 10: Advanced Topics**, focusing on **10.1 Quarkus Extensions**. This section will explore the vast ecosystem of Quarkus extensions and demonstrate how to use them to add powerful capabilities to your applications.

**Module 10: Advanced Topics (Focus on 10.1)**

**Goal (for 10.1):** To provide an understanding of the Quarkus extension ecosystem and how to use various extensions to integrate with different technologies, from messaging and data streaming to communication protocols and other utilities. By the end of this section, students will be able to leverage the power of Quarkus extensions to enhance their applications.

**10.1 Quarkus Extensions (3 - 4 hours)**

*   **What are Quarkus Extensions?**
    *   Reusable modules that provide functionality and integrations with other technologies.
    *   Extensions are the core building blocks of Quarkus applications.
    *   They are packaged as JAR files and added to a Quarkus project as Maven or Gradle dependencies.
    *   They provide a convenient and consistent way to extend Quarkus' functionality.
*   **The Quarkus Ecosystem:**
    *   A wide variety of extensions are available for different technologies:
        *   Databases (e.g., PostgreSQL, MySQL, MongoDB, Neo4j).
        *   Message brokers (e.g., Kafka, RabbitMQ, AMQP).
        *   API technologies (e.g., gRPC, GraphQL, OpenAPI).
        *   Cloud integrations (e.g., AWS, Azure, Google Cloud).
        *   Utilities (e.g., Security, Configuration, Testing).
    *   A large community of contributors developing and maintaining extensions.
*   **Exploring the Quarkus Extension Catalog:**
    *   The Quarkus website has a comprehensive catalog of available extensions.
        *   [https://quarkus.io/extensions/](https://quarkus.io/extensions/)
    *   Browsing and searching for extensions based on their names, descriptions, technologies.
    *   The Quarkus website provides guides with detailed information on how to use the extensions.
*   **Dependency Management:**
    *   Adding extensions as dependencies in your `pom.xml` (Maven) or `build.gradle` (Gradle) file.
    *   Using the Quarkus CLI to automatically add extensions to your project.
        *   `quarkus extension add <extension-name>`.
        *   You can also add extensions using the code.quarkus.io website.
*   **Configuration of Extensions:**
    *   Configuration of extensions using the `application.properties` file.
    *   Using specific properties prefixed by the extension name.
        *   e.g., `quarkus.kafka.bootstrap.servers=localhost:9092`.
    *   Configuration is contextual based on the extensions added.
    *  Most extensions will follow the conventions used in Quarkus to configure properties.
*   **Using Various Extensions:**
    *   **Kafka:** A distributed streaming platform.
        *   Using `quarkus-kafka-client` extension for producing and consuming messages.
        *   Configuring the Kafka brokers, topic names, serializers, and deserializers.
        *   Using reactive streams with Kafka for asynchronous messaging.
    *   **gRPC:** A high-performance RPC (Remote Procedure Call) framework.
        *   Using `quarkus-grpc` extension for creating and consuming gRPC services.
        *   Defining gRPC services using protocol buffer definitions.
        *   Using the gRPC client and server implementations.
    *   **Camel:** A powerful integration framework.
        *   Using `quarkus-camel` extension for integration with multiple systems.
        *   Defining Camel routes for data processing and transformation.
        *   Using Camel components for various protocols and formats.
    *   **AMQP:** Advanced Message Queuing Protocol.
        *  Using `quarkus-artemis` for message broker communications via AMQP protocol.
        *  Using the AMQP client library to publish and consume messages using queues and topics.
*   **Examples:**

**Kafka Example**

```java
import org.eclipse.microprofile.reactive.messaging.Channel;
import org.eclipse.microprofile.reactive.messaging.Emitter;
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/kafka")
public class KafkaResource {
   @Inject
    @Channel("products-out")
    Emitter<String> emitter;

    @GET
    @Path("/send")
    @Produces(MediaType.TEXT_PLAIN)
    public String sendMessage() {
        emitter.send("New product sent");
        return "Message Sent";
    }
}
```

`application.properties`

```properties
mp.messaging.outgoing.products-out.topic=products
mp.messaging.outgoing.products-out.connector=smallrye-kafka
quarkus.kafka.bootstrap.servers=localhost:9092
```

**gRPC Example**

*   Create a `.proto` file to define a gRPC service.

```protobuf
syntax = "proto3";

option java_multiple_files = true;
option java_package = "org.acme.grpc";
option java_outer_classname = "ProductProto";

package org.acme.grpc;
service ProductService {
  rpc getProduct (ProductRequest) returns (ProductReply) {}
}

message ProductRequest {
 string id = 1;
}
message ProductReply {
  string name = 1;
   double price = 2;
}
```
*  Create the gRPC server

```java
import io.quarkus.grpc.GrpcService;
import io.smallrye.mutiny.Uni;
import org.acme.grpc.ProductProto;
import org.acme.grpc.ProductReply;
import org.acme.grpc.ProductRequest;
import org.acme.grpc.ProductServiceGrpc;

import javax.inject.Inject;

@GrpcService
public class ProductGrpcService extends ProductServiceGrpc.ProductServiceImplBase {

    @Inject
    ProductRepository productRepository;


   @Override
   public Uni<ProductReply> getProduct(ProductRequest request){
    Product product = productRepository.findById(Long.parseLong(request.getId()));
        ProductReply productReply =  ProductReply.newBuilder().setName(product.name).setPrice(product.price).build();
       return Uni.createFrom().item(productReply);
   }
}
```
*   Add the gRPC client

```java
import io.quarkus.grpc.GrpcClient;
import org.acme.grpc.ProductReply;
import org.acme.grpc.ProductRequest;
import org.acme.grpc.ProductServiceGrpc;
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
@Path("/grpc-products")
public class ProductGrpcResource {

 @Inject
 @GrpcClient
 ProductServiceGrpc.ProductServiceBlockingStub productService;

  @GET
  @Path("/{id}")
  @Produces(MediaType.TEXT_PLAIN)
  public String getProduct(@PathParam("id") String id){
    ProductReply productReply = productService.getProduct(ProductRequest.newBuilder().setId(id).build());
      return String.format("Product: %s - price: %f",productReply.getName(), productReply.getPrice());
  }
}
```

*   **Hands-on Lab:**
    *   Choose one or two extensions from the examples.
    *   Add the selected extensions to your project as dependencies.
    *   Configure the extensions using the `application.properties` file.
    *   Develop a small application that utilizes the chosen extension (e.g., send messages with Kafka, create a gRPC service).
    *   Test your application using the new functionalities.
    *   Use Dev Services when applicable, for the selected extensions.

