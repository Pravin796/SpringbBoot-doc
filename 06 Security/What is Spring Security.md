Spring Security is a framework that protects your application by handling:

- Authentication (Who are you?)
- Authorization (What are you allowed to do?)
- Protection against attacks (CSRF, Session Fixation, etc.)

Think of an e-commerce app:

|Endpoint|Access|
|---|---|
|GET /products|Everyone|
|POST /users|Everyone (registration)|
|GET /orders|Logged-in users|
|DELETE /products/1|Admin only|

Spring Security helps enforce these rules.

---

# 2. Authentication vs Authorization

### Authentication

Verifying identity.

Example:

```
Username: pravinPassword: 1234
```

Spring checks:

```
Is this really Pravin?
```

If yes:

```
Authenticated = true
```

---

### Authorization

Checking permissions.

Example:

```
User: PravinRole: USER
```

Trying:

```
DELETE /products/1
```

Spring checks:

```
Does USER have permission?
```

No → 403 Forbidden

---

# 3. What Happens Without Security?

Suppose you have:

```
@GetMapping("/orders")public String getOrders() {    return "orders";}
```

Anyone can access:

```
GET /orders
```

No protection.

---

# 4. What Happens When Spring Security Is Added?

By default Spring Security:

- Secures every endpoint
- Creates a default user
- Generates a random password

You see something like:

```
Using generated security password:6a7b8c9d...
```

Then every request needs authentication.

# Learning Roadmap

I recommend learning Spring Security in this order:

### Phase 1 (Current)

✅ SecurityFilterChain

✅ Authentication vs Authorization

✅ Stateless Sessions

✅ CSRF

✅ Request Matchers

---

### Phase 2

- UserDetails
- UserDetailsService
- PasswordEncoder
- BCrypt

---

### Phase 3

- AuthenticationManager
- AuthenticationProvider
- DaoAuthenticationProvider

---

### Phase 4

- JWT
- JWT Filter
- Bearer Tokens
- Refresh Tokens

---

### Phase 5

- Roles (`USER`, `ADMIN`)
- Method Security

```
@PreAuthorize("hasRole('ADMIN')")
```