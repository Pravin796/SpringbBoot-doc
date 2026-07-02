Understanding **Spring Bean Scopes** is very important because it defines:

> **How many objects (instances) of a bean Spring will create and how long they live.**

---

# 1. What is Bean Scope?

A **Bean Scope** tells Spring:

👉 _"Should I create one object or multiple objects of this class?"_

---

# 2. Types of Bean Scopes

Spring provides **5 main scopes**:

|Scope|Description|
|---|---|
|`singleton`|One object per Spring container|
|`prototype`|New object every time|
|`request`|One object per HTTP request|
|`session`|One object per HTTP session|
|`application`|One object per application|

---

# 3. Singleton Scope (Default)

This is the **default scope in Spring**.

👉 Only **one instance** of the bean is created.

Example:

@Service  
public class UserService {  
}

Spring creates **only one object** and reuses it everywhere.

### Behavior:

Controller1 → same UserService object  
Controller2 → same UserService object

✔ Memory efficient  
✔ Best for stateless services

---

# 4. Prototype Scope

👉 A **new object is created every time** the bean is requested.

Example:

import org.springframework.context.annotation.Scope;  
import org.springframework.stereotype.Component;  
  
@Component  
@Scope("prototype")  
public class PaymentService {  
}

### Behavior:

Request 1 → new PaymentService()  
Request 2 → new PaymentService()

❗ Spring does NOT manage full lifecycle (destroy phase not handled)

---

# 5. Request Scope (Web Only)

👉 One bean per **HTTP request**

Example:

@Component  
@Scope("request")  
public class RequestBean {  
}

### Behavior:

User hits API → new object created  
Next API call → new object again

Used in:

- Request-specific data
- User input handling

---

# 6. Session Scope (Web Only)

👉 One bean per **user session**

Example:

@Component  
@Scope("session")  
public class SessionBean {  
}

### Behavior:

Same user → same object  
Different user → different object

Used in:

- Login session data
- User preferences

---

# 7. Application Scope

👉 One bean per **ServletContext (entire application)**

Example:

@Component  
@Scope("application")  
public class AppBean {  
}

Similar to singleton but tied to **web context**.

---

# 8. How to Define Scope

Using annotation:

@Scope("prototype")  
@Component  
public class MyBean {  
}

---

# 9. Important Interview Concepts

## Singleton vs Prototype

|Feature|Singleton|Prototype|
|---|---|---|
|Instances|One|Multiple|
|Default|Yes|No|
|Lifecycle managed|Fully|Partially|
|Use case|Services|Temporary objects|

---

# 10. Real-World Usage

### Use Singleton (Most Common)

- Services (`@Service`)
- Repositories (`@Repository`)
- Controllers (`@RestController`)

### Use Prototype

- Temporary objects
- State-specific logic

### Use Request/Session

- Web applications with user-specific data

---

# 11. Important Gotcha (VERY IMPORTANT)

If you inject a **prototype bean into a singleton**, Spring will create it **only once**.

Example:

@Service  
public class OrderService {  
  
    @Autowired  
    private PaymentService paymentService; // prototype  
  
}

👉 This behaves like **singleton**, NOT prototype.

To fix this, you need advanced techniques like:

- `ObjectFactory`
- `Provider`

---

# 12. Simple Visualization

Singleton  → 1 object → shared everywhere  
Prototype  → new object every time  
Request    → 1 per API call  
Session    → 1 per user session

---

# 13. Interview Definition

**What is Bean Scope in Spring?**

> Bean scope defines the lifecycle and visibility of a bean, i.e., how many instances of a bean are created and how they are shared within the Spring container.