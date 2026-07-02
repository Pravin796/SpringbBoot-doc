# First understand the idea

Entities = Tables  
Relationships = Foreign Keys

Real world example:

- User has Address
- User places Orders
- Student enrolls Courses

These become JPA relationships.

There are **4 main types**

|Relationship|DB meaning|
|---|---|
|OneToOne|One row ↔ one row|
|OneToMany|One row ↔ many rows|
|ManyToOne|Many rows ↔ one row|
|ManyToMany|Many ↔ many|

We’ll go one by one in depth 👇

---

# 1️⃣ OneToOne Relationship

## Example

A user has one profile.  
A profile belongs to one user.

### Database tables

USER  
id | name  
  
PROFILE  
id | bio | user_id (FK)

---

## Entity code

### User entity (Parent)

@Entity  
@Getter @Setter  
public class User {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String name;  
  
    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)  
    private Profile profile;  
}

### Profile entity (Owner)

@Entity  
@Getter @Setter  
public class Profile {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String bio;  
  
    @OneToOne  
    @JoinColumn(name = "user_id")  
    private User user;  
}

---

## ⚠️ VERY IMPORTANT — Owning side

👉 The side having **@JoinColumn** is the **Owner**.  
Owner controls the foreign key.

Here:

Profile → OWNER  
User → INVERSE SIDE (mappedBy)

---

## Cascade meaning

cascade = CascadeType.ALL

If you save User → Profile auto saved.

---
#### @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)  
-  here mappedby is use to know that this object is from owner side 
- it is written in inverse side (primary key) the child side

---
### Key Points
 - the owning side dictates the foregien key updates
 - updates to the mapped feild on the inverse side cannot update the foregien key
 - parent controlled the lifecycle of other, here if a profile is deleted, their user should also be deleted (HENCE PROFILE IS PARENT)
---

# 2️⃣ OneToMany & ManyToOne (MOST IMPORTANT ⭐)

This is used **everywhere**.

## Example

One User → many Addresses

### Database tables

USER  
id | name  
  
ADDRESS  
id | city | user_id (FK)

Many addresses belong to ONE user.

So DB always stores FK on **Many side**.

👉 Therefore:  
**ManyToOne is always the owner.**

---

## Address (Many side → OWNER)

@Entity  
@Getter @Setter  
public class Address {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String city;  
  
    @ManyToOne  
    @JoinColumn(name = "user_id")  
    private User user;  
}

---

## User (One side → INVERSE)

@Entity  
@Getter @Setter  
public class User {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String name;  
  
    @OneToMany(mappedBy = "user",  
               cascade = CascadeType.ALL,  
               orphanRemoval = true)  
    private List<Address> addresses = new ArrayList<>();  
  
    // helper method ❤️  
    public void addAddress(Address address){  
        addresses.add(address);  
        address.setUser(this);  
    }  
}

---

## orphanRemoval = true (VERY IMPORTANT)

If you remove address from list → delete from DB automatically.

user.getAddresses().remove(address);

DB row deleted automatically.

Interview favorite ⭐

---

# 🔥 Golden Rule to remember

ManyToOne → ALWAYS OWNER  
OneToMany → ALWAYS mappedBy

---

# 3️⃣ ManyToMany Relationship

## Example

Students enroll in Courses

A student can join many courses  
A course has many students

### Database tables

STUDENT  
COURSE  
STUDENT_COURSE (Join Table)  
student_id | course_id

---

## Student entity (Owner)

@Entity  
@Getter @Setter  
public class Student {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String name;  
  
    @ManyToMany  
    @JoinTable(  
        name = "student_course",  
        joinColumns = @JoinColumn(name = "student_id"),  
        inverseJoinColumns = @JoinColumn(name = "course_id")  
    )  
    private List<Course> courses = new ArrayList<>();  
}

---

## Course entity (Inverse)

@Entity  
@Getter @Setter  
public class Course {  
  
    @Id @GeneratedValue  
    private Long id;  
    private String title;  
  
    @ManyToMany(mappedBy = "courses")  
    private List<Student> students = new ArrayList<>();  
}

---

## What is JoinTable?

JPA automatically creates a **third table**.

student_course  
student_id | course_id

---

# ⚠️ Important Best Practice

Real projects rarely use direct ManyToMany ❌  
Instead we convert it to:

StudentCourse (extra entity)

Why?  
Because we may need extra fields like:

- enrollmentDate
- grade

---

# Relationship Summary Table 🧾

|Type|Owner Side|Annotation|
|---|---|---|
|OneToOne|side with FK|@JoinColumn|
|OneToMany|Many side|@ManyToOne|
|ManyToOne|Many side|@JoinColumn|
|ManyToMany|side with @JoinTable|join table|

---

# 💡 Most asked interview questions

1️⃣ Which side owns relation?  
→ Side with **@JoinColumn**

2️⃣ Where FK stored?  
→ On **Many side**

3️⃣ Why helper methods?  
→ To keep both sides in sync.

4️⃣ Why avoid @Data in entities?  
→ Infinite recursion in relationships.



![[Pasted image 20260504221310.png]]