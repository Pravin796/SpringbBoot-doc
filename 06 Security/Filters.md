
A filter is a component that sits **between the client and your controller** and can inspect, modify, allow, or block requests and responses.

When a request comes in:

```
Client   |   vFilter 1   |   vFilter 2   |   vFilter 3   |   vController
```

And when the response goes back:

```
Controller   |   vFilter 3   |   vFilter 2   |   vFilter 1   |   vClient
```

---

## Real-Life Example

Imagine entering an airport:

```
Passenger    |    vSecurity Check    |    vPassport Verification    |    vBoarding Gate    |    vFlight
```

Each checkpoint acts like a filter.

Similarly, in Spring:

```
Request   |   vAuthentication Filter   |   vAuthorization Filter   |   vController
```

---

## Why Do We Need Filters?

Filters are commonly used for:

- Authentication (Is the user logged in?)
    
- Authorization (Does the user have permission?)
    
- Logging requests
    
- JWT validation
    
- Rate limiting
    
- CORS handling
    
- Modifying requests/responses
    

---

## Simple Servlet Filter Example

```
@Componentpublic class LoggingFilter implements Filter {   
@Override    
public void doFilter( ServletRequest request, ServletResponse response,
					FilterChain chain) throws IOException, ServletException {      
		System.out.println("Request received");        
		chain.doFilter(request, response);        
		System.out.println("Response sent");    
}}
```

### Flow

Request comes:

```
Request received
```

Then:

```
chain.doFilter(request, response);
```

passes the request to the next filter (or controller).

After the controller finishes:

```
Response sent
```

---

## What is `FilterChain`?

Think of it as the list of remaining filters.

```
chain.doFilter(request, response);
```

means:

> "I'm done. Send the request to the next filter."

If you don't call it:

```
public void doFilter(...) {    System.out.println("Blocked");}
```

the request stops there and never reaches the controller.

---

## Filters in Spring Security

Spring Security itself is built using many filters.

When a request arrives:

```
Request   |   vSecurityContextHolderFilter   |   vUsernamePasswordAuthenticationFilter   |   vAuthorizationFilter   |   vController
```

Each filter has a specific responsibility.

---

## JWT Authentication Example

Suppose the request is:

```
GET /api/usersAuthorization: Bearer eyJhbGciOi...
```

A custom JWT filter can:

1. Read the Authorization header.
    
2. Extract the token.
    
3. Validate the token.
    
4. Load the user.
    
5. Store authentication information in Spring Security.
    

```
public class JwtAuthenticationFilter extends OncePerRequestFilter {    @Override    protected void doFilterInternal(            HttpServletRequest request,            HttpServletResponse response,            FilterChain filterChain)            throws ServletException, IOException {        String authHeader =                request.getHeader("Authorization");        if (authHeader != null &&            authHeader.startsWith("Bearer ")) {            String token =                    authHeader.substring(7);            // validate token            // load user            // set authentication        }        filterChain.doFilter(request, response);    }}
```

---

## Why `OncePerRequestFilter`?

In Spring Security, instead of implementing `Filter` directly, we usually extend:

```
OncePerRequestFilter
```

because Spring guarantees it runs **only once per request**.

```
public class JwtAuthenticationFilter        extends OncePerRequestFilter {}
```

This is what you'll use when building JWT authentication.

---

## How Filters Fit Into Your Security Config

You already have:

```
@Beanpublic SecurityFilterChain securityFilterChain(        HttpSecurity http) throws Exception {    http        .sessionManagement(c ->            c.sessionCreationPolicy(                SessionCreationPolicy.STATELESS            )        );    return http.build();}
```

Later, you'll add your JWT filter:

```
http.addFilterBefore(    jwtAuthenticationFilter,    UsernamePasswordAuthenticationFilter.class);
```

Meaning:

```
Request   |   vJwtAuthenticationFilter   |   vUsernamePasswordAuthenticationFilter   |   vController
```

The JWT filter runs first and authenticates the user before Spring Security performs authorization checks.

---

### Key Takeaway

A filter is simply a **checkpoint that every request passes through before reaching your controller**.

```
Client   |   vFilters   |   vController   |   vResponse
```

In Spring Security, authentication and authorization are implemented almost entirely through a chain of filters, which is why understanding filters is essential before learning JWT authentication.