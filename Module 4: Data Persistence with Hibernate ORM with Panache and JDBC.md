**4.1 Introduction to Object-Relational Mapping (ORM) (1.5 - 2 hours)**

*   **The Impedance Mismatch Problem:**
    *   Explanation: The disconnect between the way data is structured in object-oriented programming (objects with properties) and the way data is structured in relational databases (tables with rows and columns).
    *   Simplified analogy: Trying to fit a round object (Java object) into a square hole (database table).
    *   Why this is a problem:
        *   Writing raw SQL code to interact with a database from Java can be cumbersome and error-prone.
        *   It leads to a lot of boilerplate code for transferring data between objects and database records.
        *   It can make applications less maintainable and more difficult to change.
*   **What is ORM?**
    *   ORM is a technique that provides a bridge between object-oriented code and relational databases.
    *   It allows you to interact with a database using objects and their properties, without having to write SQL directly.
    *   It handles the mapping process: converting objects to database records and vice versa.
*   **Key Concepts of ORM:**
    *   **Entity:** A Java object that represents a table in a relational database. Each instance of an entity corresponds to a row.
        *   Example: A `Product` entity representing a `products` table.
    *   **Mapping:** The process of defining how entity properties are linked to table columns (also known as field mapping).
        *   Example: The `id` property of the `Product` object is mapped to the `id` column in the `products` table.
    *   **Persistence:** The ability to save and retrieve objects from the database.
        *   Saving a `Product` object to the database and retrieving it later based on its `id`.
    *   **Session/EntityManager:** The core object used to perform database operations (e.g. persist, update, query).

*   **Benefits of using ORM:**
    *   **Reduced Boilerplate Code:** Less code needed to interact with the database.
    *   **Faster Development:** ORM abstracts away the low-level details, letting you focus on business logic.
    *   **Improved Code Maintainability:** Easier to change database schema or application logic when using an ORM.
    *   **Simplified Data Access:** Access database using object-oriented approach instead of directly with SQL.
    *   **Database Independence:** An ORM can allow you to switch databases without significant code changes (to a certain degree).
*   **The Downside of ORM:**
    *   **Performance Overhead:** ORM can introduce some overhead due to mapping processes and potentially more complex queries. (It can be optimized if needed)
    *   **Learning Curve:** Requires learning a new API/framework (e.g., Hibernate).
    *   **Abstraction can hide some database details:** You should understand when an ORM is doing something that could be optimized manually.

**Example (Conceptual):**

**Without ORM (Conceptual SQL Approach):**

```java
//Assume there is a database connection
// Getting data from database requires writing SQL, and mapping by hand
public class Product {
    private int id;
    private String name;
    private double price;

    // Constructor, getters, setters
}
// Raw SQL to fetch a product
Connection con = ...;
PreparedStatement statement = con.prepareStatement("SELECT id, name, price FROM products where id = ?");
statement.setInt(1, productId);
ResultSet rs = statement.executeQuery();

if(rs.next()) {
    Product p = new Product();
    p.setId(rs.getInt("id"));
    p.setName(rs.getString("name"));
    p.setPrice(rs.getDouble("price"));
    return p;
}

//Raw SQL to save a product:
PreparedStatement statement = con.prepareStatement("INSERT INTO products (name, price) VALUES (?,?)");
statement.setString(1, p.getName());
statement.setDouble(2, p.getPrice());
statement.executeUpdate();
//... and so on for other operations

```

**With ORM (Conceptual):**

```java
//With ORM, we map our Java object with database table:
@Entity
@Table(name="products")
public class Product {
    @Id
    private int id;
    private String name;
    private double price;
    //Constructor, getters and setters
}
// The ORM is in charge of database access

// Getting a product
Product product = entityManager.find(Product.class, productId);

// Saving a product
entityManager.persist(product);
```

**4.2 Introduction to Hibernate ORM (1.5 - 2 hours)**

*   **What is Hibernate ORM?**
    *   A widely used Java library that implements the JPA (Jakarta Persistence API) standard for ORM.
    *   Quarkus integrates with Hibernate ORM to provide database persistence.
    *   Hibernate translates object-oriented operations into database-specific SQL.
