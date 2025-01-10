
**5.1 Introduction to Reactive Programming (2.5 - 3.5 hours)**

*   **The Traditional Imperative Approach and Its Limitations:**
    *   Explanation of how traditional Java code typically executes sequentially, step by step.
    *   Problems with traditional approach when dealing with I/O-bound operations, concurrency, and asynchronous processing.
        *   Blocking operations can tie up resources.
        *   Threads are expensive to manage and create.
        *   Can lead to performance and scalability issues.
    *   Example: A long running query to the database can make the application unresponsive (blocking request).
*   **What is Reactive Programming?**
    *   A programming paradigm focused on asynchronous, non-blocking data streams and change propagation.
    *   It's about building systems that are resilient, responsive, elastic, and message-driven.
    *   Reactive programming addresses issues in traditional approaches by using a non-blocking approach.
*   **Key Principles of Reactive Programming:**
    *   **Asynchronous:** Operations are executed without blocking the main thread (non-blocking IO).
        *   The application can continue with another process or request while waiting for an operation to complete (e.g., database request).
    *   **Non-Blocking:** Threads are not kept waiting for an operation to complete, which means the application can use those threads for other requests.
        *   Instead of waiting, the operation is done in other threads and when it finishes the current thread is notified.
    *   **Event-Driven:** Systems react to events (data being available, an error occurring, or a timeout).
    *   **Backpressure:** Ability for consumers to tell producers to slow down when they are overloaded with data.
        *   Helps prevent resource exhaustion when the producer is much faster than the consumer.
    *   **Composable:** Operations can be chained together and composed in a declarative way.
*   **Reactive Systems:**
    *   Systems designed with the principles of reactive programming in mind.
    *   Characteristics:
        *   **Responsive:** System respond in a timely manner.
        *   **Resilient:** System remain available in the presence of failures.
        *   **Elastic:** The system is able to scale up and down as needed.
        *   **Message-Driven:** Systems that rely on messages to communicate with each other.
*   **The Reactive Streams Specification:**
    *   A standard that defines how reactive streams should behave in Java.
    *   Provides a common set of interfaces and rules.
    *   Libraries like Mutiny and RxJava implement this specification.
    *   Important interfaces of the Reactive Streams:
        *   `Publisher`: Source of data, emits a stream of values.
        *   `Subscriber`: Consumes the stream of data emitted by the publisher.
        *   `Subscription`: Controls the flow of data, allows to cancel a stream or request values.
        *   `Processor`: Combines the functionality of a Publisher and Subscriber.

*   **Why Reactive Programming is Important:**
    *   Building high-performance, non-blocking applications.
    *   Managing concurrency and asynchronous tasks more efficiently.
    *   Handling large volumes of data.
    *   Building systems with better scalability and responsiveness.

**5.2 Mutiny: A Reactive Streams Library for Java (2.5 - 3.5 hours)**

*   **What is Mutiny?**
    *   A Reactive Streams library for Java specifically designed to work well in Quarkus.
    *   Provides a set of classes and operations for building reactive applications.
    *   It's a foundation for building reactive applications with Quarkus.
    *   Simple and intuitive API
*   **Key Mutiny Concepts:**
    *   **`Uni<T>`:** A reactive type that represents a single value or no value (completion/failure).
        *   Think of it as a future or promise that will eventually contain a single result, or fail.
        *   Good for asynchronous requests (e.g., database query for a single entity, HTTP request).
    *   **`Multi<T>`:** A reactive type that represents a stream of values (0 to infinite values).
        *   Think of it as a stream of data where values can arrive over time.
        *   Good for data that changes over time or for collections of items (e.g., database streaming data, server-sent events).
    *   **Creating Reactive Streams:**
        *   `Uni.createFrom().item()`: Create a `Uni` from a static value.
        *   `Uni.createFrom().completionStage()`: Create a `Uni` from a `CompletionStage`.
        *   `Multi.createFrom().items()`: Create a `Multi` from a collection of items.
        *   `Multi.createFrom().publisher()`: Create a `Multi` from any reactive streams `Publisher`.
        *   `Multi.createFrom().emitter()`: Create a `Multi` and emit values through an emitter.
    *   **Operators: Transforming, Filtering, Combining Streams:**
        *   `map()`: Transform each item in a stream.
        *   `flatMap()`: Transform and flatten streams.
        *   `filter()`: Filter items in a stream based on a condition.
        *   `concat()`: Combine multiple streams sequentially.
        *   `merge()`: Combine multiple streams concurrently.
        *   `zip()`: Combine multiple streams by merging items at same positions.
    *   **Subscribing and Consuming Streams:**
        *   `subscribe().with()`: Subscribe to a stream and provide callbacks for successful and error scenarios.
    *   **Error Handling and Recovery:**
        *   `onFailure()`: Handle errors within a stream.
        *   `onFailure().recoverWithItem()`: Recover from errors by providing a fallback value.
        *   `onFailure().retry()`: Retry an operation if an error occurs.
    *   **Asynchronous Programming with Mutiny**
        *  `runOn(executor)`: Specify the thread pool where the operation must execute.
    *   **Examples:**

