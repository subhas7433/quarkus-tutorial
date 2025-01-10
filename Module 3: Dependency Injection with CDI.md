**Module 3: Dependency Injection with CDI (4-6 hours)**

**Goal:** To understand the concepts of Dependency Injection (DI) and how to use CDI within Quarkus applications to manage dependencies effectively. By the end of this module, students will be able to design loosely coupled applications and leverage CDI for various tasks.

**3.1 Introduction to Dependency Injection (1 - 1.5 hours)**

*   **The Problem: Tight Coupling**
    *   Explanation of what tight coupling means (when different parts of your code are too interconnected, making them hard to change).
    *   Example: A class directly creates its dependencies inside itself (using `new` keyword) creating dependencies directly.
    *   Why tight coupling is bad:
        *   Difficult to test (hard to isolate units).
        *   Hard to change (any modification can impact multiple components).
        *   Hard to reuse (components become too specific).
*   **What is Dependency Injection?**
    *   A design pattern where dependencies are provided to a class, instead of the class creating them itself.
    *   Think of it like a restaurant: instead of making all the ingredients themselves, they get them from suppliers.
    *   Core idea: decouple the creation of objects from their usage.
*   **Benefits of Dependency Injection:**
    *   **Loose Coupling:** Reduces dependencies between classes, making them independent and reusable.
    *   **Improved Testability:** Makes it easy to provide mock objects for unit testing.
    *   **Increased Flexibility and Maintainability:** Changes in one part of the code have less impact on other parts.
    *   **Code Reusability:** Promotes the reuse of components in different parts of the application.
*   **Inversion of Control (IoC):**
    *   DI is a form of Inversion of Control.
    *   Instead of the class being in control of creating its dependencies, an external framework or mechanism is in control (e.g., CDI in our case).
    *   The framework “inverts” the traditional flow of control.
*   **Types of Dependency Injection:**
    *   **Constructor Injection:** Passing dependencies through a constructor (recommended for required dependencies).
    *   **Setter Injection:** Providing dependencies through setter methods (can be used for optional dependencies).
    *   **Field Injection:** Injecting dependencies directly into fields using annotations (convenient but less preferred for production).

**3.2 Contexts and Dependency Injection (CDI) in Quarkus (2 - 2.5 hours)**

*   **What is CDI?**
    *   Jakarta Contexts and Dependency Injection (formerly Java EE CDI).
    *   A standard for managing dependencies in Java applications.
    *   A core feature of Quarkus, providing a standardized and powerful way to manage beans.
*   **CDI Annotations in Quarkus:**
    *   `@Inject`: Marks a field, constructor, or method as a dependency injection point.
        *   Quarkus CDI will automatically inject an instance of a compatible bean.
    *   **Scopes:** Define the lifecycle of a bean (how long it lives).
        *   `@ApplicationScoped`: One instance per application.
        *   `@RequestScoped`: One instance per incoming request (useful for web requests).
        *   `@Dependent`: Instance is tied to the lifecycle of the object it's injected into (default scope).
    *   `@Produces`: Marks a method as a bean producer (can be used to create and return specific types of dependencies).
        *   Great for providing external dependencies such as database connections.
    *   `@Disposes`: Marks a method for releasing resources when a bean is destroyed.
    *   `@Named`: Give bean a specific name that can be used for injection using the `@Inject` with `@Named` annotation
    *   `@Qualifier`: Allow to disambiguate between multiple beans of the same type.
    *   `@Alternative`: Marks a bean that could replace the default bean when the application is deployed with specific configurations.
*   **Bean Discovery:**
    *   Quarkus CDI automatically scans for CDI-managed beans.
    *   Understanding implicit and explicit bean declarations.
*   **Dependency Injection in Action:**
    *   Examples: Injecting a service into a REST resource or injecting a data access object into a service.
*   **Hands-on Lab:**
    *   Create a `ProductService` class that handles business logic related to products.
        *   This service class will depend on a `ProductRepository` class.
    *   Create a `ProductRepository` that is responsible for accessing and storing product data (for this first lab, store data in memory).
    *   Use `@Inject` to inject `ProductService` into the product resource class (from Module 2).
    *   Use `@ApplicationScoped` for `ProductService` and `ProductRepository` to ensure they are created once.
    *   Make sure that the REST resource will use the product service to perform business logic on products instead of doing it directly.
    *   Refactor `ProductService` and `ProductRepository` to use Interfaces.
    *   Add `@Qualifier` to select specific implementations when there are multiple.

**3.3 Advanced CDI Concepts (1 - 1.5 hours)**

*   **Interceptors:**
    *   AOP-like mechanism for intercepting method calls (can add behaviors before and after a method is executed).
    *   Creating interceptor bindings and interceptor classes.
    *   Use cases: logging, timing, security, validation
    *   Implement `@Interceptor` and `@InterceptorBinding`
    *   `@AroundInvoke` - how to do the actual interception of the method
*   **Decorators:**
    *   Allows to add new behaviors to a method that has already an existing implementation.
    *   Similar to proxy pattern
    *   Implement `@Decorator`
    *   Use `@Delegate` to delegate the original implementation of the method to the decorated one.
*   **Events:**
    *   Asynchronous events to allow different parts of the application to communicate without direct coupling.
    *   Firing and observing events.
    *   `@Observes` for method that receives the events.
    *   `javax.enterprise.event.Event` to send events.
*   **Built-in Beans in CDI**
    *  `InjectionPoint`: provides information about the injection point at which is being used.
    * `Instance`: enables accessing all beans of a given type.
*   **Hands-on Lab:**
    *   Create an interceptor that logs the execution time of methods in the `ProductService`.
    *   Add a decorator that will implement caching on `ProductRepository` based on `getProductById` method.
    *    Use events to notify some other part of the application that a product is created/modified/deleted.

**Key Takeaways from Module 3:**

*   Understanding the purpose and benefits of Dependency Injection.
*   Using CDI to manage dependencies in a Quarkus application.
*   Knowing how to use different CDI scopes, `@Inject`, `@Produces`, `@Disposes` `@Qualifier` and `@Alternative`.
*   Understanding Interceptors, Decorators and Events.
*   Designing loosely coupled applications using CDI.

**Throughout this module:**

*   Emphasize clean code practices and the advantages of loosely coupled code.
*   Encourage students to experiment with different scope types and observe the effects on application behavior.
*   Show how CDI makes testing and refactoring easier.
*   Continue to apply concepts learned in Modules 1 and 2 and integrate them with DI.