*   **JPA (Jakarta Persistence API)**
    *   A standard specification for ORM in Java.
    *   Provides a common API that can be implemented by different ORM libraries (like Hibernate).
*   **Key Hibernate ORM Concepts:**
    *   **Entities:** POJOs (Plain Old Java Objects) that are annotated to represent database tables.
        *   The `@Entity` annotation marks a class as an entity.
        *   `javax.persistence.Entity`
    *   **Mapping Annotations:**
        *   `@Table`: Specifies the database table name for the entity.
            *   `javax.persistence.Table`
        *   `@Id`: Marks the primary key of the table.
            *   `javax.persistence.Id`
        *   `@GeneratedValue`: Specifies how the primary key is generated (e.g., auto-increment).
            *   `javax.persistence.GeneratedValue`
            *   `strategy = GenerationType.IDENTITY` (for auto-incrementing)
        *   `@Column`: Customizes the column mapping (name, length, etc.).
            *   `javax.persistence.Column`
    *   **EntityManager:** An interface responsible for interacting with the database.
        *   `javax.persistence.EntityManager`
        *   Used for persisting objects, retrieving objects, updating objects.
    *   **Persistence Context:** A cache of managed entities, used to track changes and avoid unnecessary database interactions.
*   **Setting up a Simple Entity with Hibernate:**
    *   Creating a Java class and annotating it with `@Entity`, `@Table`, `@Id`, and `@Column`.
    *   Mapping basic properties (data types) to database table columns.
* **Example:**

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.persistence.Column;

@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name", nullable = false)
    private String name;

    @Column(name = "price")
    private double price;

    // Constructors, getters, setters, (optionally: equals and hashcode)

    public Product() {
    }

    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }


    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}
```

*   **Explanation of Annotations:**
    *   `@Entity`: Marks the class as an entity that maps to a table.
    *   `@Table(name = "products")`: Specifies that the table name is "products".
    *   `@Id`: Marks the field 'id' as the primary key for the entity.
    *   `@GeneratedValue(strategy = GenerationType.IDENTITY)`: Specifies that the `id` value is auto-generated by the database.
    *   `@Column(name = "name", nullable = false)`: Defines column for `name` and sets it as non-nullable.
    *   `@Column(name = "price")`: Defines column for `price` and sets as nullable.






**4.3 Simplified Data Access with Panache (2.5 - 3.5 hours)**

*   **The Challenge of Traditional Data Access:**
    *   Review: Using the `EntityManager` can be effective, but often requires writing a lot of similar code for basic CRUD operations.
    *   Example: Finding an entity by ID, saving an entity, deleting an entity, updating and querying using the `EntityManager`.
    *   The problem: It's a repetitive process with significant overhead.
*   **What is Panache?**
    *   A Quarkus extension that provides an active record pattern for simplified database access.
    *   It reduces the boilerplate code necessary for database interaction and makes working with data easier.
    *   It is built on top of Hibernate ORM but adds a layer of abstraction.
*   **Active Record Pattern vs. Repository Pattern:**
    *   **Active Record Pattern:** Entities themselves have methods to perform persistence operations (e.g., `product.persist()`, `product.delete()`).
        *   Panache favors this approach.
        *   Makes it very easy to perform CRUD operations on entities directly.
    *   **Repository Pattern:** A separate "repository" class is created for data access logic, decoupling data access from entities.
        *   More flexible, but requires more code.
        *   Can be used alongside Panache when you need more complex logic.
*   **Creating Entities with Panache:**
    *   Extending `PanacheEntity` or `PanacheEntityBase` or implementing `PanacheEntity` interface or `PanacheRepository` interface.
        *   `PanacheEntity` for entities with auto-generated IDs.
        *   `PanacheEntityBase` for entities with custom IDs or that require specific behavior.
        *   `PanacheRepository` for repositories.
    *   Now the entity has static methods to directly perform database operations.
*   **Performing CRUD Operations with Panache:**
    *   `persist()`: Saves an entity.
    *   `delete()`: Deletes an entity.
    *   `findById()`: Find an entity by its ID.
    *   `listAll()`: List all entities of a certain type.
    *   `update()`: Updates an entity.
    *   `isPersistent()`, `isDeleted()`, `count()`.
*   **Simplifying Queries with Panache:**
    *   `find()`: Find entities based on a query string or Map.
    *   `find(...)` and other find related methods to create queries using an object-oriented approach without needing JPQL.
        *   Simplified criteria queries and pagination.
    *   `list(...)` and other similar methods to find and list entities based on a query.
    *   Using JPQL (Java Persistence Query Language) when you need to make more complex custom queries.
*   **Examples**

```java
import io.quarkus.hibernate.orm.panache.PanacheEntity;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "products")
public class Product extends PanacheEntity{ // Extends PanacheEntity instead of plain Entity
   @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    public Long id;
    public String name;
    public double price;
}
```

```java
//CRUD operations using Panache
// Save a new product to the database
Product product = new Product();
product.name = "My New Product";
product.price = 20.00;
product.persist(); // Static method from PanacheEntity

