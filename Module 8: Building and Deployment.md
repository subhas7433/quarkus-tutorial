**8.1 Building Quarkus Applications (2 - 3 hours)**

*   **The Build Process:**
    *   Transforming your application's source code into an executable artifact (JAR or native executable).
    *   Automating tasks such as compiling, testing, packaging.
    *   Using build tools (Maven or Gradle) to manage dependencies and the build lifecycle.
*   **Maven vs. Gradle:**
    *   **Maven:** A popular build tool based on a declarative approach using XML configurations (`pom.xml`).
        *   A very mature build tool with a large community and plugins.
        *   Good for simpler projects and when you prefer conventions over configuration.
    *   **Gradle:** A modern build tool based on a programming approach with groovy or kotlin (`build.gradle`).
        *   More flexible and customizable than Maven.
        *   Good for complex projects and when you need more control of the build process.
        *   Good for microservices architectures with multi module projects.
    *   Both Maven and Gradle are supported by Quarkus.
    *   The choice between them depends on personal preferences and project needs.
*   **The Maven Build Process:**
    *   Understanding the basic Maven build phases (compile, test, package, install, deploy).
    *   Using the `pom.xml` file to define dependencies, plugins, and other build configurations.
    *   Running basic Maven commands:
        *   `mvn clean`: Removes all previous build outputs.
        *   `mvn compile`: Compiles your Java code.
        *   `mvn test`: Runs the tests.
        *   `mvn package`: Packages the application into a JAR file.
        *   `mvn install`: Installs the JAR into your local Maven repository.
        *   `mvn deploy`: Deploys the JAR to a remote repository.
*   **The Gradle Build Process:**
    *  Understanding the Gradle build phases (compileJava, test, jar, assemble)
    *  Using `build.gradle` file to configure dependencies, plugins, tasks and other build configurations.
     *  Running basic Gradle commands:
          *  `gradle clean`: Removes all previous build outputs.
          *  `gradle compileJava`: Compiles your Java code.
         *  `gradle test`: Runs the tests.
        *   `gradle jar`: Packages the application into a JAR file.
        *   `gradle assemble`: Assemble the application artifacts (includes building the `jar` file)
*   **Quarkus Build Extensions:**
    *   Plugins for Maven (`quarkus-maven-plugin`) or Gradle (`quarkus-gradle-plugin`).
        *   Used to create a build configuration specific for Quarkus.
    *   Provides specific goals and tasks for Quarkus development and build process.
*   **Packaging as a JAR (Uber-JAR):**
    *   Uber-JAR (also known as a "fat JAR"): A JAR file that contains all dependencies of your application.
        *  You can execute it directly with `java -jar app.jar`.
        *  Ideal for simple deployments or running on environments where dependency management is handled separately.
    *   Generating an Uber-JAR using Maven (`mvn package`) or Gradle (`gradle jar`).
    *   It's a traditional way of deploying Java applications in any environment.
    *   Example:
    ```xml
     <plugin>
        <groupId>io.quarkus</groupId>
         <artifactId>quarkus-maven-plugin</artifactId>
          <version>${quarkus.version}</version>
           <executions>
           <execution>
           <goals>
         <goal>build</goal>
             </goals>
           </execution>
         </executions>
         </plugin>
    ```
    ```groovy
    plugins {
        id 'io.quarkus'
    }
    ```
*   **Hands-on Lab:**
    *   Build a Quarkus application using Maven or Gradle.
    *   Verify the output JAR file located in `target` (Maven) or `build/libs` (Gradle).
    *   Run the application directly from the JAR file using the command `java -jar`.
    *  Make sure to add the `quarkus-maven-plugin` or `quarkus-gradle-plugin` in order to have the required functionality.

**8.2 Native Image Compilation with GraalVM (2 - 3 hours)**

*   **What is GraalVM?**
    *   A high-performance polyglot virtual machine developed by Oracle.
    *   Can compile Java bytecode into native executables.
    *   Can also execute other languages (e.g., JavaScript, Python, Ruby).
*   **Benefits of Native Images:**
    *   **Faster startup time:** Native images start up much faster compared to JVM-based applications.
    *   **Reduced memory consumption:** Native images use less memory.
    *   **Improved performance:** Native images can have better performance in some cases (depending on the nature of the application).
    *   **Smaller footprint:** Native images can create smaller executables.
    *   Ideal for serverless applications and constrained environments.
