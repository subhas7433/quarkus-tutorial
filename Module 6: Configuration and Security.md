**6.1 Application Configuration (2.5 - 3.5 hours)**

*   **The Importance of Configuration Management:**
    *   Why it's bad to hardcode configuration values (database URLs, API keys, etc.) into the application code.
    *   Why it's important to externalize configuration.
    *   Benefits of flexible configuration:
        *   Easier deployments to different environments (development, staging, production).
        *   Easier changes without needing to recompile or redeploy.
        *   Securely manage sensitive configuration data (passwords, API keys).
*   **Configuration Sources in Quarkus:**
    *   `application.properties` (or `application.yaml`): Default configuration file for Quarkus.
    *   Environment variables: System or user-defined environment variables.
    *   MicroProfile Config: A standard API for accessing configuration from multiple sources.
        *   Quarkus uses MicroProfile Config internally
        *   Allows you to create custom configuration sources.
    *   Command-line arguments: Values passed to the application when it starts up.
    *   Externalized configuration servers (e.g., HashiCorp Consul, Apache ZooKeeper, Kubernetes ConfigMaps).
*   **`application.properties` File:**
    *   How to define configuration properties in the `application.properties` file.
    *   Examples of properties for common scenarios (database, server port, logging, etc.).
    *   Using simple name/value pairs (e.g., `quarkus.http.port=8080`).
*   **Profiles (dev, test, prod):**
    *   Quarkus support for different configuration profiles.
        *  A profile is used to group different properties for different environments.
    *   Using the `-Dquarkus.profile=<profile>` flag to activate a profile.
        *  `mvn quarkus:dev -Dquarkus.profile=dev`
    *   How to override default settings for different environments using profiles.
    *   Using profile-specific files (`application-dev.properties`, `application-test.properties`, etc).
*   **MicroProfile Config:**
    *   A standardized API to retrieve configuration values in Java.
    *   Using `@ConfigProperty` annotation to inject values into fields.
        *   `org.eclipse.microprofile.config.inject.ConfigProperty`
    *   Accessing configuration values programmatically using the `Config` interface.
        *   `org.eclipse.microprofile.config.Config`
    *   Example:

```java
import org.eclipse.microprofile.config.inject.ConfigProperty;

import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import org.eclipse.microprofile.config.Config;
@Path("/config")
public class ConfigResource {

  @Inject
    Config config;

  @ConfigProperty(name = "my.greeting")
    String greetingMessage;


  @GET
  @Produces(MediaType.TEXT_PLAIN)
    public String getConfiguredValues(){

       return "Configured value : " + greetingMessage + " Other config property: " +
               config.getValue("quarkus.application.name", String.class);
    }
}
```

*   **Externalized Configuration:**
    *   Using environment variables for configuration.
        *   `export MY_DATABASE_URL="jdbc://..."`
        *   `MY_DATABASE_URL` will automatically overwrite the value from `application.properties`
    *   Brief overview of other ways to externalize configuration (Kubernetes ConfigMaps, config servers like Consul or Spring Cloud Config).
*   **Hands-on Lab:**
    *   Create a new property in the `application.properties` file for a custom greeting message.
    *   Create a new REST endpoint that shows the customized value from the `application.properties` using the `Config` object and using the annotation `@ConfigProperty`.
    *   Create an `application-dev.properties` file for development environment, to override the default greeting message.
    *   Run the app with and without `dev` profile to see the changes.
    *   Configure the database URL through an environment variable and test it.

**6.2 Securing Quarkus Applications (2.5 - 3.5 hours)**

*   **Authentication and Authorization:**
    *   **Authentication:** Verifying the identity of the user/client (who are you?).
        *   Credentials (username/password, API keys, tokens).
        *   Different authentication methods.
    *   **Authorization:** Determining what the authenticated user/client is allowed to do (what are you allowed to do?).
        *   Access control to endpoints and resources.
        *   Role-based access control (RBAC).
