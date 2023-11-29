# Fidelity|Real Interview|Experienced 6 years|Microservices|Springboot

# 1.How to secure your application in spring boot 

Securing a Spring Boot application involves implementing authentication and authorization mechanisms to control access to resources. Spring Security is a powerful and widely used framework for securing Java-based applications, including Spring Boot applications. Below is a step-by-step guide to secure a Spring Boot application using Spring Security.

### Step 1: Add Spring Security Dependency

In your `pom.xml` file, add the following dependency for Spring Security:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### Step 2: Configure Security in `application.properties` or `application.yml`

Configure security settings in your `application.properties` or `application.yml` file. For example:

```yaml
# application.yml
spring:
  security:
    user:
      name: user
      password: password
    roles: USER
```

This configuration creates a default user with the username "user," password "password," and the role "USER."

### Step 3: Customize Security Configuration

Create a class that extends `WebSecurityConfigurerAdapter` to customize the security configuration:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/public/**").permitAll() // Publicly accessible URLs
                .anyRequest().authenticated() // All other URLs require authentication
                .and()
            .formLogin()
                .loginPage("/login") // Custom login page
                .permitAll()
                .and()
            .logout()
                .permitAll();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

In this example:
- URLs starting with "/public/" are accessible without authentication.
- All other URLs require authentication.
- A custom login page is specified ("/login").
- The application supports logout.

### Step 4: Secure Methods in Controllers

Use `@PreAuthorize` annotation to secure methods in your controllers:

```java
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {

    @GetMapping("/secured")
    @PreAuthorize("hasRole('USER')")
    public String securedResource() {
        return "This is a secured resource.";
    }

    @GetMapping("/admin")
    @PreAuthorize("hasRole('ADMIN')")
    public String adminResource() {
        return "This is an admin resource.";
    }
}
```

In this example:
- The "/secured" endpoint requires the user to have the role "USER."
- The "/admin" endpoint requires the user to have the role "ADMIN."

### Step 5: Secure Service Layer

Use `@Secured` or `@PreAuthorize` in your service layer to secure methods:

```java
import org.springframework.security.access.annotation.Secured;
import org.springframework.stereotype.Service;

@Service
public class MyService {

    @Secured("ROLE_USER")
    public String secureMethod() {
        return "This method is secured.";
    }
}
```

### Step 6: Test the Application

Run your Spring Boot application and test the security features. Access the secured resources with the provided username and password. Ensure that access is denied to unauthorized users.

Remember to customize these configurations based on your application's specific requirements. Spring Security provides a wide range of options for customizing authentication providers, user details services, and access control rules. The example above provides a basic setup to get you started.