```java
import io.smallrye.mutiny.Uni;
import io.smallrye.mutiny.Multi;
import io.smallrye.mutiny.subscription.Cancellable;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MutinyExample {

    public Uni<String> getGreetingAsync(String name){
     return Uni.createFrom().item(()->String.format("Hello %s",name));
    }

    public  Multi<Integer> generateNumbers(){
    return Multi.createFrom().items(1,2,3,4,5);
    }

    public Uni<Integer> getSumAsync(int a, int b){
        CompletableFuture<Integer> future =  CompletableFuture.supplyAsync(() -> a + b);
        return Uni.createFrom().completionStage(future);
    }

     public  Multi<String> transformStream(List<Integer> numbers){
       return   Multi.createFrom().items(numbers)
                  .map(number -> "The number is " + number)
                  .filter( str -> str.contains("2"))
        ;
     }

  public void consumeStream(Multi<String> stream){
         Cancellable cancellable = stream.subscribe().with(item -> System.out.println("Item: " + item),
                failure -> System.err.println("Failed: " + failure));
  }

  public Uni<String> transformValue(String greeting) {
      return Uni.createFrom().item(greeting)
                .map(String::toUpperCase);
  }

 public  void consumeValue(Uni<String> uni){
         uni.subscribe().with(item -> System.out.println("Item: " + item),
                 failure -> System.err.println("Failed: " + failure));
  }

  public Uni<String> asyncOperation(String name) {
          ExecutorService executor = Executors.newFixedThreadPool(10);
       return Uni.createFrom().item(() -> {
            try {
                Thread.sleep(2000); // Simulating an async operation
                return "Async operation was executed for " + name;
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                return "Exception in async operation";
            }
        }).runOn(executor); // Runs on a thread from the pool

    }
}
```

*   **Hands-on Lab:**
    *   Create a class and implement a method that returns a `Uni<String>` that sends a message based on some parameter.
    *   Implement another method that returns a `Multi<Integer>` that generates a range of numbers.
    *   Create another method that returns a `Uni<Integer>` that perform a long operation based on some parameters.
    *   Add a method that transform a list of numbers into a stream of Strings using `map` operator, filters using the `filter` operator and print it to the console.
    *   Add a method that transforms a `Uni<String>`, transforms it to uppercase and print the result.
    *    Create a service that performs an async operation by returning a `Uni` and executing the long operation on a specific thread pool.





**5.3 Integrating Mutiny with JAX-RS (2.5 - 3.5 hours)**

*   **Why Integrate Mutiny with JAX-RS?**
    *   To build non-blocking REST APIs that can handle multiple concurrent requests without tying up threads.
    *   To handle asynchronous operations within REST endpoints efficiently.
    *   To respond with data streams (using Server-Sent Events, for example).
    *   To offer a fully reactive experience for both clients and server.
*   **Returning `Uni` from REST Endpoints:**
    *   A `Uni` is suitable for returning a single asynchronous result from an endpoint (like fetching a product by id).
    *   JAX-RS can automatically convert a `Uni` to the appropriate HTTP response (with proper content type handling).
    *   The processing of the `Uni` is non-blocking; the request thread can be used for other requests while the `Uni` is being resolved.
*   **Returning `Multi` from REST Endpoints:**
    *   A `Multi` is suitable for returning a stream of data (like a list of products, or a stream of events).
    *   JAX-RS can handle `Multi` responses in several ways:
        *   **Server-Sent Events (SSE):** A mechanism for pushing data from the server to the client in real time (using `MediaType.SERVER_SENT_EVENTS`).
        *   **JSON Array (Default):** `Multi` is serialized as a JSON array when using `MediaType.APPLICATION_JSON`.
    *   The processing of the `Multi` is non-blocking, so the server can stream out data as it becomes available without blocking a thread.
*   **Reactive REST Clients:**
    *   Use `RestEasy Reactive Client` to create non-blocking clients.
    *   `RestEasy Reactive Client` integrates with Mutiny and can consume `Uni` and `Multi` responses.
*   **Examples:**

```java
import io.smallrye.mutiny.Uni;
import io.smallrye.mutiny.Multi;

import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import java.util.List;

@Path("/reactive-products")
public class ReactiveProductResource {
    @Inject
    ProductService productService;

    @GET
    @Path("/{id}")
    @Produces(MediaType.APPLICATION_JSON)
    public Uni<Product> getProductById(@PathParam("id") Long id) {
        return productService.findProductById(id); // returns a Uni
    }


    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Multi<Product> getAllProducts() {
        return productService.getAllProducts(); // returns a Multi
    }

    @GET
    @Path("/stream")
    @Produces(MediaType.SERVER_SENT_EVENTS)
    public Multi<String> productUpdates(){
        return productService.getProductUpdates();
    }


}
```

