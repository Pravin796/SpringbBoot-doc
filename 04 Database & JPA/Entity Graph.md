In **Spring Data JPA**, an **Entity Graph** is a way to control **which related entities should be fetched in a single query**, instead of relying only on `EAGER` or `LAZY`.

Think of it like telling Hibernate:  
👉 _“For this query only, fetch these relations also.”_

This helps avoid the famous **N+1 query problem** and improves performance.

---

# Why EntityGraph is needed

Imagine these entities:

User  --->  List<Order> orders  
Order --->  Product product

Default behaviour:

- `@OneToMany` → LAZY
- `@ManyToOne` → EAGER

Now suppose you fetch users:

List<User> users = userRepo.findAll();

Later you access orders:

users.get(0).getOrders();

Hibernate will run **extra queries for each user** 😭  
This is called **N+1 problem**.

---

# Solution Options in JPA

There are 3 ways to fetch relations:

|Method|Problem|
|---|---|
|EAGER|Always loads → slow|
|JPQL JOIN FETCH|Hard to reuse|
|⭐ EntityGraph|Flexible + clean|

EntityGraph = **Best practice**

---

# What is EntityGraph?

It defines **fetch plan per query**.

Instead of making relation eager permanently, you fetch it **only when required**.

---

# Step 1 — Entities Example

@Entity  
public class User {  
    @Id  
    Long id;  
    String name;  
  
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)  
    List<Order> orders;  
}  
  
@Entity  
public class Order {  
    @Id  
    Long id;  
    String item;  
  
    @ManyToOne(fetch = FetchType.LAZY)  
    User user;  
}

Everything LAZY → Good practice 👍

---

# Step 2 — Create Named EntityGraph

We define what to fetch.

@Entity  
@NamedEntityGraph(  
    name = "User.orders",  
    attributeNodes = @NamedAttributeNode("orders")  
)  
public class User {  
    @Id  
    Long id;  
    String name;  
  
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)  
    List<Order> orders;  
}

Here we created a graph called:

User.orders

Meaning → fetch **orders** along with user.

---

# Step 3 — Use EntityGraph in Repository

public interface UserRepository extends JpaRepository<User, Long> {  
  
    @EntityGraph(value = "User.orders")  
    List<User> findAll();  
}

Now when calling:

userRepo.findAll();

Hibernate generates **ONE JOIN query** 👇

select u.*, o.*  
from users u  
left join orders o on u.id = o.user_id;

🔥 N+1 problem solved!

---

# Dynamic EntityGraph (Without Named Graph)

You can define directly in repository.

@EntityGraph(attributePaths = {"orders"})  
List<User> findByName(String name);

This is called **Ad-hoc EntityGraph**.

---

# Fetch Multiple Levels (Nested Graph)

User → Orders → Product

@EntityGraph(attributePaths = {"orders", "orders.product"})  
List<User> findAll();

Boom 💥  
3 tables loaded in **single query**.

---

# EntityGraph vs JOIN FETCH

|Feature|JOIN FETCH|EntityGraph|
|---|---|---|
|Reusable|❌|✅|
|Clean|❌ JPQL clutter|✅|
|Dynamic|❌|✅|
|Recommended|❌|⭐ YES|

---

# Real Interview Definition

👉 **EntityGraph is a JPA feature that allows defining a fetch plan dynamically to load related entities in a single query and avoid the N+1 problem.**

---

# When to use EntityGraph

Use it when:

- DTO projection not needed
- You need full entities
- You want flexible fetching
- You want to avoid N+1 queries

---

# Quick Summary

• Keep relations LAZY always  
• Use EntityGraph when relation needed  
• Improves performance  
• Avoids N+1 queries  
• Cleaner than JOIN FETCH