*   **SmallRye JWT (JSON Web Tokens):**
    *   JWT is a standard method for secure authentication and authorization.
    *   How JWTs are structured (header, payload, signature).
    *   Using JWTs in Quarkus (keycloak example).
        *   Configure the application to validate the JWT tokens.
        *   Protect endpoints by requiring a valid JWT token.
    *  Configuring Keycloak to generate and sign tokens.
*   **OAuth 2.0 and OpenID Connect:**
    *   OAuth 2.0: Authorization protocol that allows third-party applications to access resources on behalf of a user.
    *   OpenID Connect (OIDC): An authentication layer built on top of OAuth 2.0.
    *   Using Keycloak for OAuth 2.0 and OpenID Connect.
    *   How to configure Quarkus to use OIDC for authentication and authorization.
    *    How to obtain access and ID tokens.
*   **Role-Based Access Control (RBAC):**
    *   Assigning roles to users and granting them access to specific resources based on their roles.
    *   Using annotations to protect JAX-RS endpoints based on roles.
        * `@RolesAllowed`, `@PermitAll`, `@DenyAll`.
        * `javax.annotation.security.*`
    *   Example: Only users with the “admin” role can access certain endpoints.
*   **Programmatic Security:**
    *   Accessing the security context programmatically:
        *   Get the authenticated user's information.
        *   Check for roles.
    *   `javax.security.core.SecurityContext`.
*   **Examples:**

`application.properties`

```properties
# OIDC properties
quarkus.oidc.enabled=true
quarkus.oidc.auth-server-url=http://localhost:8080/realms/myrealm
quarkus.oidc.client-id=quarkus-app
quarkus.oidc.credentials.secret=secret
quarkus.oidc.discovery-enabled=true
```

`ProductResource`

```java
import javax.annotation.security.RolesAllowed;
import javax.annotation.security.PermitAll;
import javax.inject.Inject;
import javax.ws.rs.*;
import javax.ws.rs.core.MediaType;
import java.util.List;
import org.eclipse.microprofile.jwt.JsonWebToken;
@Path("/products")
public class ProductResource {

  @Inject
    JsonWebToken jsonWebToken;

  @GET
  @PermitAll // Accessible by everyone.
  @Produces(MediaType.APPLICATION_JSON)
  public List<Product> getAllProducts() {
    return productService.listAll();
  }

  @GET
  @RolesAllowed("admin") // Requires admin role.
  @Path("/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public Product getProductById(@PathParam("id") Long id) {
    return productService.findById(id);
  }

  @GET
  @Path("/username")
  @RolesAllowed({"admin","user"}) // Requires admin or user role
  @Produces(MediaType.TEXT_PLAIN)
  public String getUsername(){
        return "user: " + jsonWebToken.getName();
    }

}
```
*   **Hands-on Lab:**
    *   Set up a Keycloak server and configure a realm and a user.
    *   Configure your Quarkus application to use Keycloak for authentication and authorization.
    *   Add `@RolesAllowed` annotation to the product API endpoint to restrict access based on roles.
    *   Create an endpoint that shows the username of the authenticated user.
    *   Obtain a JWT token from Keycloak and use it to access the secured endpoints.
    *   Test the endpoints with different roles and see how access is granted or denied.



**6.3 Using Vault with Quarkus (1.5 - 2.5 hours)**

*   **What is HashiCorp Vault?**
    *   A tool for managing secrets and protecting sensitive data.
    *   Centralized storage for keys, passwords, tokens, and certificates.
    *   Provides a secure way to access and manage secrets.
    *   Vault can manage any kind of secret, you define the type of secrets that will be managed.
*   **Why Use Vault?**
    *   Centralized secrets management: store, access, and revoke secrets from a single place.
    *   Fine-grained access control: control access to secrets based on policies.
    *   Audit logging: track who accesses which secrets.
    *   Dynamic secrets: generate secrets on-demand and revoke them after use.
    *   Secrets rotation: automatically rotate secrets to enhance security.
    *   Prevents secrets from being exposed in code or configuration files.
