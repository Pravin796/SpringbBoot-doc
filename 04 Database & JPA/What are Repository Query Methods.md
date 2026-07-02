

When you extend `CrudRepository` / `JpaRepository`, Spring automatically **generates SQL queries from method names**.

You don’t write SQL.  
Spring reads the method name → builds JPQL/SQL → executes it.

This feature is called **Query Method / Derived Query**.

Example:

public interface UserRepository extends JpaRepository<User, Long> {  
    User findByEmail(String email);  
}

You never wrote SQL… but Spring executes:

SELECT * FROM user WHERE email = ?

Magic ✨

---

# 🧠 How Spring Understands the Method Name

Spring splits the method name into parts:

find  By   Email  
^     ^    ^  
type  by   field name

General pattern:

<Prefix>By<Field><Condition>

---

# 🧩 Step 1 — Query Prefix Keywords

These tell Spring **what type of query to run**

|Prefix|Meaning|
|---|---|
|`findBy`|return result|
|`getBy`|same as find|
|`readBy`|same|
|`queryBy`|same|
|`countBy`|returns count|
|`existsBy`|returns true/false|
|`deleteBy`|delete records|

### Example

User findByEmail(String email);  
boolean existsByEmail(String email);  
long countByRole(String role);  
void deleteByEmail(String email);

---

# 🧩 Step 2 — Field Names

After `By`, Spring expects **entity field names**.

Entity:

class User {  
    String name;  
    String email;  
    int age;  
}

You must use **exact field names**, not column names.

✔️ Correct:

findByEmail()  
findByName()  
findByAge()

❌ Wrong:

findByEmailId()  // field doesn't exist

---

# 🧩 Step 3 — Conditions Keywords

Now comes the powerful part 🔥  
You can add conditions like SQL **WHERE operators**.

## Equality (default)

User findByEmail(String email);

SQL → `WHERE email = ?`

---

## Comparison Operators

|Keyword|SQL|
|---|---|
|`GreaterThan`|`>`|
|`LessThan`|`<`|
|`GreaterThanEqual`|`>=`|
|`LessThanEqual`|`<=`|
|`Between`|BETWEEN|
|`After`|> (dates)|
|`Before`|< (dates)|

Examples:

List<User> findByAgeGreaterThan(int age);  
List<User> findByAgeBetween(int a, int b);

---

## LIKE / String Search

|Keyword|SQL|
|---|---|
|`Containing`|LIKE %value%|
|`StartingWith`|LIKE value%|
|`EndingWith`|LIKE %value|
|`Like`|LIKE|

List<User> findByNameContaining(String name);

SQL:

WHERE name LIKE %?%

---

## NULL checks

List<User> findByEmailIsNull();  
List<User> findByEmailIsNotNull();

---

## Boolean checks

List<User> findByActiveTrue();  
List<User> findByActiveFalse();

---

# 🧩 Step 4 — AND / OR Conditions

Spring allows combining fields 😍

### AND (default)

User findByEmailAndPassword(String email, String pass);

SQL:

WHERE email=? AND password=?

### OR

List<User> findByNameOrEmail(String name, String email);

---

# 🧩 Step 5 — Sorting

List<User> findByAgeOrderByNameAsc(int age);  
List<User> findByAgeOrderByNameDesc(int age);

SQL:

ORDER BY name ASC

---

# 🧩 Step 6 — Top / First (LIMIT)

User findTopByOrderByAgeDesc();  
User findFirstByOrderByAgeAsc();

SQL:

LIMIT 1

---

# 🧩 Step 7 — Ignore Case

User findByEmailIgnoreCase(String email);  
List<User> findByNameContainingIgnoreCase(String name);

---

# 🔥 Real Interview Style Examples

List<User> findByAgeGreaterThan(int age);  
  
List<User> findByNameStartingWith(String prefix);  
  
User findTopByOrderBySalaryDesc();  
  
boolean existsByEmail(String email);  
  
long countByRole(String role);  
  
List<User> findByNameContainingAndAgeGreaterThan(String name, int age);

Spring generates ALL queries automatically 🤯

---

# 🚨 Important Rule

Spring only generates queries if the method name follows the **naming convention**.

If query becomes complex → use:

@Query("select u from User u where u.salary > :salary")  
List<User> findRichUsers(@Param("salary") double salary);

here :  is used for take the value from the params 

use jpql query generator using the alt+enter select "extract jpql query and configure"

---

# 🎯 Summary

Repository query methods =  
👉 Write method name  
👉 Spring converts to SQL  
👉 Executes automatically

Pattern:

find / count / exists / delete  
        +  
By  
        +  
FieldName  
        +  
Condition.


