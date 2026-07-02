# What is a REST API (simple explanation)

REST API = a way for **frontend ↔ backend** to talk using HTTP.

Example:  
When you open a hospital app and click **“Book Appointment”**, the frontend sends a request to backend.

Frontend → “Please create appointment”  
Backend → “Appointment created ✅”

This communication happens using **REST endpoints**.

---

# 🧠 Real Life Analogy

Imagine a restaurant 🍽️

|Role|In REST|
|---|---|
|Customer|Frontend|
|Waiter|REST API|
|Kitchen|Backend logic + Database|

Customer never goes to kitchen directly.  
They talk to waiter (API).

---

# 🔑 Core REST Concepts (Very Important)

## 1️⃣ HTTP Methods (Actions)

These are the operations we perform on data.

|Method|Meaning|Example|
|---|---|---|
|GET|Read data|Get all doctors|
|POST|Create data|Add new patient|
|PUT|Update full data|Update patient details|
|PATCH|Update partial|Update email only|
|DELETE|Delete data|Remove appointment|

💡 This is called **CRUD**

Create → POST  
Read → GET  
Update → PUT/PATCH  
Delete → DELETE

---

## 2️⃣ Resources (Nouns not verbs)

In REST we deal with **resources**.

Bad API ❌

```
/getDoctors/createDoctor
```

Good REST API ✅

```
/doctors/patients/appointments
```

We use HTTP methods to define the action.

Example:

```
GET    /doctorsPOST   /doctorsGET    /doctors/1DELETE /doctors/1
```

---

## 3️⃣ JSON – The Language of APIs

Frontend and backend talk using JSON.

Example request:

```
POST /patients{  "name": "Rahul",  "email": "rahul@gmail.com",  "bloodGroup": "O+"}
```

Response:

```
{  "id": 1,  "name": "Rahul",  "message": "Patient created"}
```

---

# 🧩 REST in Spring Boot (Big Picture)

Spring Boot REST has 4 main layers:

```
Controller  → receives HTTP requestService     → business logicRepository  → database interactionDatabase    → MySQL/Postgres
```

Flow:

```
Client → Controller → Service → Repository → DB
```

---

# 🔥 First REST Controller Example

```
@RestController@RequestMapping("/hello")public class HelloController {    @GetMapping    public String sayHello() {        return "Hello REST API";    }}
```

When you open:

```
http://localhost:8080/hello
```

You get:

```
Hello REST API
```

🎉 You just created your first API.

---

# 🗺️ Our Learning Roadmap (important)

We’ll learn in this order:

### Phase 1 — Basics

1. @RestController
2. @RequestMapping / @GetMapping / @PostMapping
3. @PathVariable
4. @RequestParam
5. @RequestBody
6. ResponseEntity

### Phase 2 — Real CRUD API

7. Connect with Service layer
8. Connect with Repository (JPA)
9. Build Patient/Doctor CRUD APIs

### Phase 3 — Advanced REST

10. DTO pattern
11. Validation
12. Exception handling
13. Status codes
14. Pagination
15. Versioning
16. Security (JWT)

---

# ❤️ Important mindset

REST is NOT just annotations.  
It’s about **designing clean APIs**.

And since you already built entities for Hospital Management, this will fit perfectly 😄