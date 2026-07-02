# Problem First — Why DTO Projection?

Imagine you have this **User entity**:

@Entity  
public class User {  
    @Id  
    private Long id;  
    private String name;  
    private String email;  
    private String password;  
    private String address;  
    private String phone;  
}

Now suppose your API only needs:

👉 id, name, email

But when you do:

userRepository.findAll();

JPA fetches **ALL columns** 😬

SQL generated:

select * from user;

That means:

- password fetched ❌ (security risk)
- address fetched ❌ (not needed)
- phone fetched ❌ (not needed)

This is called **over-fetching**.

So we use **DTO Projection** to fetch **only required columns**.

---

# 🧠 What is DTO Projection?

DTO = **Data Transfer Object**

It is a **small class** that contains only required fields.

Instead of returning Entity → we return DTO.

---

# 🪄 Method 1 — Interface Based Projection (Easiest ⭐)

## Step 1 — Create DTO Interface

public interface UserSummaryDTO {  
    Long getId();  
    String getName();  
    String getEmail();  
}

⚠️ Important:

- Method names must match entity field names.

---

## Step 2 — Create Repository Method

public interface UserRepository extends JpaRepository<User, Long> {  
  
    List<UserSummaryDTO> findBy();  
}

That's it 😍  
No query needed!

---

## 🧾 Generated SQL

Spring automatically generates:

select id, name, email from user;

Only required columns fetched ✅

---

## Step 3 — Use in Service

public List<UserSummaryDTO> getUsers(){  
    return userRepository.findBy();  
}

Spring creates **proxy object** and fills data automatically.

---

# 🔥 Method 2 — Constructor Based DTO (Most Used in Real Projects)

This gives more control and is used in complex queries.

---

## Step 1 — Create DTO Class

public class UserSummaryDTO {  
  
    private Long id;  
    private String name;  
    private String email;  
  
    public UserSummaryDTO(Long id, String name, String email) {  
        this.id = id;  
        this.name = name;  
        this.email = email;  
    }  
}

---

## Step 2 — Write JPQL Query

public interface UserRepository extends JpaRepository<User, Long> {  
  
    @Query("""  
        SELECT new com.example.dto.UserSummaryDTO(u.id, u.name, u.email)  
        FROM User u  
    """)  
    List<UserSummaryDTO> fetchUserSummary();  
}

### 💡 MAGIC LINE

SELECT new com.example.dto.UserSummaryDTO(...)

This tells JPA:

👉 Don't fetch entity  
👉 Directly create DTO objects

---

## SQL Generated

select id, name, email from user;

Again only needed columns fetched ✔️

---

# 🧪 Method 3 — Dynamic Projection (Advanced & Powerful)

One method → return Entity OR DTO.

<T> List<T> findBy(Class<T> type);

Usage:

userRepository.findBy(UserSummaryDTO.class); // DTO  
userRepository.findBy(User.class);           // Entity

This is called **Dynamic Projection**.

---

# 🚀 Interface vs Constructor Projection

|Feature|Interface Projection|Constructor Projection|
|---|---|---|
|Code|Very easy|Slightly more code|
|Custom query|Limited|Full control|
|Complex joins|❌ Hard|✅ Best|
|Real projects|Medium|⭐ Most used|

👉 In real projects we mostly use **Constructor DTO Projection**.

---

# 🧠 When Should You Use DTO Projection?

Use DTO when:

- Returning API response
- Avoid exposing password fields
- Improve performance
- Fetching from joins
- Large tables

---

# 🎉 Final Flow Visualization

Without DTO ❌  
Entity → All columns → Convert to JSON → Send

With DTO ✅  
DB → Only needed columns → DTO → JSON → Send

Much faster ⚡


Examples

We want an API like:

GET /users/orders

Response should be:

[  
  {  
    "userName": "Pravin",  
    "email": "pravin@gmail.com",  
    "orderId": 101,  
    "amount": 5000  
  }  
]

⚠️ Notice:  
We don’t want full User or full Order entity.  
We want **combined partial data from both tables**.

This is called:

# 👉 DTO Projection with JOIN

---

# 🧱 Step 1 — Create Entities

## 👤 User Entity

@Entity  
public class User {  
    @Id  
    @GeneratedValue  
    private Long id;  
  
    private String name;  
    private String email;  
  
    @OneToMany(mappedBy = "user")  
    private List<Order> orders;  
}

---

## 📦 Order Entity

