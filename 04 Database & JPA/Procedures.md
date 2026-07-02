# What is a Stored Procedure?

A **Stored Procedure** is a **pre-written SQL program stored inside the database** that you can call whenever needed.

Think of it like:

👉 Instead of sending 10 SQL queries from Java every time  
👉 You store the logic **once in DB** and call it with parameters.

### Why use Stored Procedures?

• Faster (pre-compiled in DB)  
• Reusable  
• Secure (no raw SQL exposure)  
• Good for complex business logic

---

## Example Problem

We have a `users` table:

users(  
   id BIGINT,  
   name VARCHAR,  
   email VARCHAR,  
   age INT  
)

We want a stored procedure to:

👉 Find users whose age is greater than a given value.

---

# 2️⃣ Creating Stored Procedure in Database

### MySQL Stored Procedure

Delimiter (use and and here)
CREATE PROCEDURE get_users_by_age(IN min_age INT)  
BEGIN  
SELECT * FROM users WHERE age > min_age;  
END (use and and here)
Delimiter;

Here:

|Part|Meaning|
|---|---|
|`CREATE PROCEDURE`|creating procedure|
|`IN min_age INT`|input parameter|
|`SELECT`|SQL logic|

Test in DB:

CALL get_users_by_age(25);

---

# 3️⃣ Calling Stored Procedure in Spring Boot

Spring provides **@Procedure** to call stored procedures directly from Repository.

There are **3 ways** to use it 👇

---

# 4️⃣ Way 1 — Simple @Procedure (Easiest)

### Step 1 — Entity

@Entity  
public class User {  
    @Id  
    private Long id;  
    private String name;  
    private String email;  
    private int age;  
}

---

### Step 2 — Repository using @Procedure

@Repository  
public interface UserRepository extends JpaRepository<User, Long> {  
  
    @Procedure(name = "get_users_by_age")  
    List<User> getUsersByAge(@Param("min_age") Integer age);  
}

### Important

Method name can be anything.  
Procedure name must match DB procedure.

---

### Step 3 — Service Call

@Autowired  
UserRepository repo;  
  
public void test() {  
    List<User> users = repo.getUsersByAge(25);  
}

🔥 Done! Stored procedure called from Java.

---

# 5️⃣ Way 2 — Using procedureName attribute (Recommended)

More explicit and safe.

@Procedure(procedureName = "get_users_by_age")  
List<User> fetchUsers(Integer min_age);

Spring automatically maps parameter by position.

---

# 6️⃣ Way 3 — Using @NamedStoredProcedureQuery (Enterprise Way ⭐)

This is **most professional & interview favourite**.

We define procedure mapping in **Entity class**.

---

### Step 1 — Add Named Stored Procedure to Entity

@Entity  
@NamedStoredProcedureQuery(  
    name = "User.getUsersByAge",  
    procedureName = "get_users_by_age",  
    resultClasses = User.class,  
    parameters = {  
        @StoredProcedureParameter(  
            mode = ParameterMode.IN,  
            name = "min_age",  
            type = Integer.class  
        )  
    }  
)  
public class User {  
    @Id  
    private Long id;  
    private String name;  
    private String email;  
    private int age;  
}

👉 We created mapping between Java & DB procedure.

---

### Step 2 — Call from Repository

@Procedure(name = "User.getUsersByAge")  
List<User> getUsersByAge(Integer min_age);

Notice:

|Name|Meaning|
|---|---|
|`"User.getUsersByAge"`|Java mapping name|
|`"get_users_by_age"`|DB procedure name|

---

# 7️⃣ Stored Procedure with OUT Parameter

Now let’s see powerful example 💥

👉 Procedure that returns **count of users**

### SQL

CREATE PROCEDURE get_user_count(OUT total INT)  
BEGIN  
   SELECT COUNT(*) INTO total FROM users;  
END

---

### Repository

@Procedure(procedureName = "get_user_count")  
Integer getUserCount();

Spring automatically reads OUT parameter 🎉

---

# 8️⃣ Procedure with IN + OUT Parameter

### SQL

CREATE PROCEDURE count_users_above_age(  
    IN min_age INT,  
    OUT total INT  
)  
BEGIN  
   SELECT COUNT(*) INTO total  
   FROM users  
   WHERE age > min_age;  
END

---

### Repository

@Procedure(procedureName = "count_users_above_age")  
Integer countUsersAboveAge(@Param("min_age") Integer age);

---

# 9️⃣ When Should You Use Stored Procedures?

Use when:

✅ Heavy DB logic  
✅ Reporting queries  
✅ Batch operations  
✅ Performance critical queries  
✅ Legacy DB already has procedures

Avoid when:

❌ Simple CRUD  
❌ You want DB portability

---

# 🔟 Quick Interview Summary 🧠

**Stored Procedure**

- SQL program stored in DB
- Improves performance
- Called using `@Procedure` in Spring Data JPA

**3 Ways to Use**

1. `@Procedure(name="proc")`
2. `@Procedure(procedureName="proc")`
3. `@NamedStoredProcedureQuery` ⭐ (best practice)