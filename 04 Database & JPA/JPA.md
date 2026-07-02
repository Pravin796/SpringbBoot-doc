In Spring, **Bean Hooks** are methods that let you **run custom logic at specific points in a bean’s lifecycle**.

👉 Think of them as **“callbacks”**:

> “When my bean is created → run this code”  
> “Before my bean is destroyed → run this code”

---

# 1. Why Bean Hooks Are Used

We use bean hooks to:

✔ Initialize resources (DB connection, cache, etc.)  
✔ Validate data after bean creation  
✔ Clean up resources before shutdown

---

# 2. Types of Bean Hooks in Spring

There are **3 main ways** to define bean hooks:

1️⃣ Annotations (`@PostConstruct`, `@PreDestroy`) ✅ (Most used)  
2️⃣ Interfaces (`InitializingBean`, `DisposableBean`)  
3️⃣ Custom methods (`initMethod`, `destroyMethod`)

---

# 3. Using Annotations (Recommended)

## 🔹 `@PostConstruct`

Runs **after bean is created and dependencies are injected**.

Example:

import jakarta.annotation.PostConstruct;  
import org.springframework.stereotype.Component;  
  
@Component  
public class UserService {  
  
    @PostConstruct  
    public void init(){  
        System.out.println("Bean initialized");  
    }  
  
}

👉 Runs automatically after Spring creates the bean.

---

## 🔹 `@PreDestroy`

Runs **just before the bean is destroyed**.

import jakarta.annotation.PreDestroy;  
  
@PreDestroy  
public void destroy(){  
    System.out.println("Bean destroyed");  
}

---

# 4. Using Interfaces

### `InitializingBean`

import org.springframework.beans.factory.InitializingBean;  
  
@Component  
public class OrderService implements InitializingBean {  
  
    @Override  
    public void afterPropertiesSet(){  
        System.out.println("After properties set");  
    }  
  
}

---

### `DisposableBean`

import org.springframework.beans.factory.DisposableBean;  
  
@Component  
public class OrderService implements DisposableBean {  
  
    @Override  
    public void destroy(){  
        System.out.println("Bean destroyed");  
    }  
  
}

---

# 5. Using `@Bean` with initMethod

@Bean(initMethod = "init", destroyMethod = "cleanup")  
public PaymentService paymentService(){  
    return new PaymentService();  
}

Class:

public class PaymentService {  
  
    public void init(){  
        System.out.println("Init method called");  
    }  
  
    public void cleanup(){  
        System.out.println("Cleanup method called");  
    }  
  
}

---

# 6. Complete Bean Lifecycle with Hooks

1. Bean Created  
2. Dependencies Injected  
3. @PostConstruct / afterPropertiesSet()  
4. Bean Ready to Use  
5. Application Running  
6. @PreDestroy / destroy()

---

# 7. Real Example

@Component  
public class DatabaseService {  
  
    @PostConstruct  
    public void connect(){  
        System.out.println("Connecting to DB...");  
    }  
  
    @PreDestroy  
    public void disconnect(){  
        System.out.println("Closing DB connection...");  
    }  
  
}

---

# 8. Important Notes

✔ `@PostConstruct` runs **only once**  
✔ `@PreDestroy` runs **when app shuts down**  
❗ Not called for **prototype beans** (important interview point)

---

# 9. Best Practice

👉 Always prefer:

@PostConstruct  
@PreDestroy

Avoid interfaces unless required.

---

# 10. Interview Definition

**What are Bean Hooks in Spring?**

> Bean hooks are lifecycle callback methods that allow developers to execute custom logic during bean initialization and destruction phases.

---

# 🔥 Quick Revision

| Hook             | When it runs            |
| ---------------- | ----------------------- |
| `@PostConstruct` | After bean creation     |
| `@PreDestroy`    | Before bean destruction |
# 13. Real-World Flow

POST /users  
     ↓  
Controller  
     ↓  
Service  
     ↓  
Repository.save()  
     ↓  
Hibernate  
     ↓  
INSERT INTO user

---

# 🔥 What You Should Do Now (Practice)

Build a small project:

👉 **User Management System**