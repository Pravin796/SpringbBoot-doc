
**CSRF (Cross-Site Request Forgery)** is an attack where a malicious website tricks a user's browser into sending a request to another website where the user is already logged in.

The key point is:

> The attacker is not stealing your password. They are abusing the fact that your browser is already authenticated.

---

# A Real Example

Imagine a banking application.

You log in:

```
bank.com
```

After login, the server creates a session:

```
JSESSIONID=abc123
```

Your browser stores this cookie.

---

Now you visit a malicious site:

```
evil.com
```

The attacker puts this hidden form on their page:

```
<form action="https://bank.com/transfer" method="POST">    <input type="hidden" name="amount" value="10000">    <input type="hidden" name="to" value="attacker"></form><script>    document.forms[0].submit();</script>
```

---

Your browser sends:

```
POST /transferCookie: JSESSIONID=abc123
```

Notice something important:

The browser automatically includes the cookie.

The bank sees:

```
Valid SessionAuthenticated User
```

and may process the transfer.

The request came from the attacker, but the bank thinks it came from you.

That's CSRF.

---

# Why Does It Work?

Because browsers automatically send cookies.

Example:

```
Cookie: JSESSIONID=abc123
```

The browser sends it without asking you.

That's the entire reason CSRF exists.

---

# How Spring Security Prevents It

Spring Security generates a random CSRF token.

Example:

```
f8a91bc2-xyz-123
```

The server stores it.

When a form is submitted:

```
<input type="hidden"       name="_csrf"       value="f8a91bc2-xyz-123">
```

---

The request becomes:

```
POST /transfer_csrf=f8a91bc2-xyz-123
```

Spring checks:

```
Token in request==Token stored in session
```

If they match:

```
Request allowed
```

Otherwise:

```
403 Forbidden
```

---

# Why Can't the Attacker Know the Token?

The attacker can create:

```
<form action="https://bank.com/transfer">
```

But they cannot read:

```
f8a91bc2-xyz-123
```

stored on the bank's site.

So their forged request won't contain the correct token.

Spring rejects it.

---

# Why Do GET Requests Usually Not Need CSRF?

GET requests should only read data.

Example:

```
GET /usersGET /products
```

No state changes.

CSRF protection is mainly for:

```
POSTPUTPATCHDELETE
```

because they modify data.

---

# Why Do We Disable CSRF in JWT APIs?

Consider your future Spring Boot API.

Login:

```
POST /login
```

Response:

```
{  "token": "eyJhbGciOi..."}
```

Client stores JWT.

Every request:

```
Authorization: Bearer eyJhbGciOi...
```

---

Notice:

No cookies.

No sessions.

No automatic browser authentication.

The browser does **not** automatically send:

```
Authorization: Bearer ...
```

The frontend must explicitly attach it.

Example:

```
fetch("/orders", {  headers: {    Authorization: "Bearer eyJ..."  }});
```

Because of this:

```
Attacker's site cannot force the browserto include your JWT.
```

The attack mechanism disappears.

That's why for stateless JWT APIs we usually write:

```
http.csrf(AbstractHttpConfigurer::disable);
```

---

# Rule of Thumb

### Session + Cookies

```
SessionCreationPolicy.IF_REQUIRED
```

or

```
SessionCreationPolicy.ALWAYS
```

➡️ Keep CSRF enabled.

---

### JWT + Bearer Token

```
SessionCreationPolicy.STATELESS
```

➡️ Usually disable CSRF.

---

# Quick Interview Answer

**What is CSRF?**

> CSRF (Cross-Site Request Forgery) is an attack where a malicious website tricks a user's browser into sending authenticated requests to another website using the user's existing session cookie.

**How does Spring Security prevent it?**

> Spring Security generates a CSRF token and requires state-changing requests (POST, PUT, DELETE, PATCH) to include that token. Requests without a valid token are rejected.

**Why is CSRF usually disabled in JWT-based APIs?**

> Because JWT authentication uses Authorization headers instead of session cookies, and browsers do not automatically attach Authorization headers to cross-site requests. Therefore the traditional CSRF attack vector does not exist.