*   **Building Native Images with Quarkus:**
    *   Using the Quarkus Maven or Gradle plugins.
    *   Adding the `native` profile to perform the build for native image.
    *   Requires GraalVM to be installed and configured correctly on your machine.
        *  GraalVM path should be in your `PATH` environment variable
        *   `JAVA_HOME` variable should point to the GraalVM jdk path.
    *   Using the `-Dnative` profile and other properties to build the native image.
    *   Using `docker` to build native images in containers (recommended option).
*  **Using Docker to build Native Image:**
   * It is recommended to use docker to build native image as the build process can require more resources.
   * `quarkus.native.container-build=true` is the property that enables building the native image inside a container
   * The native-image executable is created in the target/native/native-image directory.
*   **Limitations of Native Image Compilation:**
    *   **Ahead-of-time compilation:** All code and dependencies must be known during build time, no dynamic class loading.
    *   **Reflection:** Can be limited without specific configurations.
        *   You must register reflection usage in a file or by using the `@RegisterForReflection` annotation.
    *   **Dynamic proxies:** Need to be explicitly configured.
    *   **Resources:** Required resources (like configuration files, templates etc.) must be included in the image.
    *   **Build time:** Can take longer than building a JAR (can be improved using docker).
    *   **Debugging**: Debugging native images is more complex, and need some special flags to enable debug support (e.g. `quarkus.native.debug-build=true`)
    *  Some libraries are not fully compatible with GraalVM (you should check your dependencies compatibility).
*   **Examples:**

```xml
    <profile>
        <id>native</id>
        <properties>
            <quarkus.package.type>native</quarkus.package.type>
        </properties>
    </profile>
```

`application.properties`
```properties
quarkus.native.container-build=true
```
*   **Hands-on Lab:**
    *   Build a Quarkus application as a native executable using Maven or Gradle (using the `native` profile).
    *   Using docker to build native images.
    *   Verify the output executable located in `target/native/native-image` (or a similar directory).
    *   Run the native executable.
    *  Observe the difference in startup time compared to a JVM application.
    *  Verify if there are any exceptions related to native image limitations, and fix them.
    *   Add a reflection configuration to include the Product entity reflection.





**8.3 Containerizing Quarkus Applications with Docker (2 - 3 hours)**

*   **What is Docker?**
    *   A platform for building, running, and managing containerized applications.
    *   Containers provide a lightweight and portable way to package and run applications.
    *   Containers isolate applications from the underlying operating system, ensuring consistency across environments.
*   **Why Use Docker?**
    *   **Consistency:** Applications run the same way across different environments.
    *   **Isolation:** Applications are isolated from each other, avoiding dependency conflicts.
    *   **Portability:** Easily move applications between different hosts or clouds.
    *   **Scalability:** Easily scale applications up or down.
    *   **Resource efficiency:** Containers are lightweight and efficient, using fewer resources than virtual machines.
*   **Docker Images:**
    *   A snapshot of a container's file system and configurations (read-only template).
    *   Used to create Docker containers (running instances of Docker images).
    *   Images are stored in repositories (e.g., Docker Hub, Quay.io).
*   **Dockerfiles:**
    *   A text file that defines the steps for building a Docker image.
    *   Includes instructions like copying files, setting up environment variables, running commands.
    *   A Dockerfile is the base recipe to build a Docker image.
*   **Creating Dockerfiles for JAR Images:**
    *   Using a base image that has Java installed (e.g., `openjdk:17-slim`).
    *   Copying the JAR file into the Docker image.
    *   Defining the entry point to run the application (e.g., `java -jar app.jar`).
    *   Exposing the application port.
*   **Creating Dockerfiles for Native Images:**
    *   Using a base image that has the necessary libraries.
    *   Copying the native executable into the Docker image.
    *   Defining the entry point to run the native executable.
    *   Exposing the application port.
*   **Building Docker Images:**
    *   Using the `docker build` command to build Docker images from Dockerfiles.
    *   Tagging Docker images using the `-t` flag.
        * `docker build -t my-quarkus-app:1.0 .`
*   **Running Docker Containers:**
    *   Using the `docker run` command to run Docker containers.
    *   Mapping ports between the host and the container using the `-p` flag.
       * `docker run -p 8080:8080 my-quarkus-app:1.0`
*   **Optimizing Docker Images for Quarkus:**
    *   Using smaller base images (e.g., distroless images).
    *   Using multi-stage builds to reduce the size of final image by removing unnecessary files.
    *   Using a health check in docker for testing the availability of the application inside a container.
    *  Using a non-root user for better security
*   **Examples:**

