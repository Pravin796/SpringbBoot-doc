# What is Entity Lifecycle?

Every JPA entity moves through **4 states** during its life.

Think of an entity like a student in a school system 🎓

|State|Meaning|
|---|---|
|Transient|Object created, NOT saved in DB|
|Persistent|Managed by Hibernate + stored in DB|
|Detached|Was managed before, now disconnected|
|Removed|Marked for deletion|

Let’s go one by one with examples 👇

---

# 1️⃣ Transient State (New Object)

Entity exists only in **Java memory**, not in DB.

User user = new User();  
user.setName("Pravin");

At this moment:

- No DB row
- Hibernate not tracking it
- Just a normal Java object

👉 This is called **Transient**

💡 Think: “Not yet admitted to school”

---

# 2️⃣ Persistent State (Managed by Hibernate)

When we save the entity:

entityManager.persist(user);

Now Hibernate:

- Assigns ID
- Starts tracking changes
- Will sync with DB automatically

This state is called **Persistent (Managed)**.

💡 Think: “Student enrolled in school”

---

## Magic of Persistent state ✨

Once entity is persistent, **you don't need to call save again**.

user.setName("Rahul");

Hibernate detects change automatically and runs SQL:

UPDATE user SET name='Rahul' WHERE id=1;

This feature is called:

# 👉 Dirty Checking

Hibernate checks what changed and updates DB automatically.

Super important concept ⭐

---

# 3️⃣ Detached State (Disconnected)

When session closes or transaction ends:

entityManager.detach(user);

Now:

- Object still exists
- But Hibernate **no longer tracks it**

Changes will NOT be saved:

user.setName("Amit"); // ❌ Not saved

💡 Think: “Student left the school”

---

## How to make detached → persistent again?

Use **merge()**

entityManager.merge(user);

Hibernate re-attaches it.

---

# 4️⃣ Removed State (Delete)

When we delete entity:

entityManager.remove(user);

Now entity is:

- Marked for deletion
- Will be removed from DB on commit

SQL executed:

DELETE FROM user WHERE id=1;

💡 Think: “Student removed from school records”

---

# 🔁 Full Lifecycle Flow

new User()  
   ↓  
Transient  
   ↓ persist()  
Persistent (Managed)  
   ↓ detach() / session closed  
Detached  
   ↓ remove()  
Removed

---

# 🧾 Real Spring Boot Example

@Transactional  
public void updateUser(Long id) {  
    User user = userRepository.findById(id).get(); // Persistent  
  
    user.setName("New Name"); // No save() needed 😲  
}

Hibernate auto-updates DB due to **dirty checking**.

This surprises many beginners 😄

---

# 🔥 Important Terms

## Persistence Context

It is like a **cache of managed entities**.

Hibernate keeps all persistent entities here and tracks changes.

Also called:  
👉 First Level Cache

---

# 🎯 Interview Questions

### ❓ What are Hibernate entity states?

1. Transient
2. Persistent
3. Detached
4. Removed

### ❓ What is dirty checking?

Hibernate automatically detects changes in persistent entities and updates DB.

### ❓ Do we need save() after updating entity?

❌ No — if entity is persistent inside transaction.

---

# 💡 Most common beginner mistake

People write:

userRepository.save(user);

even after fetching and updating.

Not needed if inside `@Transactional`.