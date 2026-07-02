# What is Fetching Strategy in JPA?

When you load an entity from DB, the question is:

👉 Should related entities be loaded **immediately** or **only when needed?**

This is called **Fetch Strategy**.

There are only 2 types:

|Type|Meaning|
|---|---|
|**EAGER**|Load related data **immediately** with parent|
|**LAZY**|Load related data **only when accessed**|

---

# 2️⃣ Simple Real Life Example

Imagine you load a **User**.

User has:

- Address (one-to-one)
- Orders (one-to-many)

Now ask yourself:

When I fetch user, do I ALWAYS need:

- Address? 👉 YES mostly
- Orders? 👉 NO, user may have 10,000 orders 😱

That’s why fetch strategies exist.

---

# 3️⃣ EAGER Fetching (Load Immediately)

## Definition

Hibernate loads the related entity **in the same query (JOIN)**.

@OneToOne(fetch = FetchType.EAGER)  
private Address address;

## What happens internally

When you run:

userRepo.findById(1);

Hibernate runs SQL like:

SELECT * FROM user u  
JOIN address a ON u.address_id = a.id  
WHERE u.id = 1;

👉 Address is loaded **instantly**

### Memory picture

User object created  
 └── Address object ALSO created immediately

---

## When to use EAGER?

Use when related data is:

- Small
- Always needed
- One record only

### Perfect for:

|Relationship|Why|
|---|---|
|`@OneToOne`|Only one object|
|`@ManyToOne`|Only one object|

Example:

- User → Address
- Order → Customer
- Employee → Department

Fetching 1 extra row is cheap 👍

---

# 4️⃣ LAZY Fetching (Load Only When Needed)

## Definition

Hibernate loads related data **only when you access it**.

@OneToMany(fetch = FetchType.LAZY)  
private List<Order> orders;

When you fetch user:

User user = userRepo.findById(1);

Hibernate runs ONLY:

SELECT * FROM user WHERE id = 1;

Orders are **NOT loaded** yet 🚫

---

### When do orders load?

Only when you call:

user.getOrders();

Then Hibernate fires another query:

SELECT * FROM orders WHERE user_id = 1;

This is called **Lazy Initialization**.

---

### Memory picture

User created  
 └── orders = Proxy object (fake placeholder)  
         ↓ when accessed  
     real orders loaded from DB

Hibernate creates a **proxy object** instead of real list.

---

# 5️⃣ Why Lazy is Important (Performance)

Imagine:

User has **50,000 orders**

If EAGER is used 😱

List<User> users = userRepo.findAll();

Hibernate will load:

100 users  
+ 50k orders each  
= 💥 5 MILLION rows loaded

Your app will die 💀

That’s why collections must be **LAZY**.

---

# 6️⃣ Default Fetch Types (VERY IMPORTANT 🔥)

JPA already decided best defaults:

|Relationship|Default Fetch|
|---|---|
|`@OneToOne`|**EAGER**|
|`@ManyToOne`|**EAGER**|
|`@OneToMany`|**LAZY**|
|`@ManyToMany`|**LAZY**|

This is NOT random. It’s based on performance.

### Rule to remember 🎯

👉 Single object → EAGER  
👉 Collection → LAZY

Because collections can be HUGE.

---

# 7️⃣ Why `@OneToMany` must be LAZY (Big concept)

Example:

class User {  
   @OneToMany  
   List<Order> orders;  
}

One user can have:

- 10 orders
- 100 orders
- 10,000 orders

Hibernate **cannot guess size**.

So it plays safe → LAZY.

---

# 8️⃣ N+1 Query Problem (Important Interview Question)

When using LAZY incorrectly:

List<User> users = userRepo.findAll();  
  
for(User u : users){  
   u.getOrders().size(); // triggers query each time  
}

Queries fired:

1 query -> fetch users  
+ 100 queries -> fetch orders for each user  
= 101 queries 😱

This is called **N+1 Problem**.

Fix using:

@Query("SELECT u FROM User u JOIN FETCH u.orders")

But we’ll study later.

---

# 9️⃣ Final Golden Rule 🏆

|Use Case|Fetch Type|
|---|---|
|Single related entity|EAGER|
|Collection / List / Set|LAZY|

**Golden sentence to remember:**

> Always prefer LAZY. Use EAGER only when absolutely sure.

Many senior devs even force everything to LAZY and manually fetch when needed.

---

# 🔟 Short Interview Answer

**Q: Why OneToOne is EAGER and OneToMany is LAZY?**

**Answer:**

Because:

- OneToOne fetches only **one row**, cheap.
- OneToMany can fetch **thousands of rows**, expensive.
- Lazy loading improves performance and memory usage.