@Entity  
@Table(name = "orders")  
public class Order {  
    @Id  
    @GeneratedValue  
    private Long id;  
  
    private Double amount;  
  
    @ManyToOne  
    @JoinColumn(name = "user_id")  
    private User user;  
}

---

# 🧠 Database Tables

**user**

|id|name|email|
|---|---|---|

**orders**

| id | amount | user_id |

Relationship:

User 1 ---- * Orders

---

# 🚫 Without DTO (Bad Approach)

If we fetch Users → it loads Orders lazily/ eagerly  
Then convert to JSON → huge nested object 😵‍💫

We only want:

- user name
- email
- order id
- amount

So we use DTO.

---

# 🪄 Step 2 — Create JOIN DTO

public class UserOrderDTO {  
  
    private String userName;  
    private String email;  
    private Long orderId;  
    private Double amount;  
  
    public UserOrderDTO(String userName, String email,  
                        Long orderId, Double amount) {  
        this.userName = userName;  
        this.email = email;  
        this.orderId = orderId;  
        this.amount = amount;  
    }  
}

⚠️ Constructor order must match query order.

---

# ✨ Step 3 — Write JOIN Query in Repository

public interface UserRepository extends JpaRepository<User, Long> {  
  
    @Query("""  
        SELECT new com.example.dto.UserOrderDTO(  
            u.name,  
            u.email,  
            o.id,  
            o.amount  
        )  
        FROM User u  
        JOIN u.orders o  
    """)  
    List<UserOrderDTO> fetchUserOrders();  
}

---

# 🔍 Understanding the JPQL JOIN

## This line 👇

JOIN u.orders o

means:

User u  
INNER JOIN orders o ON u.id = o.user_id

---

## Generated SQL (What DB actually runs)

SELECT   
    u.name,  
    u.email,  
    o.id,  
    o.amount  
FROM user u  
JOIN orders o ON u.id = o.user_id;

🔥 Only 4 columns fetched from DB!

---

# 📊 Result Structure

If DB contains:

Users:

|id|name|email|
|---|---|---|
|1|Pravin|p@gmail.com|

Orders:

|id|amount|user_id|
|---|---|---|
|101|5000|1|
|102|3000|1|

Result DTO list:

[  
 { "userName":"Pravin","email":"p@gmail.com","orderId":101,"amount":5000 },  
 { "userName":"Pravin","email":"p@gmail.com","orderId":102,"amount":3000 }  
]

User repeated for each order → This is NORMAL.

---

# 🧠 INNER JOIN vs LEFT JOIN

### Current query = INNER JOIN

Users without orders ❌ NOT returned

If you want all users even without orders:

@Query("""  
    SELECT new com.example.dto.UserOrderDTO(  
        u.name,  
        u.email,  
        o.id,  
        o.amount  
    )  
    FROM User u  
    LEFT JOIN u.orders o  
""")  
List<UserOrderDTO> fetchUserOrders();

Now users without orders appear with:

orderId = null  
amount = null

---

# 💡 Very Important Interview Question

👉 Why not use `fetch join` here?

Because:

- Fetch join returns **Entity**
- We want **DTO**
- Fetch join loads full object graph (heavy)

DTO projection = best for APIs 🚀

---

# 🎯 Service Layer

@Service  
public class UserService {  
  
    private final UserRepository repo;  
  
    public UserService(UserRepository repo) {  
        this.repo = repo;  
    }  
  
    public List<UserOrderDTO> getUserOrders() {  
        return repo.fetchUserOrders();  
    }  
}

---

# 🏁 Final Architecture Flow

DB → JOIN → Partial Columns → DTO → Controller → JSON

Fast ✔️ Secure ✔️ Clean ✔️

---

# ⭐ Pro Tip (Real Project Pattern)

Most production apps never return Entities directly.  
They always return **DTOs with JOIN queries** like this.

---

Next awesome step would be 🔥  
**One user → list of orders grouped (nested DTO)**  
which is trickier and super popular in interviews.

Want to learn that next? 😄

Teach me DTO projection with JOIN (User + Orders)

Awesome — let’s go one level deeper and do the **real-world version** 🔥

Last time we returned **flat rows** (one row per order).  
Now we’ll build the version used in production APIs:

# 🎯 Goal (Nested Response)

We want this JSON:

[  
  {  
    "userId": 1,  
    "name": "Pravin",  
    "email": "p@gmail.com",  
    "orders": [  
      { "orderId": 101, "amount": 5000 },  
      { "orderId": 102, "amount": 3000 }  
    ]  
  }  
]

