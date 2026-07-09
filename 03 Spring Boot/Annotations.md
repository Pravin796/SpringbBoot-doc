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

# What is @MappedSuperclass?

@MappedSuperclass tells JPA:

"This class is not an entity itself, but its fields should be inherited by other entities."

Think of it like a template that provides common columns to child entities.

Without @MappedSuperclass

Suppose you have two entities:

@Entity
public class User {

    @Id
    private Long id;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    private String name;
}
@Entity
public class Product {

    @Id
    private Long id;

    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;

    private String title;
}

Notice anything?

createdAt and updatedAt are duplicated.

Imagine 20 entities.

You'd repeat the same fields 20 times.

Solution: Create a base class
@MappedSuperclass
public class BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private LocalDateTime createdAt;

    private LocalDateTime updatedAt;
}

Now:

@Entity
public class User extends BaseEntity {

    private String name;
}
@Entity
public class Product extends BaseEntity {

    private String title;
}

No duplication!

What happens in the database?

Suppose you save a User.

The table becomes

users
----------------------------
id
created_at
updated_at
name

If you save a Product

products
----------------------------
id
created_at
updated_at
title

Notice:

There is NO BaseEntity table.

Instead, its fields become part of each child entity's table.

Visual representation
              BaseEntity
        (@MappedSuperclass)
        --------------------
        id
        createdAt
        updatedAt
               ▲
      -------------------
      ▲                 ▲
    User             Product
    -----            -------
    name             title

Database:

users
---------
id
created_at
updated_at
name
products
---------
id
created_at
updated_at
title
Why isn't it an entity?

Because a mapped superclass cannot exist on its own.

You cannot do

entityManager.find(BaseEntity.class, 1);

❌ Not allowed.

You also cannot write

@Repository
public interface BaseRepository
        extends JpaRepository<BaseEntity, Long> {
}

❌ Because BaseEntity is not an entity.

Only User and Product are entities.

Real-world example

Most Spring Boot applications have something like this:

@MappedSuperclass
@Getter
@Setter
public abstract class BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @CreationTimestamp
    private LocalDateTime createdAt;

    @UpdateTimestamp
    private LocalDateTime updatedAt;
}

Then:

@Entity
public class User extends BaseEntity {

    private String name;
}
@Entity
public class Order extends BaseEntity {

    private Double amount;
}
@Entity
public class Product extends BaseEntity {

    private String title;
}

Every entity automatically gets:

id
createdAt
updatedAt
Common use cases

@MappedSuperclass is perfect for fields shared across many entities, such as:

id
createdAt
updatedAt
createdBy
updatedBy
version
deleted

For example:

@MappedSuperclass
public class Auditable {

    private LocalDateTime createdAt;

    private LocalDateTime updatedAt;

    private String createdBy;

    private String updatedBy;
}
Difference between @MappedSuperclass and @Entity
Feature	@MappedSuperclass	@Entity
Has its own table?	❌ No	✅ Yes
Can be queried?	❌ No	✅ Yes
Can have a repository?	❌ No	✅ Yes
Fields inherited by children?	✅ Yes	Only if using JPA inheritance (@Inheritance)
Exists independently?	❌ No	✅ Yes
@MappedSuperclass vs @Inheritance

This is a common interview question.

@MappedSuperclass
BaseEntity
   ▲
--------
▲      ▲
User  Product

Tables:

users
id
created_at
name
products
id
created_at
title

There is no BaseEntity table.

@Inheritance
Person
   ▲
---------
▲       ▲
Student Teacher

With @Inheritance, Person is also an entity. Depending on the inheritance strategy, JPA creates tables that represent the inheritance hierarchy, and you can query Person directly.

Best practices
✅ Use @MappedSuperclass for common entity fields.
✅ Make the class abstract so it isn't instantiated directly.
✅ Keep only reusable state and behavior there.
❌ Don't create repositories for mapped superclasses.
❌ Don't expect a separate database table for the mapped superclass.