*   **Integrating Vault with Quarkus:**
    *   Using the Quarkus Vault extension (`quarkus-vault`).
        *    `quarkus-vault` can be configured using different authentication types.
        *   You need to add it as a dependency in your application.
    *   How to configure the Vault extension (Vault address, authentication method, etc.).
        *  Using `application.properties` to configure the connection to the Vault server.
        *  You need to configure your Vault Server with the proper policies.
    *   Different authentication methods for Vault:
        *   Token authentication.
        *   AppRole authentication.
        *   Kubernetes authentication.
    *  Using different secret engines (e.g., key/value, database, PKI).
*   **Accessing Secrets with `@ConfigProperty`:**
    *   Retrieving secrets using `@ConfigProperty` (similar to accessing other configuration properties).
    *   Vault secrets are retrieved using the `vault:` prefix in the configuration key.
        * Example:  `@ConfigProperty(name = "vault:secret/my-secret-name:my-secret-key")`.
*   **Accessing Secrets Programmatically:**
    *   Accessing Vault secrets using `VaultClient`.
        *  Create an object of type `VaultClient`.
    *  Access secrets using `read` method from the VaultClient.
    *   Using the Vault Client to access dynamic secrets.
*   **Examples:**

`application.properties`:
```properties
quarkus.vault.url=http://localhost:8200
quarkus.vault.authentication.token.token=s.some-vault-token
quarkus.vault.kv-secret-engine=secret
```

`VaultResource`
```java
import org.eclipse.microprofile.config.inject.ConfigProperty;
import javax.inject.Inject;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import com.bettercloud.vault.Vault;

@Path("/vault")
public class VaultResource {

  @Inject
  VaultClient vaultClient;

  @ConfigProperty(name = "vault:secret/my-app-config:database_password")
    String databasePassword;

    @GET
   @Path("/config")
    @Produces(MediaType.TEXT_PLAIN)
  public String getConfiguredSecret() {
     return "Database Password : " + databasePassword;
   }

   @GET
   @Path("/secret")
   @Produces(MediaType.TEXT_PLAIN)
   public String getSecretProgramatically() {
       String databaseUrl =  vaultClient.readSecret("secret/my-app-config", "database_url");
      return "Database URL: " + databaseUrl;
    }
}

```

`VaultClient`
```java
import com.bettercloud.vault.Vault;
import com.bettercloud.vault.VaultConfig;
import com.bettercloud.vault.VaultException;
import javax.enterprise.context.ApplicationScoped;
import javax.inject.Inject;
import org.eclipse.microprofile.config.inject.ConfigProperty;

@ApplicationScoped
public class VaultClient {

  @ConfigProperty(name = "quarkus.vault.url")
    String vaultUrl;

  @ConfigProperty(name = "quarkus.vault.authentication.token.token")
    String vaultToken;


  public String readSecret(String path, String key) {
      try {
          final VaultConfig config = new VaultConfig()
            .address(vaultUrl)
            .token(vaultToken)
              .build();
        final Vault vault = new Vault(config);
         return vault.logical().read(path).getData().get(key);
     } catch(VaultException e){
          return null;
      }
   }
}

```

*   **Hands-on Lab:**
    *   Set up a local Vault server (you can use the docker image provided by HashiCorp).
    *   Create a secret in Vault that contains database URL and password (or other sensitive data).
    *   Configure your Quarkus application to connect to Vault using the token authentication.
    *   Access the database URL and password secrets using `@ConfigProperty` in a REST endpoint.
    *  Create a new REST endpoint that retrieves a secret from Vault using the `VaultClient` and print the value.
    *   Test the endpoints and observe that you can retrieve the secrets from Vault.

