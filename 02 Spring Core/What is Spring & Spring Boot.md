# 1. What is Spring Framework?

**Spring** is a **Java framework** used to build **enterprise applications**.

It provides tools for:

- Dependency Injection
- Web applications
- Database access
- Security
- Microservices

Example:  
Large applications like **banking systems, e-commerce platforms, enterprise apps** often use Spring.

---

# 2. What is Spring Boot?

**Spring Boot = Spring Framework + Auto Configuration**

It makes Spring development **faster and easier**.

Before Spring Boot, you had to write **a lot of configuration (XML)**.

Spring Boot automatically configures everything.

Example:

Old Spring required **XML configuration**

<bean id="orderService" class="com.example.OrderService"/>

Spring Boot uses **annotations**

@Service  
public class OrderService {  
}

Much cleaner 👍

---

# 3. Why Developers Use Spring Boot

Advantages:

✔ No XML configuration  
✔ Embedded server (Tomcat)  
✔ Faster development  
✔ Microservices friendly  
✔ Production ready

Example:

Without Spring Boot:

You must install **Tomcat server manually**

With Spring Boot:

Run main method → server starts automatically

---

# 4. Spring Boot Architecture

Basic flow:

Client (Browser / Postman)  
        ↓  
Controller  
        ↓  
Service  
        ↓  
Repository  
        ↓  
Database

Example:

User requests → /users  
  
Controller → handles request  
Service → business logic  
Repository → database