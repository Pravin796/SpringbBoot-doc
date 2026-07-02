  In **Spring Boot**, **Component Annotations** are used to tell Spring:

👉 _"This class is a Spring-managed object (bean), please create and manage it automatically."_

Spring will **detect these classes and create objects for them inside the IOC Container**.

---

# 1. What is `@Component` Annotation?

`@Component` is the **basic stereotype annotation** in Spring.

It tells Spring:

> "Create an object of this class and manage it in the Spring container."

Example:

import org.springframework.stereotype.Component;  
  
@Component  
public class PaymentService {  
  
    public void pay(){  
        System.out.println("Payment successful");  
    }  
  
}

Now Spring will **automatically create the object of `PaymentService`**.

---

# 2. How Spring Finds Components

Spring uses **Component Scanning**.

In your main class:

@SpringBootApplication

This annotation automatically enables **Component Scan**.

Meaning:

Spring will scan all classes in the package and sub-packages for:

- `@Component`
- `@Service`
- `@Repository`
- `@Controller`

---

# 3. Types of Component Annotations

Spring provides **specialized component annotations**.

|Annotation|Purpose|
|---|---|
|`@Component`|Generic component|
|`@Service`|Business logic layer|
|`@Repository`|Database layer|
|`@Controller`|MVC controller|
|`@RestController`|REST API controller|

All of them are **internally derived from `@Component`**.

---

# 4. Example of Each Component

### 1️⃣ `@Component`

General purpose bean.

@Component  
public class EmailService {  
  
    public void sendMail(){  
        System.out.println("Mail Sent");  
    }  
  
}

---

### 2️⃣ `@Service`

Used in **business logic layer**.

import org.springframework.stereotype.Service;  
  
@Service  
public class OrderService {  
  
    public void placeOrder(){  
        System.out.println("Order placed");  
    }  
  
}

---

### 3️⃣ `@Repository`

Used for **database operations**.

import org.springframework.stereotype.Repository;  
  
@Repository  
public class UserRepository {  
  
}

Spring also adds **database exception handling** here.

---

### 4️⃣ `@RestController`

Used for **REST APIs**.

@RestController  
public class HelloController {  
  
    @GetMapping("/hello")  
    public String hello(){  
        return "Hello Pravin";  
    }  
  
}

---

# 5. How Dependency Injection Works

Example:

### Service

@Service  
public class OrderService {  
  
    public String order(){  
        return "Order Placed";  
    }  
  
}

### Controller

@RestController  
public class OrderController {  
  
    @Autowired  
    private OrderService orderService;  
  
    @GetMapping("/order")  
    public String placeOrder(){  
        return orderService.order();  
    }  
  
}

Spring will **automatically inject `OrderService`**.

---

# 6. Real Application Architecture

Typical Spring Boot structure:

controller  
   |  
service  
   |  
repository  
   |  
database

Example:

UserController  
      ↓  
UserService  
      ↓  
UserRepository  
      ↓  
MySQL Database

---

# 7. Important Interview Question

### Difference between `@Component` and `@Service`

Answer:

- `@Component` → Generic Spring bean
- `@Service` → Used for business logic layer

But internally **both work the same**.

---

# 8. Key Point

All these are **Component Annotations**:

@Component  
@Service  
@Repository  
@Controller  
@RestController

They tell Spring:

> "Create and manage this class as a bean."