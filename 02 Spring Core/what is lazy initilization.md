**Lazy Initialization** in Spring means:

> A bean is **not created when the application starts**, it is **created only when it is used for the first time**.

Normally, Spring uses **Eager Initialization**, meaning all beans are created at startup.

---

# 1. Default Behavior (Eager Initialization)

By default, Spring creates all beans when the application starts.

Example:

@Service  
public class OrderService {  
  
    public OrderService(){  
        System.out.println("OrderService created");  
    }  
  
}

When the application starts, Spring immediately creates this bean.

Output during startup:

OrderService created

Even if the bean is **never used**, it is still created.

---

# 2. Lazy Initialization

With **Lazy Initialization**, Spring delays bean creation until it is actually needed.

Example:

@Service  
@Lazy  
public class OrderService {  
  
    public OrderService(){  
        System.out.println("OrderService created");  
    }  
  
}

Now Spring will **not create the bean at startup**.

The bean will be created **only when it is used**.

---

# 3. Lazy Injection Example

@RestController  
public class OrderController {  
  
    private final OrderService orderService;  
  
    public OrderController(@Lazy OrderService orderService){  
        this.orderService = orderService;  
    }  
  
}

Now `OrderService` will be created **only when the controller calls it**.

---

# 4. Where `@Lazy` Can Be Used

You can use it on:

### Class

@Lazy  
@Service  
public class PaymentService {  
}

### Injection Point

@Autowired  
@Lazy  
private PaymentService paymentService;

### Configuration Bean

@Bean  
@Lazy  
public PaymentService paymentService(){  
    return new PaymentService();  
}

---

# 5. Why Use Lazy Initialization?

Lazy initialization is useful when:

✔ Bean creation is **expensive**  
✔ Bean is **rarely used**  
✔ Application has **many beans**  
✔ Improve **startup performance**

Example:

- Large cache systems
- Heavy API clients
- Complex services

---

# 6. When NOT to Use Lazy Initialization

Avoid lazy initialization when:

❌ Bean is used frequently  
❌ You want **fail-fast startup** (detect errors early)

---

# 7. Global Lazy Initialization (Spring Boot)

You can enable lazy initialization for the entire application.

In **application.properties**:

spring.main.lazy-initialization=true

Now **all beans will be lazy** unless specified otherwise.

---

# 8. Example Flow

### Without Lazy

Application Start  
      ↓  
Spring Container  
      ↓  
All Beans Created  
      ↓  
Application Ready

### With Lazy

Application Start  
      ↓  
Spring Container  
      ↓  
Beans NOT created  
      ↓  
Bean created when used

---

# 9. Interview Definition

**What is Lazy Initialization in Spring?**

> Lazy Initialization is a mechanism in Spring where a bean is created only when it is requested for the first time instead of at application startup.