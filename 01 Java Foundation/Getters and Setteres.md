**Lombok** is a Java library that **removes boilerplate code** like getters, setters, constructors, `toString()`, `equals()`, etc.

👉 Without Lombok → you write lots of repetitive code  
👉 With Lombok → you write **just annotations**

This makes Spring Boot development **much faster and cleaner**.

---

# 1. Problem Without Lombok 😓

Example entity without Lombok:

public class User {  
  
    private Long id;  
    private String name;  
    private String email;  
  
    public User() {}  
  
    public User(Long id, String name, String email){  
        this.id = id;  
        this.name = name;  
        this.email = email;  
    }  
  
    public Long getId(){ return id; }  
    public void setId(Long id){ this.id = id; }  
  
    public String getName(){ return name; }  
    public void setName(String name){ this.name = name; }  
  
    public String getEmail(){ return email; }  
    public void setEmail(String email){ this.email = email; }  
  
    public String toString(){  
        return "User{id="+id+", name="+name+"}";  
    }  
}

👉 Too much repetitive code 😵

---

# 2. Same Code Using Lombok 😍

import lombok.Data;  
  
@Data  
public class User {  
    private Long id;  
    private String name;  
    private String email;  
}

DONE ✅  
Lombok automatically generates everything at compile time.

---

# 3. Add Lombok Dependency

In **pom.xml**

<dependency>  
    <groupId>org.projectlombok</groupId>  
    <artifactId>lombok</artifactId>  
    <optional>true</optional>  
</dependency>

After adding:

- Install Lombok plugin in IntelliJ
- Enable annotation processing

---

# 4. Most Important Lombok Annotations

These are used in **every Spring Boot project**.

---

## 1️⃣ `@Getter` and `@Setter`

@Getter  
@Setter  
public class User {  
    private String name;  
}

Generates getters/setters automatically.

---

## 2️⃣ `@ToString`

@ToString  
public class User { }

Generates `toString()`.

---

## 3️⃣ `@EqualsAndHashCode`

Generates `equals()` and `hashCode()`.

---

## 4️⃣ `@NoArgsConstructor`

@NoArgsConstructor  
public class User { }

Creates empty constructor.

---

## 5️⃣ `@AllArgsConstructor`

@AllArgsConstructor  
public class User { }

Creates constructor with all fields.

---

## 6️⃣ ⭐ `@Data` (Most Used)

@Data  
public class User { }

This single annotation includes:

- Getter
- Setter
- toString
- equals
- hashCode
- Required constructor

👉 This is used in **90% of entity classes**.

---

## 7️⃣ ⭐ `@Builder` (Very Popular)

Used to create objects cleanly.

Without Builder:

User user = new User(1,"Pravin","p@gmail.com");

With Builder:

@Builder  
@Data  
public class User {  
    private Long id;  
    private String name;  
    private String email;  
}

Usage:

User user = User.builder()  
        .id(1L)  
        .name("Pravin")  
        .email("p@gmail.com")  
        .build();

Very readable 👍

---

## 8️⃣ ⭐ `@RequiredArgsConstructor` (Spring Favorite)

Used for **constructor injection**.

@Service  
@RequiredArgsConstructor  
public class UserService {  
  
    private final UserRepository userRepository;  
  
}

Spring automatically injects dependency — no constructor needed.

---

# 5. Lombok in Spring Boot (Real Usage)

Typical Entity:

@Entity  
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
@Builder  
public class User {  
  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private Long id;  
  
    private String name;  
    private String email;  
}

This is how companies write entities.

---

# 6. Advantages of Lombok

✔ Less code  
✔ Cleaner classes  
✔ Faster development  
✔ Reduces human errors

---

# 7. Interview Definition

**What is Lombok?**

> Lombok is a Java library that reduces boilerplate code by automatically generating getters, setters, constructors, and other methods using annotations at compile time.