This is called:

# 👉 Nested DTO Projection (Parent → Children)

This is trickier because **JPA cannot directly build nested DTO lists** 😬  
So we do it in **2 steps**.

---

# 🧠 Why JPA Can’t Directly Create Nested DTO?

JPQL returns **rows**, not object trees.

SQL result looks like:

|userId|name|email|orderId|amount|
|---|---|---|---|---|
|1|Pravin|p@gmail.com|101|5000|
|1|Pravin|p@gmail.com|102|3000|

JPA can create:

- Entity graph ✔️
- Flat DTO ✔️
- Nested DTO ❌ (must group manually)

So we fetch **flat DTO** → then **group in Java**.

This is the **industry standard approach**.

---

# 🧱 Step 1 — Entities (recap)

User → OneToMany → Orders

---

# 🪄 Step 2 — Create FLAT DTO (Row DTO)

This represents **one DB row**.

public class UserOrderFlatDTO {  
  
    private Long userId;  
    private String name;  
    private String email;  
    private Long orderId;  
    private Double amount;  
  
    public UserOrderFlatDTO(Long userId, String name, String email,  
                            Long orderId, Double amount) {  
        this.userId = userId;  
        this.name = name;  
        this.email = email;  
        this.orderId = orderId;  
        this.amount = amount;  
    }  
  
    // getters  
}

---

# ✨ Step 3 — JPQL JOIN Query

@Query("""  
    SELECT new com.example.dto.UserOrderFlatDTO(  
        u.id,  
        u.name,  
        u.email,  
        o.id,  
        o.amount  
    )  
    FROM User u  
    LEFT JOIN u.orders o  
""")  
List<UserOrderFlatDTO> fetchUserOrdersFlat();

DB returns flat rows → Perfect.

---

# 🧩 Step 4 — Create FINAL Nested DTOs

## Order DTO

public class OrderDTO {  
    private Long orderId;  
    private Double amount;  
  
    public OrderDTO(Long orderId, Double amount) {  
        this.orderId = orderId;  
        this.amount = amount;  
    }  
}

---

## User DTO (contains list)

public class UserWithOrdersDTO {  
  
    private Long userId;  
    private String name;  
    private String email;  
    private List<OrderDTO> orders = new ArrayList<>();  
  
    public UserWithOrdersDTO(Long userId, String name, String email) {  
        this.userId = userId;  
        this.name = name;  
        this.email = email;  
    }  
  
    public void addOrder(OrderDTO order){  
        orders.add(order);  
    }  
}

---

# 🚀 Step 5 — MAGIC SERVICE LAYER (Grouping)

This is the most important part ⭐

public List<UserWithOrdersDTO> getUsersWithOrders() {  
  
    List<UserOrderFlatDTO> rows = repo.fetchUserOrdersFlat();  
  
    Map<Long, UserWithOrdersDTO> map = new LinkedHashMap<>();  
  
    for (UserOrderFlatDTO row : rows) {  
  
        // 1️⃣ Get or create user  
        UserWithOrdersDTO userDTO = map.computeIfAbsent(  
            row.getUserId(),  
            id -> new UserWithOrdersDTO(id, row.getName(), row.getEmail())  
        );  
  
        // 2️⃣ Add order if exists  
        if (row.getOrderId() != null) {  
            userDTO.addOrder(  
                new OrderDTO(row.getOrderId(), row.getAmount())  
            );  
        }  
    }  
  
    return new ArrayList<>(map.values());  
}

---

# 🧠 How This Works (Visualization)

### Flat rows from DB:

(1, Pravin, email, 101, 5000)  
(1, Pravin, email, 102, 3000)  
(2, Amit, email, null, null)

### Map after grouping:

1 → UserDTO(orders=[101,102])  
2 → UserDTO(orders=[])

### Final List returned:

Nested JSON 🎉

---

# ⚡ Why This Approach Is Used in Real Projects

|Approach|Problem|
|---|---|
|Fetch Entity graph|Huge memory + N+1|
|Fetch Join Entities|Loads full objects|
|Native SQL|Hard to maintain|
|⭐ Flat DTO + Grouping|Fast + clean + scalable|

This is **production standard**.

---

# 🎯 Interview Summary Answer

If interviewer asks:

**How do you fetch OneToMany DTO in JPA?**

You say:

1️⃣ Fetch flat DTO using JPQL JOIN  
2️⃣ Group using Map in service layer  
3️⃣ Return nested DTO

This answer impresses interviewers 😄