**Dockerfile for JAR:**
```dockerfile
FROM openjdk:17-slim
COPY target/quarkus-app-1.0.0-runner.jar /app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app.jar"]
```
**Dockerfile for Native Image:**
```dockerfile
FROM ubuntu:latest AS builder
RUN apt-get update && apt-get install -y curl
RUN curl -s "https://get.sdkman.io" | bash
RUN bash -c 'source "$HOME/.sdkman/bin/sdkman-init.sh" && sdk install java 22.0.1.23.10-grl'
ENV JAVA_HOME=/root/.sdkman/candidates/java/22.0.1.23.10-grl
ENV PATH=$JAVA_HOME/bin:$PATH
WORKDIR /app
COPY target/quarkus-app-1.0.0-runner /app
RUN chmod +x /app/quarkus-app-1.0.0-runner
FROM ubuntu:22.04
COPY --from=builder /app /app
EXPOSE 8080
ENTRYPOINT ["/app/quarkus-app-1.0.0-runner"]
```

*   **Hands-on Lab:**
    *   Create a Dockerfile for the Uber-JAR created in the previous steps.
    *   Create a Dockerfile for the native executable created in the previous steps.
    *   Build the Docker images using the `docker build` command.
    *   Run the Docker containers using the `docker run` command.
    *   Test the application in the containers using your browser and `curl`.
    *  Use a multi-stage build to minimize the native image size.
     *    Create a `healthcheck` for the containerized application.
     *   Run the container as a non-root user

**8.4 Deploying to Kubernetes (2 - 3 hours)**

*   **What is Kubernetes?**
    *   A platform for automating deployment, scaling, and management of containerized applications.
    *   Provides a way to orchestrate containers and manage them in a cluster.
    *   Open-source container orchestration platform initially developed by Google.
*   **Why Use Kubernetes?**
    *   **Automation:** Automates the deployment and scaling process.
    *   **Scalability:** Easily scale applications up and down.
    *   **Resilience:** Application can be recovered automatically after a failure.
    *   **Flexibility:** Support for a wide variety of application architectures and workloads.
    *   **Resource efficiency:** Optimize resource utilization by scaling and scheduling containers on different nodes.
*   **Kubernetes Concepts:**
    *   **Pods:** The smallest deployable unit in Kubernetes (one or more containers running together).
        *   Pods represents a running instance of your application.
    *   **Deployments:** Describes the desired state of a set of pods (number of replicas, image version, etc.).
        *   Deployment provides declarative updates for pods.
    *   **Services:** Exposes applications running in pods to the outside world or other services inside the cluster.
        *   Provides a way to access your application from inside and outside Kubernetes.
    *   **Namespaces:** A way to organize and isolate resources within the cluster.
    *   **Labels and Selectors:** Key/value pairs used to identify Kubernetes resources.
        *   Used to organize and filter pods, deployments, and services.
*   **Creating Kubernetes Manifests:**
    *   YAML files that define Kubernetes resources (deployments, services, pods).
    *   Manifests specify the desired state of your application.
*   **Deploying Quarkus Applications to Kubernetes:**
    *   Create a Kubernetes deployment manifest for your application.
    *   Create a Kubernetes service manifest to expose your application (e.g., `NodePort` or `LoadBalancer`).
    *   Apply the manifests using the `kubectl apply` command.
        *  `kubectl apply -f deployment.yaml`
        *  `kubectl apply -f service.yaml`
    *   Check the status of deployments and services using the `kubectl get` command.
         *   `kubectl get deployments`
         *   `kubectl get pods`
         *   `kubectl get services`
    *   Scale the application by changing the replica count of the deployment.
        *  `kubectl scale deployment my-quarkus-app --replicas=3`
*   **Using Minikube or kind:**
    *   Minikube: A lightweight Kubernetes implementation to run a local cluster (for learning and testing purposes).
    *   kind: A lightweight Kubernetes implementation to run a local cluster with Docker (for learning and testing purposes).
    *   Both tools are easy to set up and use for local Kubernetes environments.
*  **Examples:**

**deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-quarkus-app
  labels:
    app: my-quarkus-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-quarkus-app
  template:
    metadata:
      labels:
        app: my-quarkus-app
    spec:
      containers:
      - name: my-quarkus-app
        image: my-quarkus-app:1.0
        ports:
        - containerPort: 8080
```
**service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-quarkus-app-service
spec:
  selector:
    app: my-quarkus-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```

*   **Hands-on Lab:**
    *   Set up a local Kubernetes cluster using Minikube or kind.
    *   Create Kubernetes manifests for your Quarkus application (deployment and service).
    *   Deploy your application to Kubernetes using `kubectl apply`.
    *   Test that the application is accessible through the Kubernetes service.
    *   Scale your application by changing the replica count in the deployment manifest.
        *  Test that scaling the pods works correctly.
    *  Check the logs of your application inside the Kubernetes pods.




