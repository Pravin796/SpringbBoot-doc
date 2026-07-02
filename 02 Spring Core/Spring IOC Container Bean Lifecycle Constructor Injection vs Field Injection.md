These three concepts are **core fundamentals of Spring Boot**. If you understand them well, the rest of Spring becomes much easier.

---

# 1. Spring IoC Container

**IoC = Inversion of Control**

It means **Spring controls the creation and management of objects**, not the developer.

Normally in Java, we create objects like this:

OrderService orderService = new OrderService();

Here **you control the object creation**.

But in Spring:

@Service  
public class OrderService {  
}

Spring automatically creates the object and manages it inside the **IoC Container**.

Then you can inject it anywhere.

Example:

@RestController  
public class OrderController {  
  
    @Autowired  
    private OrderService orderService;  
  
}

Spring will automatically give the `OrderService` object.

---

## What does IoC Container do?

The **Spring IoC Container** is responsible for:

1️⃣ Creating objects (beans)  
2️⃣ Managing their lifecycle  
3️⃣ Injecting dependencies  
4️⃣ Destroying beans when application stops

The container stores objects called **Beans**.

---

# 2. What is a Bean?

A **Bean** is simply an object that is **managed by the Spring IoC Container**.

Example:

@Service  
public class PaymentService {  
}

Here **PaymentService becomes a Spring Bean**.

Spring creates it and stores it inside the container.

---

# 3. Bean Lifecycle

A **Bean lifecycle** means the **stages a bean goes through from creation to destruction**.

Lifecycle steps:

1 Bean Instantiation  
2 Dependency Injection  
3 Initialization  
4 Bean Ready to Use  
5 Destruction

---

### Step 1: Bean Creation

Spring creates the object.

Example:

@Service  
public class UserService {  
}

Spring creates the `UserService` object.

---

### Step 2: Dependency Injection

Spring injects required dependencies.

Example:

@Service  
public class OrderService {  
  
    @Autowired  
    private PaymentService paymentService;  
  
}

Spring injects `PaymentService`.

---

### Step 3: Initialization

Spring runs initialization methods.

Example:

@PostConstruct  
public void init(){  
    System.out.println("Bean initialized");  
}

---

### Step 4: Bean Ready

Now the bean is ready to use in the application.

---

### Step 5: Destruction

When application stops:

@PreDestroy  
public void destroy(){  
    System.out.println("Bean destroyed");  
}

---

# 4. Dependency Injection Types

There are **three types**:

1️⃣ Field Injection  
2️⃣ Constructor Injection  
3️⃣ Setter Injection

But in modern Spring Boot **Constructor Injection is recommended**.

---

# 5. Field Injection (Common but Not Recommended)

Example:

@RestController  
public class OrderController {  
  
    @Autowired  
    private OrderService orderService;  
  
}

Spring automatically injects the dependency.

### Problem with Field Injection

- Harder to test
- Hidden dependencies
- Not recommended for large applications

---

# 6. Constructor Injection (Best Practice)

Example:

@RestController  
public class OrderController {  
  
    private final OrderService orderService;  
  
    public OrderController(OrderService orderService){  
        this.orderService = orderService;  
    }  
  
}

Spring automatically injects the dependency through constructor.

Advantages:

✔ Better for testing  
✔ Clear dependencies  
✔ Immutable objects  
✔ Recommended by Spring

---

# 7. Constructor Injection with Annotation (Optional)

In newer Spring versions you don't need `@Autowired`.

@RestController  
public class OrderController {  
  
    private final OrderService orderService;  
  
    @Autowired  
    public OrderController(OrderService orderService){  
        this.orderService = orderService;  
    }  
  
}

But **Spring automatically detects it** even without `@Autowired`.

---

# 8. Visual Flow

Spring Application Start  
        ↓  
IoC Container Created  
        ↓  
Beans Created  
        ↓  
Dependencies Injected  
        ↓  
Beans Ready  
        ↓  
Application Running  
        ↓  
Application Stop  
        ↓  
Beans Destroyed

---

# 9. Real Example Architecture

UserController  
       ↓  
UserService  
       ↓  
UserRepository  
       ↓  
Database

Each of these is a **Spring Bean managed by the IoC Container**.

---

# 10. Interview-Level Summary

### IoC Container

> The Spring IoC Container is responsible for creating, configuring, and managing the lifecycle of Spring Beans.

### Bean

> A Bean is an object managed by the Spring IoC Container.

### Constructor Injection

> A type of dependency injection where dependencies are provided through the constructor. It is the recommended approach in Spring Boot.  