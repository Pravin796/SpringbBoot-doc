@Configuration
@Bean
@Qualifier
@Primary
@value


These annotations are very important in **Spring Boot configuration and dependency injection**. Let’s understand them one by one with **simple examples**.

---

# 1. `@Configuration`

`@Configuration` is used to **define a class that contains bean definitions**.

It tells Spring:

> "This class contains methods that create Spring beans."

Example:

import org.springframework.context.annotation.Configuration;  
  
@Configuration  
public class AppConfig {  
  
}

Spring will treat this class as a **configuration class**.

Inside this class, we usually define beans using **`@Bean`**.

---

# 2. `@Bean`

`@Bean` is used to **manually create and register a bean inside the Spring IoC Container**.

Normally we use:

@Component  
@Service  
@Repository

But sometimes we need **manual bean creation**.

Example:

import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
@Configuration  
public class AppConfig {  
  
    @Bean  
    public PaymentService paymentService(){  
        return new PaymentService();  
    }  
  
}

Now **Spring will create the object of `PaymentService`** and store it as a **bean**.

You can inject it anywhere:

@RestController  
public class OrderController {  
  
    private final PaymentService paymentService;  
  
    public OrderController(PaymentService paymentService){  
        this.paymentService = paymentService;  
    }  
  
}

---

# When Do We Use `@Bean`?

We use it when:

✔ The class is **from a third-party library**  
✔ We **cannot modify the source code**  
✔ We need **custom object creation**

Example:

@Bean  
public ObjectMapper objectMapper(){  
    return new ObjectMapper();  
}

---

# 3. `@Qualifier`

`@Qualifier` is used when **multiple beans of the same type exist**.

Example:

Two payment services:

@Service  
public class PaypalService implements PaymentService {  
}

@Service  
public class StripeService implements PaymentService {  
}

Now Spring gets confused:

Which bean should be injected?

Error:

NoUniqueBeanDefinitionException

Solution → Use **`@Qualifier`**.

Example:

@RestController  
public class OrderController {  
  
    private final PaymentService paymentService;  
  
    public OrderController(@Qualifier("paypalService") PaymentService paymentService){  
        this.paymentService = paymentService;  
    }  
  
}

Now Spring injects **PaypalService**.

---

# 4. `@Primary`

`@Primary` tells Spring:

> "This bean should be the default choice when multiple beans exist."

Example:

@Service  
@Primary  
public class PaypalService implements PaymentService {  
}

Another service:

@Service  
public class StripeService implements PaymentService {  
}

Now when Spring injects:

private final PaymentService paymentService;

Spring will automatically use **PaypalService** because it is marked `@Primary`.

---

# Difference Between `@Qualifier` and `@Primary`

|Feature|@Primary|@Qualifier|
|---|---|---|
|Purpose|Default bean|Specific bean|
|When used|Global preference|Explicit selection|
|Annotation location|Bean class|Injection point|

---

# Example Flow

PaymentService (Interface)  
       ↑  
PaypalService  
StripeService

If:

PaypalService → @Primary

Spring injects **PaypalService automatically**.

If you want Stripe:

@Qualifier("stripeService")

---

# Quick Summary

|Annotation|Purpose|
|---|---|
|`@Configuration`|Defines configuration class|
|`@Bean`|Manually creates Spring bean|
|`@Qualifier`|Select specific bean|
|`@Primary`|Default bean when multiple beans exist|

# @Value

`@Value` in Spring Boot is used to **inject values from configuration files or environment variables into a Spring bean**.

In simple words:

> `@Value` helps you read values from **`application.properties` or `application.yml`** and use them in your Java classes.

---

# 1. Basic Example of `@Value`

Suppose you have this in **application.properties**

app.name=JobHunt

Now you can inject it into a class.

import org.springframework.beans.factory.annotation.Value;  
import org.springframework.stereotype.Component;  
  
@Component  
public class AppService {  
  
    @Value("${app.name}")  
    private String appName;  
  
    public void printName(){  
        System.out.println(appName);  
    }  
}

Output:

JobHunt

Spring reads the value from **properties file** and injects it.

---

# 2. Injecting Server Properties

Example from **application.properties**

server.port=8081

Use it like this:

@Value("${server.port}")  
private int port;

Now `port = 8081`.

---

# 3. Default Value with `@Value`

You can also provide a **default value** if the property doesn't exist.

Example:

@Value("${app.version:1.0}")  
private String version;

If `app.version` is missing → Spring uses **1.0**.

---

# 4. Using `@Value` in Controller

Example:

@RestController  
public class HelloController {  
  
    @Value("${app.name}")  
    private String appName;  
  
    @GetMapping("/app")  
    public String getAppName(){  
        return appName;  
    }  
  
}

API response:

JobHunt

---

# 5. Injecting Environment Variables

Example:

@Value("${JAVA_HOME}")  
private String javaHome;

Spring can read **system environment variables**.

---

# 6. Injecting Expressions (SpEL)

Spring also supports **Spring Expression Language (SpEL)**.

Example:

@Value("#{2 + 3}")  
private int result;

Output:

5

---

# 7. Injecting List Values

Example in **application.properties**

app.languages=Java,Python,JavaScript

Use it like this:

@Value("${app.languages}")  
private String[] languages;

---

# 8. Where `@Value` Is Commonly Used

Developers use it for:

- Database configuration
- API keys
- Application settings
- External configuration

Example:

@Value("${spring.datasource.url}")  
private String dbUrl;

---

# 9. Important Interview Definition

**What is `@Value` in Spring?**

Answer:

> `@Value` is used to inject values from external configuration sources like `application.properties`, `application.yml`, environment variables, or system properties into Spring beans.

---

# 10. Best Practice (Advanced)

For many properties, instead of multiple `@Value`, we use:

@ConfigurationProperties

Example:

app.name=JobHunt  
app.version=1.0  
app.owner=Pravin

Mapped to a class.