**8.5 Deploying to Serverless Platforms (1.5 - 2.5 hours)**

*   **What are Serverless Platforms?**
    *   A computing model where the infrastructure is managed by the cloud provider.
    *   Developers focus solely on writing and deploying their code as functions.
    *   Also known as Function-as-a-Service (FaaS).
    *   The platform is responsible for provisioning and managing resources, scaling, and executing functions based on events.
*   **Key Characteristics of Serverless Platforms:**
    *   **No server management:** No need to manage servers.
    *   **Automatic scaling:** Functions automatically scale based on demand.
    *   **Event-driven:** Functions are triggered by events (e.g., HTTP requests, database updates, messages).
    *   **Pay-per-use:** Pay only for the compute time used by functions.
    *   **Simplified deployment:** Just deploy your code and the platform manages the execution.
*   **Popular Serverless Platforms:**
    *   **AWS Lambda:** Amazon's serverless compute service.
    *   **Azure Functions:** Microsoft's serverless compute service.
    *   **Google Cloud Functions:** Google's serverless compute service.
    *   Overview of other platforms like IBM Cloud Functions, OpenFaaS, Knative.
*   **Quarkus Extensions for Serverless Platforms:**
    *   Quarkus provides specific extensions for different serverless platforms:
        *   `quarkus-amazon-lambda`: For deploying to AWS Lambda.
        *   `quarkus-azure-functions`: For deploying to Azure Functions.
        *   `quarkus-google-cloud-functions`: For deploying to Google Cloud Functions.
        *  `quarkus-funqy` : for generic function support.
    *   These extensions simplify the process of developing and deploying Quarkus functions.
        *   Adapting the applications to fit in the serverless environment.
        *   Handling event triggers and platform-specific requirements.
*   **Deploying Quarkus Applications as Serverless Functions:**
    *   Adding the appropriate serverless extension to your project.
    *   Writing code that acts as serverless function handler (e.g., implement `RequestHandler` interface for AWS Lambda).
    *   Configuring the platform settings (e.g., region, function name, execution role, memory allocation) for your cloud provider.
    *   Packaging your application into a deployable artifact (e.g., a JAR or ZIP file).
    *   Deploying the function using the platform's command-line interface or web console.
    *  The code must have some `entrypoint`, for AWS lambda this can be the method that implements the `RequestHandler` interface.
*   **Overview of Common Use Cases:**
    *   Processing data from object storage (e.g., image resizing, data transformation).
    *   Handling webhooks or API requests.
    *   Executing scheduled tasks.
    *   Responding to database events.
    *   Messaging events consumption (e.g. kafka events).
*   **Limitations and Considerations:**
    *   Cold starts: Delay in executing a function for the first time (can be minimized with native images).
    *   Execution limits: There are limits on execution time.
    *   State management: Serverless functions are stateless, which can require external state stores.
    *   Vendor lock-in: Using a specific platform can lock you into that specific provider's ecosystem.
    *  Debugging can be more complex than debugging traditional applications.

*   **Examples (Conceptual - AWS Lambda using quarkus-amazon-lambda):**

`application.properties`

```properties
quarkus.lambda.handler=org.acme.lambda.LambdaHandler::handleRequest
quarkus.lambda.package-type=zip
```

`LambdaHandler`
```java
package org.acme.lambda;

import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import com.amazonaws.services.lambda.runtime.events.APIGatewayProxyRequestEvent;
import com.amazonaws.services.lambda.runtime.events.APIGatewayProxyResponseEvent;
import javax.inject.Named;

@Named("hello")
public class LambdaHandler implements RequestHandler<APIGatewayProxyRequestEvent, APIGatewayProxyResponseEvent> {

  @Override
   public APIGatewayProxyResponseEvent handleRequest(APIGatewayProxyRequestEvent input, Context context) {
      APIGatewayProxyResponseEvent response = new APIGatewayProxyResponseEvent();
        response.setBody("Hello from lambda");
        response.setStatusCode(200);
        return response;
   }
}
```

*   **Hands-on Lab (Optional):**
    *   Choose a serverless platform (e.g., AWS Lambda).
    *   Add the corresponding Quarkus extension to your project.
    *   Create a simple Quarkus application that can be deployed as a serverless function (e.g., returns a message based on event data).
    *   Configure the function for the target platform (AWS, Azure, or Google Cloud).
    *   Package the application into a deployable artifact (JAR or ZIP file).
    *   Deploy the function to the selected platform using the command-line interface or the web console.
    *   Test your serverless function.

