Great topic 🙌 Pagination is **very important in Spring Boot + databases** and interviewers love it. Let’s learn it step-by-step in a simple way.

---

# 1️⃣ What is Pagination? (Why we need it)

Imagine your `Patient` table has **1,00,000 rows**.

If your API returns all rows:

```
GET /patients
```

Problems 🚨

- Very slow query
- Huge memory usage
- Big JSON response
- Bad user experience

Instead we send **small chunks of data** → called **pages**.

Example:

```
Page 1 → 10 recordsPage 2 → next 10 recordsPage 3 → next 10 records
```

This is **pagination**.

---

# 2️⃣ Pagination in Spring Boot (Spring Data JPA)

Spring gives built-in support using:

```
PageablePage<T>
```

These two are the heart of pagination ❤️

---

# 3️⃣ Step 1 — Repository Layer

Suppose we have entity:

```
@Entitypublic class Patient {    @Id    private Long id;    private String name;    private int age;}
```

### Add Pagination Method

```
public interface PatientRepository extends JpaRepository<Patient, Long> {    Page<Patient> findAll(Pageable pageable);}
```

👉 Notice return type is **Page<Patient>**

---

# 4️⃣ Step 2 — Service Layer

```
@Servicepublic class PatientService {    @Autowired    private PatientRepository repo;    public Page<Patient> getPatients(int page, int size) {        Pageable pageable = PageRequest.of(page, size);        return repo.findAll(pageable);    }}
```

### Important

```
PageRequest.of(page, size)
```

|Parameter|Meaning|
|---|---|
|page|page number (starts from 0 ⚠️)|
|size|records per page|

Example:

```
PageRequest.of(0,5) → first 5 recordsPageRequest.of(1,5) → next 5 records
```

---

# 5️⃣ Step 3 — Controller Layer

```
@RestController@RequestMapping("/patients")public class PatientController {    @Autowired    private PatientService service;    @GetMapping    public Page<Patient> getPatients(            @RequestParam int page,            @RequestParam int size) {        return service.getPatients(page, size);    }}
```

---

# 6️⃣ How API will be called

```
GET /patients?page=0&size=5
```

Response example 📦

```
{  "content": [    { "id": 1, "name": "Ram", "age": 25 },    { "id": 2, "name": "Shyam", "age": 30 }  ],  "totalPages": 20,  "totalElements": 100,  "size": 5,  "number": 0}
```

Spring automatically gives extra pagination info 😍

---

# 7️⃣ Understanding `Page<T>` (Very Important)

`Page` is not just list. It gives metadata:

```
Page<Patient> page = repo.findAll(pageable);page.getContent();        // actual datapage.getTotalPages();     // total pagespage.getTotalElements();  // total rowspage.getNumber();         // current pagepage.getSize();           // page sizepage.hasNext();           // next page exists?page.hasPrevious();       // prev page exists?
```

Interview favourite question ⭐

---

# 8️⃣ Sorting with Pagination 🔥

Pagination + Sorting together is common.

### Sort by age descending

```
Pageable pageable = PageRequest.of(page, size, Sort.by("age").descending());
```

### Sort by multiple fields

```
Pageable pageable = PageRequest.of(        page,        size,        Sort.by("age").descending()            .and(Sort.by("name").ascending()));
```

API call:

```
/patients?page=0&size=5&sort=age,desc
```

Spring can auto-handle sorting too!

---

# 9️⃣ Pagination with Custom Query

```
@Query("SELECT p FROM Patient p WHERE p.age > :age")Page<Patient> findOlderPatients(int age, Pageable pageable);
```

Use:

```
repo.findOlderPatients(25, PageRequest.of(0,5));
```

Yes — Pagination works with **JPQL queries also** 💥

---

# 🔟 Page vs Slice (Interview Question)

|Feature|Page|Slice|
|---|---|---|
|Total count query|YES|NO|
|Faster|❌|✅|
|Metadata|Full|Partial|

Use **Slice** when total count is not needed (performance optimization).

---

# 🎯 Real Life Flow Summary

1. Client sends page & size
2. Controller receives params
3. Service creates `PageRequest`
4. Repository fetches limited rows
5. Spring returns Page object with metadata

---

# ❤️ Small Tip (Best Practice)

Always give default values:

```
@GetMappingpublic Page<Patient> getPatients(        @RequestParam(defaultValue="0") int page,        @RequestParam(defaultValue="5") int size) {    return service.getPatients(page, size);}
```