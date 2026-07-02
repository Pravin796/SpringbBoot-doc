# Why we need table-level annotations?

By default JPA creates table automatically:

@Entity  
public class User {  
    @Id  
    private Long id;  
}

Hibernate creates table named **user**.

But real projects need:

- custom table names
- unique email
- fast searching with indexes

That’s where `@Table` comes in.

---

# 2️⃣ `@Table` annotation

Used to configure **database table properties**.

@Entity  
@Table(name = "users")  
public class User {  
    @Id  
    private Long id;  
}

Now DB table becomes:

users

---

## Full power of @Table

@Table(  
    name = "users",  
    schema = "public",  
    uniqueConstraints = {},  
    indexes = {}  
)

We mainly use:

- name
- uniqueConstraints
- indexes

---

# 3️⃣ Unique Constraints 🔐

## Why?

To prevent duplicate data in DB.

Real example:

- Email must be unique
- Username must be unique

---

## ❌ Without constraint

Two users can register with same email 😱

---

## ✔ Method 1 — Column level unique

@Column(unique = true)  
private String email;

Hibernate generates:

email VARCHAR UNIQUE

Good for **single column unique**.

---

## ✔ Method 2 — Table level unique (IMPORTANT)

Used for **composite unique constraints**.

Example:  
User cannot have same phone in same country.

@Entity  
@Table(  
    name = "users",  
    uniqueConstraints = {  
        @UniqueConstraint(  
            name = "uk_email",  
            columnNames = "email"  
        )  
    }  
)  
public class User {  
    @Id  
    private Long id;  
  
    private String email;  
}

Hibernate generates:

CONSTRAINT uk_email UNIQUE (email)

---

## Composite Unique Constraint ⭐

Example:  
A user cannot have duplicate address in same city.

@Table(  
    uniqueConstraints = {  
        @UniqueConstraint(  
            name = "uk_user_city",  
            columnNames = {"user_id", "city"}  
        )  
    }  
)

This is **very common in real projects**.

---

# 4️⃣ Indexes ⚡ (VERY IMPORTANT FOR PERFORMANCE)

## Why indexes?

Indexes make queries FAST.

Without index:

SELECT * FROM users WHERE email = ?

DB scans whole table 😵

With index:  
DB jumps directly to row 🚀

---

## Add index using @Table

@Entity  
@Table(  
    name = "users",  
    indexes = {  
        @Index(  
            name = "idx_user_email",  
            columnList = "email"  
        )  
    }  
)  
public class User {  
    @Id  
    private Long id;  
  
    private String email;  
}

Hibernate generates:

CREATE INDEX idx_user_email ON users(email);

---

## Multiple indexes example

@Table(  
    indexes = {  
        @Index(name = "idx_email", columnList = "email"),  
        @Index(name = "idx_name", columnList = "name")  
    }  
)

---

## Composite index ⭐

Used when searching with multiple columns.

@Table(  
    indexes = {  
        @Index(  
            name = "idx_city_state",  
            columnList = "city, state"  
        )  
    }  
)

Useful query:

SELECT * FROM address WHERE city=? AND state=?;

---

# ⚠️ Index vs Unique — Interview difference

|Feature|Unique Constraint|Index|
|---|---|---|
|Prevent duplicate data|✅ Yes|❌ No|
|Improve query speed|✅ Yes|✅ Yes|
|Ensures data integrity|✅ Yes|❌ No|

👉 Unique constraint automatically creates an index internally.

---

# 🧠 Real Production Example

@Entity  
@Table(  
    name = "users",  
    uniqueConstraints = {  
        @UniqueConstraint(name = "uk_user_email", columnNames = "email")  
    },  
    indexes = {  
        @Index(name = "idx_user_name", columnList = "name"),  
        @Index(name = "idx_user_created", columnList = "created_at")  
    }  
)  
public class User {  
  
    @Id @GeneratedValue  
    private Long id;  
  
    @Column(nullable = false)  
    private String name;  
  
    @Column(nullable = false)  
    private String email;  
  
    private LocalDateTime createdAt;  
}

This is how real entities look in companies 💯

---

# 🎯 Interview Questions

### ❓ Difference between unique and index?

Unique → data integrity  
Index → performance

### ❓ Does unique create index?

✔ Yes (automatically)

### ❓ When use composite index?

When queries filter using multiple columns.