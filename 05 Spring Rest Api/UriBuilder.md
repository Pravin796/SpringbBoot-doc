`UriComponentsBuilder` in Spring Framework is used to **build URLs safely and dynamically**.

In your code, it is mainly used to create the URL of the newly created user resource.

---

## Why do we use it?

When a new resource is created using `POST`, REST APIs usually return:

- `201 Created`
- and a `Location` header containing the URL of the created resource

Example response:

```
HTTP/1.1 201 CreatedLocation: /users/5
```

Spring helps generate that URL using `UriComponentsBuilder`.

---

# Your Code

```
@PostMappingpublic ResponseEntity<UserDto> createUser(        @RequestBody RegisterUserRequest request,        UriComponentsBuilder uriBuilder){    var user = UserMapperUtil.toEntity(request);    userRepository.save(user);    var userDto = UserMapperUtil.toDto(user);    var uri = uriBuilder            .path("/users/{id}")            .buildAndExpand(userDto.getId())            .toUri();    return ResponseEntity.created(uri).body(userDto);}
```

---

# Step-by-Step Explanation

---

## 1. `UriComponentsBuilder uriBuilder`

Spring automatically provides this object.

It helps construct URLs.

Think of it like:

```
StringBuilder
```

but specifically for URLs.

---

# 2. `.path("/users/{id}")`

```
uriBuilder.path("/users/{id}")
```

This defines the URL pattern.

Currently:

```
/users/{id}
```

`{id}` is a placeholder.

---

# 3. `.buildAndExpand(userDto.getId())`

```
.buildAndExpand(userDto.getId())
```

This replaces `{id}` with the actual ID.

If:

```
userDto.getId() = 5
```

Then:

```
/users/5
```

gets generated.

---

# 4. `.toUri()`

```
.toUri()
```

Converts it into a Java `URI` object.

Result:

```
URI uri = /users/5
```

---

# 5. `ResponseEntity.created(uri)`

```
ResponseEntity.created(uri)
```

This automatically:

- sets status code to `201 Created`
- sets `Location` header

Equivalent to:

```
HTTP 201 CreatedLocation: /users/5
```

---

# Final Response Example

If user ID becomes `10`:

```
Status: 201 CreatedLocation: /users/10
```

Response body:

```
{  "id": 10,  "name": "Pravin"}
```

---

# Visual Flow

```
POST /users       │       ▼Save user in DB       │       ▼Generated ID = 10       │       ▼uriBuilder.path("/users/{id}")       │       ▼buildAndExpand(10)       │       ▼/users/10       │       ▼ResponseEntity.created(uri)
```

---

# Why not directly write String?

You could do:

```
URI uri = URI.create("/users/" + userDto.getId());
```

But `UriComponentsBuilder` is better because:

- safer
- handles encoding
- cleaner
- works with query params
- works with dynamic paths
- standard Spring approach

---

# Another Example

## Query Parameters

```
uriBuilder    .path("/users")    .queryParam("page", 1)    .queryParam("size", 5)    .toUriString();
```

Result:

```
/users?page=1&size=5
```

---

# Most Important Line

This is the key line:

```
var uri = uriBuilder        .path("/users/{id}")        .buildAndExpand(userDto.getId())        .toUri();
```

Meaning:

> "Create the URL of the newly created user resource."

---

# REST Best Practice

Returning the location of a newly created resource is a REST convention.

That’s why this pattern is very common in Spring Boot APIs.

 and what if we dont create the uri on 201 post then what happen ??

Nothing will “break” technically if you don’t include the URI with a `201 Created` response. The API will still work.

Example without URI:

```
@PostMappingpublic ResponseEntity<UserDto> createUser(        @RequestBody RegisterUserRequest request){    var user = UserMapperUtil.toEntity(request);    userRepository.save(user);    var userDto = UserMapperUtil.toDto(user);    return ResponseEntity.status(201).body(userDto);}
```

This still returns:

```
HTTP/1.1 201 Created
```

with the user data in the body.

---

But according to REST conventions, a `201 Created` response should ideally include a `Location` header pointing to the newly created resource.

Example:

```
HTTP/1.1 201 CreatedLocation: /users/10
```

That tells the client:

> “The new resource was successfully created, and you can access it here.”

---

# What happens if you don’t provide it?

## 1. Client won't know resource URL automatically

Without `Location`:

```
HTTP/1.1 201 Created
```

The frontend/client may not know:

- where the created resource exists
- what URL to call next

unless you manually include the ID in the response body.

---

# 2. Less RESTful

REST APIs commonly follow this pattern:

```
POST /users→ 201 Created→ Location: /users/10
```

Without the URI, your API is still valid, but less aligned with REST standards.

---

# 3. Some clients/tools expect it

Certain API consumers, SDKs, or integrations may expect the `Location` header after creation.

Especially in:

- enterprise APIs
- hypermedia APIs
- HATEOAS-based systems

---

# Is it mandatory?

No.

You can absolutely do this:

```
return ResponseEntity.ok(userDto);
```

or

```
return ResponseEntity.status(201).body(userDto);
```

and many real-world APIs do exactly that.

---

# Common Real-World Approaches

## Approach 1 — Full REST style

```
return ResponseEntity.created(uri).body(userDto);
```

Response:

```
201 CreatedLocation: /users/10
```

Best practice.

---

## Approach 2 — Simple API style

```
return ResponseEntity.status(201).body(userDto);
```

Still completely fine.

Frontend gets:

```
{  "id": 10,  "name": "Pravin"}
```

---

# When URI becomes very useful

Suppose frontend creates a user and immediately wants to open the profile page.

With `Location` header:

```
Location: /users/10
```

Frontend instantly knows where the new resource lives.

---

# Simple Rule

| Situation                  | Use URI?         |
| -------------------------- | ---------------- |
| Learning REST properly     | Yes              |
| Enterprise APIs            | Yes              |
| Simple internal project    | Optional         |
| Small frontend/backend app | Usually optional |