# What is @EntityListeners?

@EntityListeners tells JPA:

"Whenever something happens to this entity (before save, after save, before update, etc.), call another class to handle that event."

That "another class" is called an Entity Listener.

Think of it like an event listener in JavaScript.

JavaScript analogy
button.addEventListener("click", function () {
    console.log("Button clicked!");
});

Here:

Event = Click
Listener = Function

Similarly, in JPA:

@EntityListeners(UserListener.class)
@Entity
public class User {
}

Here:

Event = Save, Update, Delete...
Listener = UserListener
Why do we need it?

Suppose every time a user is saved, you want to:

set createdAt
update updatedAt
write a log
generate an audit record

Instead of writing that logic everywhere:

user.setCreatedAt(LocalDateTime.now());
userRepository.save(user);

JPA can do it automatically.

Example
Step 1: Entity
@Entity
@EntityListeners(UserListener.class)
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    private LocalDateTime createdAt;
}
Step 2: Listener
public class UserListener {

    @PrePersist
    public void beforeSave(User user) {
        user.setCreatedAt(LocalDateTime.now());
        System.out.println("User is about to be saved");
    }
}
Step 3: Save
User user = new User();
user.setName("Pravin");

userRepository.save(user);

Before Hibernate executes the SQL:

INSERT INTO users ...

it automatically calls

beforeSave(user);

which sets

createdAt = LocalDateTime.now();

without you writing it in your service.

Lifecycle Events

JPA provides several lifecycle annotations that can be used inside an entity listener (or directly in the entity).

Annotation	When it is called
@PrePersist	Before a new entity is inserted
@PostPersist	After the insert completes
@PreUpdate	Before an existing entity is updated
@PostUpdate	After the update completes
@PreRemove	Before an entity is deleted
@PostRemove	After the delete completes
@PostLoad	After an entity is loaded from the database
Example flow

Suppose:

userRepository.save(user);

If it's a new user:

Create User

↓

@PrePersist

↓

INSERT INTO users

↓

@PostPersist

If you update a user:

Update User

↓

@PreUpdate

↓

UPDATE users

↓

@PostUpdate
Why use a separate listener class?

You could put the methods directly inside the entity:

@Entity
public class User {

    @PrePersist
    public void beforeSave() {
        createdAt = LocalDateTime.now();
    }
}

This works perfectly.

But if many entities need the same behavior, it's cleaner to move that logic into a separate listener class and attach it with @EntityListeners.

Real-world Spring Boot example

You may have seen something like this:

@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseEntity {

    @CreatedDate
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}

This is part of Spring Data JPA's auditing support.

Here:

@EntityListeners(AuditingEntityListener.class) registers Spring's auditing listener.
The listener automatically fills fields marked with @CreatedDate and @LastModifiedDate.

So if you save:

User user = new User();
user.setName("Pravin");

userRepository.save(user);

you don't need to write:

user.setCreatedAt(...);
user.setUpdatedAt(...);

The auditing listener does it automatically.

@EntityListeners vs lifecycle annotations

@EntityListeners specifies which class should receive lifecycle callbacks.

The lifecycle annotations (@PrePersist, @PreUpdate, etc.) specify which method should run for a particular event.

Example:

@EntityListeners(UserListener.class)
@Entity
public class User {
}
public class UserListener {

    @PrePersist
    public void beforeInsert(User user) {
        // Runs before insert
    }

    @PreUpdate
    public void beforeUpdate(User user) {
        // Runs before update
    }
}

# Summary
@EntityListeners registers one or more listener classes for an entity.
Listener classes react to JPA lifecycle events such as insert, update, delete, and load.
Lifecycle methods are marked with annotations like @PrePersist, @PostUpdate, and @PreRemove.
A common real-world use is auditing, where AuditingEntityListener automatically populates fields like createdAt and updatedAt.
Keeping lifecycle logic in listeners helps keep your entity classes focused on representing data rather than handling cross-cutting concerns like auditing or logging.