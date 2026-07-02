# 1️⃣ `@Getter` and `@Setter`

These generate **only getters and setters**.

@Getter  
@Setter  
public class User {  
    private Long id;  
    private String name;  
}

Lombok generates:

public Long getId() { return id; }  
public void setId(Long id) { this.id = id; }  
  
public String getName() { return name; }  
public void setName(String name) { this.name = name; }

### When to use

Use when you want **fine control** over what Lombok generates.

Example:

@Getter  
@Setter  
private String name;  
  
@Getter   // read-only field  
private LocalDate createdDate;

So this gives **control**.

---

# 2️⃣ `@AllArgsConstructor`

This generates a constructor with **all fields**.

@AllArgsConstructor  
public class User {  
    private Long id;  
    private String name;  
}

Generated:

public User(Long id, String name) {  
    this.id = id;  
    this.name = name;  
}

### Why we need this?

Useful for:

- Constructor Injection in Spring
- Creating objects quickly

Example:

User u = new User(1L, "Pravin");

---

# 3️⃣ `@Data` (The BIG one)

`@Data` is a **combo annotation** 😎

It automatically adds:

@Getter  
@Setter  
@ToString  
@EqualsAndHashCode  
@RequiredArgsConstructor

So this:

@Data  
public class User {  
    private Long id;  
    private String name;  
}

Generates ALL of this:

- getters
- setters
- toString()
- equals()
- hashCode()
- required constructor

This is why people call it **Lombok magic** ✨

---

# ⚠️ VERY IMPORTANT — Spring Boot Reality

Many beginners overuse `@Data` everywhere ❌  
But in real Spring Boot projects, we **DON'T use @Data on Entities**.

Why? Because `@Data` generates:

equals()  
hashCode()  
toString()

These can cause BIG problems in JPA:

- Infinite loop in bidirectional relationships 🔁
- Lazy loading issues
- Performance problems

---

# 🧠 Real Project Best Practice

## ✔ For DTO / Request / Response classes

Use `@Data`

@Data  
public class UserRequest {  
    private String name;  
    private String email;  
}

Because DTOs are simple data carriers.

---

## ✔ For JPA Entities (VERY IMPORTANT)

Use this instead:

@Getter  
@Setter  
@NoArgsConstructor  
@AllArgsConstructor  
@Builder  
@Entity  
public class User {  
    private Long id;  
    private String name;  
}

Notice → ❌ NO `@Data`

This is industry standard.

---

# 🔥 Why not @Data in Entity? (Interview question)

Because it generates:

- equals()
- hashCode()
- toString()

And JPA entities should **control these manually**.

---

# 🧾 Quick Comparison Table

|Annotation|Generates|
|---|---|
|`@Getter`|getters only|
|`@Setter`|setters only|
|`@AllArgsConstructor`|constructor with all fields|
|`@Data`|getter + setter + toString + equals + hashCode + constructor|

---

# 🎯 Interview Summary

**When to use what?**

Use `@Data` → DTO classes  
Use `@Getter/@Setter` → Entities  
Use `@AllArgsConstructor` → constructor injection / object creation