```java
import io.smallrye.mutiny.Uni;
import io.smallrye.mutiny.Multi;
import java.time.Duration;
import java.util.List;
import java.util.concurrent.ThreadLocalRandom;
import javax.inject.Singleton;

@Singleton
public class ProductService {

  @Inject
    ProductRepository productRepository;


    public Uni<Product> findProductById(Long id) {
        return  Uni.createFrom().item(() -> productRepository.findById(id));
    }


    public Multi<Product> getAllProducts() {
        return Multi.createFrom().iterable(() -> productRepository.listAll());
    }
  public Multi<String> getProductUpdates() {
      return Multi.createFrom().ticks(Duration.ofSeconds(1))
          .map(tick -> String.format("Update event number: %s. Product id: %s", tick, ThreadLocalRandom.current().nextLong(10)));
    }
}
```
*   **Hands-on Lab:**
    *   Refactor the `/products/{id}` endpoint in the `ProductResource` to return a `Uni<Product>`.
        *   Use the `ProductService` to fetch the product with a reactive approach.
    *   Refactor the `/products` endpoint to return a `Multi<Product>` (returning all products) using a similar approach.
    *   Create a new endpoint `/products/stream` that returns `Multi<String>` with new product notifications (using server sent events, for example).
    *   Create a reactive rest client that consumes the `/products/{id}` and `/products` and log the results using callbacks.

**5.4 Reactive Data Access (2.5 - 3.5 hours)**

*   **Why Reactive Data Access?**
    *   To access data in a non-blocking manner, keeping the system fully reactive from the web layer to the data access layer.
    *   To handle data streams from the database efficiently.
    *   To improve performance and scalability.
*   **Reactive SQL Clients:**
    *   A client that can interact with a database using a non-blocking and asynchronous approach.
        *  Use `vertx-pg-client`, `vertx-mysql-client`, `vertx-mssql-client`, `vertx-oracle-client`, etc.
    *   Example: `vertx-pg-client` for PostgreSQL.
    *   Returns results in a reactive stream (using `Uni` or `Multi`).
    *   `io.vertx.sqlclient` is a good option for Reactive SQL Data Access.
*   **Hibernate Reactive:**
    *   A reactive version of Hibernate ORM.
        *   Use it in conjunction with Panache for simplified database access.
    *   Provides non-blocking access to the database.
    *   Can return data in a reactive stream using `Uni` or `Multi`.
    *   Better choice for applications that needs a fully reactive approach
        *    It is based on asynchronous I/O and a thread pool for database operations.
*   **Using Panache with Mutiny:**
    *   Panache provides reactive methods for accessing data (e.g., `Panache.find(...).stream()`, `Panache.listAll().stream()`).
    *   These methods return reactive streams (Mutiny `Multi` objects) that can be consumed by reactive code.
*   **Examples:**

```java
import io.smallrye.mutiny.Uni;
import io.smallrye.mutiny.Multi;
import io.quarkus.hibernate.reactive.panache.Panache;

import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;

import java.util.List;


@ApplicationScoped
public class ReactiveProductRepository {

    public Uni<Product> findById(Long id){
       return  Panache.withTransaction(() -> Product.findById(id));
    }


    public Multi<Product> listAll(){
        return  Panache.withTransaction(() -> Product.listAll().stream());
    }

  public  Multi<Product> findByPrice(double price){
      return Panache.withTransaction(() -> Product.find("price >= ?1", price).stream());
    }

}
```

```java
import io.smallrye.mutiny.Uni;
import io.smallrye.mutiny.Multi;
import javax.inject.Inject;

@ApplicationScoped
public class ProductService {

 @Inject
    ReactiveProductRepository productRepository;

    public Uni<Product> findProductById(Long id) {
        return  productRepository.findById(id);
    }

     public  Multi<Product> getAllProducts() {
    return productRepository.listAll();

    }

    public Multi<Product> getProductsByPrice(double price){
        return productRepository.findByPrice(price);
    }
}
```

*   **Hands-on Lab:**
    *   Create a new `ReactiveProductRepository` that will use Panache with `Hibernate Reactive`.
    *    Refactor the `ProductService` to use the `ReactiveProductRepository`, using `Uni` and `Multi` from Mutiny.
    *   Implement a new method in `ProductService` that will use the `ReactiveProductRepository` to find all products with price greater than a provided value.
    *    Create a new REST Endpoint in `ReactiveProductResource` that consumes the data from the `getProductsByPrice` method.
    *   Test using clients and see how the application works with non-blocking queries.



