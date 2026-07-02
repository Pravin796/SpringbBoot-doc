# The Problem First

By default, Spring Data JPA assumes every `@Query` is a **SELECT query**.

So this works fine:

@Query("SELECT u FROM User u WHERE u.email = :email")  
User findUserByEmail(String email);

Because it is **reading data**.

But what if you write UPDATE or DELETE query? 🤔

@Query("UPDATE User u SET u.name = :name WHERE u.id = :id")  
void updateUserName(Long id, String name);

When you run this ❌ you get error:

QueryExecutionRequestException: Not supported for DML operations

Why?  
Because Spring thinks this is a SELECT query.

This is where **@Modifying** comes in.

---

# 🧠 What is `@Modifying` ?

`@Modifying` tells Spring:

👉 “This query will **modify the database**, not read it.”

Meaning:

- UPDATE
- DELETE
- INSERT (native)

---

# ✅ Correct Usage

@Modifying  
@Query("UPDATE User u SET u.name = :name WHERE u.id = :id")  
int updateUserName(Long id, String name);

Now Spring knows:

> This is a DML query (Data Manipulation Language)

---

# ⚠️ VERY IMPORTANT: Needs `@Transactional`

Because modifying the database requires a transaction.

So real usage is:

@Transactional  
@Modifying  
@Query("UPDATE User u SET u.name = :name WHERE u.id = :id")  
int updateUserName(Long id, String name);

---

# 💡 Why does it return `int`?

Return value = **number of rows affected**

Example:

1 row updated → returns 1  
0 rows updated → returns 0

---

# 🧾 Example — UPDATE

@Transactional  
@Modifying  
@Query("UPDATE User u SET u.active = false WHERE u.lastLogin < :date")  
int deactivateOldUsers(LocalDate date);

---

# 🧾 Example — DELETE

@Transactional  
@Modifying  
@Query("DELETE FROM User u WHERE u.active = false")  
int deleteInactiveUsers();

---

# 🔥 Real Interview Point

Without `@Modifying`:

- Spring calls `getResultList()` (for SELECT)

With `@Modifying`:

- Spring calls `executeUpdate()` (for UPDATE/DELETE)

That’s the whole difference.

---

# 🎯 Summary

|Annotation|Purpose|
|---|---|
|`@Query`|Custom query|
|`@Modifying`|Query changes DB|
|`@Transactional`|Required to commit changes|

👉 Use together when writing UPDATE/DELETE queries.