// Find a product by Id
Product foundProduct = Product.findById(10L); // Static method from PanacheEntity

// Find all products with price above 10
List<Product> products = Product.list("price > 10");

// Delete the product
foundProduct.delete(); //Method from PanacheEntity

//List all products from database
List<Product> allProducts = Product.listAll();
```

*   **Hands-on Lab:**
    *   Modify the `Product` entity from the previous section to extend `PanacheEntity`.
    *   Refactor the `ProductRepository` (or create a new one) to use Panache's methods instead of using the `EntityManager` directly.
        *   Now you can access directly the methods on the Entity to perform database operations.
    *   Update the `ProductService` to utilize the refactored `ProductRepository`.
    *   Ensure all CRUD operations in the REST API now use Panache.

**4.4 Using JDBC Directly (1.5 - 2.5 hours)**

*   **When to use JDBC Directly?**
    *   When performance is critical and you need very fine-grained control over SQL queries.
    *   For specialized queries that cannot be easily represented by ORM (complex joins, stored procedures, native SQL functions).
    *   For batch operations or direct access to database features not exposed by Hibernate.
    *   When you already have some code using JDBC.
    *   When you don't want to map classes to tables.
*   **The Agroal Connection Pool:**
    *   Quarkus uses the Agroal connection pool for managing JDBC database connections.
    *   Agroal is highly performant, production-ready connection pool.
*   **Injecting a `DataSource`:**
    *   A `DataSource` provides access to a database connection.
    *   You can inject the `DataSource` using `@Inject`.
        *   `javax.sql.DataSource`
*   **Executing SQL Queries:**
    *   Using the `DataSource` to obtain connections and create `Statement` or `PreparedStatement` objects.
    *   Executing `SELECT`, `INSERT`, `UPDATE`, `DELETE` queries.
    *   Handling `ResultSet` objects and transforming them into Java objects.
    *   Closing the database resources properly using try-with-resources.
*   **Example**

```java
import javax.sql.DataSource;
import javax.inject.Inject;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class ProductDao {
    @Inject
    DataSource dataSource;

    public Product findProductById(Long id) {
        String sql = "SELECT id, name, price FROM products WHERE id = ?";
        try (Connection connection = dataSource.getConnection();
             PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setLong(1, id);
            try (ResultSet rs = statement.executeQuery()) {
                if(rs.next()){
                    Product product = new Product();
                    product.id = rs.getLong("id");
                    product.name = rs.getString("name");
                    product.price = rs.getDouble("price");
                    return product;
                }
                return null;

            }
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

*   **Hands-on Lab:**
    *   Create a new `ProductDao` class using JDBC.
    *   Implement a method in `ProductDao` to retrieve products by category using a custom query with a `PreparedStatement`.
    *   Expose a new REST endpoint (in `ProductResource`) that will use the `ProductDao` to access data with JDBC.
    *   Compare the performance with the implementation using Panache and Hibernate.




**4.5 Transactions (2.5 - 3.5 hours)**

*   **What is a Transaction?**
    *   A transaction is a sequence of operations performed as a single logical unit of work.
    *   All operations within a transaction must either complete successfully (commit) or all must fail (rollback).
    *   Transactions ensure data integrity and consistency.
*   **ACID Properties of Transactions:**
    *   **Atomicity:** All operations in a transaction are treated as one unit – all succeed or all fail.
        *   Think of an "all or nothing" operation.
    *   **Consistency:** A transaction must bring the database from one valid state to another valid state.
    *   **Isolation:** Transactions should execute independently of each other. One transaction shouldn't interfere with another one.
    *   **Durability:** Once a transaction is committed, the changes are permanent and will survive even a system failure.
*   **Transaction Management:**
    *   Managing the start and end of a transaction (commit or rollback).
    *   Two main types of transaction management:
        *   **Programmatic Transaction Management:** Explicitly controlling transaction boundaries in the code.
            *   Using `EntityManager.getTransaction()` methods like `begin()`, `commit()` and `rollback()`.
        *   **Declarative Transaction Management:** Using annotations to define transaction boundaries, letting the framework handle the details.
            *   Quarkus favors this approach.
*   **Declarative Transaction Management with `@Transactional`:**
    *   `@Transactional`: Marks a method as transactional.
    *   Quarkus will automatically start a transaction before the method executes and commit or rollback after the execution (based on exceptions or method return).
        *   `javax.transaction.Transactional`
    *   If an unchecked exception is thrown, the transaction will be rolled back.
    *   If the method completes successfully the transaction is committed.
*   **Transaction Propagation:**
    *   How transactions behave when a method calls another transactional method.
    *   Key Propagation Types (using `@Transactional(propagation = ...)`):
        *   `REQUIRED` (Default): If a transaction exists, join it; otherwise, start a new one.
        *   `REQUIRES_NEW`: Always start a new transaction, suspending the existing one if any.
        *   `MANDATORY`:  A transaction must already exist; otherwise, an exception is thrown.
        *   `NOT_SUPPORTED`: Execute without a transaction; if a transaction exists, suspend it.
        *   `NEVER`: A transaction must not exist; otherwise, an exception is thrown.
        *   `SUPPORTS`: If a transaction exists, use it; otherwise, execute without a transaction.
    *   Understanding which strategy to choose based on application requirements.
*   **Transaction Timeout:**
    *   Setting a timeout for transactions to prevent long-running transactions that may be stuck and taking resources without success.
        *   `@Transactional(timeout = 10)` (in seconds)
*   **Examples:**

```java
import javax.transaction.Transactional;

@ApplicationScoped
public class ProductService {

    @Inject
    ProductRepository productRepository;

    // Simple transactional operation (default REQUIRED propagation)
    @Transactional
    public void createProduct(Product product) {
       productRepository.persist(product);
    }


    // Transactional operation using REQUIRES_NEW propagation: create a new transaction
    @Transactional(Transactional.TxType.REQUIRES_NEW)
    public void createProductWithNewTransaction(Product product){
        productRepository.persist(product);
    }

   // transactional operation that will not commit or rollback (if already exists), because of NOT_SUPPORTED propagation
    @Transactional(Transactional.TxType.NOT_SUPPORTED)
    public Product findProduct(Long id) {
      return  productRepository.findById(id);
    }

}
```

*   **Hands-on Lab:**
    *   Create a `OrderService` that handles orders.
    *   Implement a method that creates both an order and updates products available stock.
        *   Implement using `@Transactional` to keep order creation and product update together in a transaction.
        *   Add business logic to check stock availability.
        *   Use `REQUIRES_NEW` for a nested transactional method.
    *   Experiment with different propagation types (REQUIRED, REQUIRES_NEW) and observe the behavior with respect to exception handling.
    *   Add a timeout to the transaction, in case something goes wrong.
    *   Test the method to simulate failure in stock availability.

**4.6 Caching (2.5 - 3.5 hours)**

*   **What is Caching?**
    *   Storing frequently accessed data in a temporary storage location (the cache).
    *   Allows for faster retrieval of data compared to fetching it from the database every time.
    *   Improves application performance and reduces the load on the database.
*   **Types of Caches:**
    *   **First-Level Cache (Persistence Context):** A cache that exists within the scope of a transaction or `EntityManager`.
        *   Managed automatically by Hibernate.
        *   Entities that are loaded or saved in the current transaction are held on the persistence context.
        *   If you request the same entity again within the same transaction, Hibernate will provide it from the first level cache.
    *   **Second-Level Cache:** A cache shared across multiple transactions and `EntityManager`s.
        *   Requires explicit configuration and an implementation (like Infinispan, Ehcache, Caffeine).
        *   Used to avoid database reads for the same entities across different requests.
*   **Hibernate's First-Level Cache:**
    *   How the Persistence Context works.
    *   Automatically caches loaded and modified entities within the transaction.
    *   Only visible in the current transaction scope.
    *   The data on the first level cache is discarded when the transaction ends.
*   **Second-Level Cache Configuration:**
    *   Enabling the second-level cache (by adding the dependency to your project, usually Infinispan, and enabling on the configurations).
    *   Choosing a caching implementation:
        *   Infinispan (Quarkus default): Fast, scalable, and distributed cache.
        *   Ehcache: Another popular caching library.
        *   Caffeine: High performance in-memory cache library.
    *   Configuring the cache region (cache names).
    *   Configuring eviction policies and cache size limits.
*   **Caching Annotations:**
    *   `@Cacheable`: Marks an entity or a method as cacheable using the second-level cache (will store data on the cache).
        *   `org.hibernate.annotations.Cache`
    *   `@Cache`:  For other cache regions when there are multiple cache configurations.
        *   `org.hibernate.annotations.Cache`
    *   `@CachePut`:  Used to always update the cache, the return of the method will be stored in the cache.
        *  `org.springframework.cache.annotation.CachePut`
    *   `@CacheEvict`  to remove an object from the cache.
        * `org.springframework.cache.annotation.CacheEvict`
*  **Cache Invalidation**
    * How to evict an object from cache.
    * Common methods: TTL, LFU, LRU
*   **Cache Strategies:**
    *   Read-through: The cache attempts to read from the cache and if it is not there it is retrieved from the database, then it's stored on the cache.
    *   Write-through: Every write operation will also update the cache.
    *   Write-behind: Write operations are not immediately performed on the database, but are cached to be written later.
*   **Examples:**

```java
import org.hibernate.annotations.Cache;
import org.hibernate.annotations.CacheConcurrencyStrategy;
import io.quarkus.hibernate.orm.panache.PanacheEntity;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "products")
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE, region = "products") // Enable caching for product entity, using read write strategy and products cache region name.
public class Product extends PanacheEntity{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    public Long id;
    public String name;
    public double price;

}
```

```java
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;

@ApplicationScoped
public class ProductService {

  @Inject
    ProductRepository productRepository;


  @Cacheable(value = "products") // Cache the return of this method
    public Product findProduct(Long id){
        return productRepository.findById(id);
    }

 @CachePut(value = "products", key = "#product.id") // Update the cache with the new object
    public Product updateProduct(Product product) {
    productRepository.update(product);
    return product;

    }

 @CacheEvict(value = "products", key = "#id") // Remove an object from the cache
   public void deleteProduct(Long id){
     productRepository.deleteById(id);
   }

}
```

*   **Hands-on Lab:**
    *   Enable second-level caching for the `Product` entity using the Infinispan.
    *   Configure the cache eviction policy using the application.properties.
    *   Add the `@Cacheable` annotation to the find methods in the `ProductService`, to use the second level cache.
    *   Add `@CachePut` to the update methods of `ProductService`, so every update also updates the cache.
    *   Add `@CacheEvict` to the delete methods of `ProductService`.
    *   Test the performance difference with and without caching (use tools to measure the time for queries).
    *   Observe how the cache works using tools like JConsole or VisualVM to verify cache hits and misses.




