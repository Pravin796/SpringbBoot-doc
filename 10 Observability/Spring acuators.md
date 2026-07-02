

# What is Spring Boot Actuator?

**Spring Boot Actuator** is a module that provides **production-ready features** for monitoring and managing your application.

Think of it as a **health dashboard** for your Spring Boot application.

It lets you answer questions like:

- ✅ Is my application running?
    
- ✅ How much memory is being used?
    
- ✅ How many HTTP requests have been made?
    
- ✅ Which beans are loaded?
    
- ✅ What configuration properties are active?
    
- ✅ What metrics (CPU, JVM, GC) are available?
    

Without writing any code, Actuator exposes endpoints that provide this information.

---

# Real-world analogy

Imagine you're driving a car.

The car has a dashboard showing:

- Speed
    
- Fuel level
    
- Engine temperature
    
- Battery status
    

You don't open the engine every minute to check if everything is okay.

Similarly,

Spring Boot Actuator is the **dashboard** for your application.

Instead of opening the application internals, you simply call endpoints like

```
/actuator/health
```

or

```
/actuator/metrics
```

and immediately know what's happening.

---

# How to add Actuator

For Maven:

```
<dependency>    <groupId>org.springframework.boot</groupId>    <artifactId>spring-boot-starter-actuator</artifactId></dependency>
```

Run your application.

---

# Default Endpoint

Visit

```
http://localhost:8080/actuator
```

You may only see something like

```
{  "_links": {    "self": {      "href": "http://localhost:8080/actuator"    },    "health": {      "href": "http://localhost:8080/actuator/health"    }  }}
```

By default, only a few endpoints are exposed.

---

# Most Common Endpoints

## 1. Health

```
GET /actuator/health
```

Output

```
{  "status": "UP"}
```

Meaning:

```
Application is running successfully.
```

If the database is down:

```
{  "status": "DOWN"}
```

---

## 2. Info

```
GET /actuator/info
```

Initially:

```
{}
```

You can add information.

`application.properties`

```
management.info.env.enabled=trueinfo.app.name=Shopping Cartinfo.app.version=1.0info.company=JobHunt
```

Now

```
{  "app": {    "name": "Shopping Cart",    "version": "1.0"  },  "company": "JobHunt"}
```

---

## 3. Metrics

```
GET /actuator/metrics
```

Returns

```
[  "jvm.memory.used",  "jvm.gc.pause",  "http.server.requests",  "system.cpu.usage",  ...]
```

To see one metric

```
GET /actuator/metrics/jvm.memory.used
```

Example

```
{  "name":"jvm.memory.used",  "measurements":[      {         "value":42123456      }  ]}
```

---

## 4. Beans

```
GET /actuator/beans
```

Shows every Spring Bean.

Example

```
UserControllerUserServiceSecurityFilterChainPasswordEncoderObjectMapper
```

Useful when debugging dependency injection.

---

## 5. Environment

```
GET /actuator/env
```

Shows

```
application.propertiesEnvironment VariablesSystem PropertiesActive Profiles
```

---

## 6. Mappings

```
GET /actuator/mappings
```

Shows every endpoint in your application.

Example

```
GET /usersPOST /loginPOST /checkoutGET /products
```

Very useful when debugging APIs.

---

## 7. Config Props

```
GET /actuator/configprops
```

Displays all configuration properties loaded into your application.

---

## 8. Loggers

```
GET /actuator/loggers
```

You can even change logging levels at runtime.

For example

```
POST /actuator/loggers/com.example
```

```
{   "configuredLevel":"DEBUG"}
```

No application restart required.

---

## 9. Heap Dump

```
GET /actuator/heapdump
```

Downloads the JVM heap.

Mostly used by developers when debugging memory leaks.

---

## 10. Thread Dump

```
GET /actuator/threaddump
```

Shows all currently running threads.

Useful when diagnosing deadlocks or performance issues.

---

# Exposing More Endpoints

By default, Spring Boot exposes only a limited set of actuator endpoints.

In `application.properties`:

```
management.endpoints.web.exposure.include=*
```

Now all endpoints become accessible.

Or expose only specific ones:

```
management.endpoints.web.exposure.include=health,info,metrics
```

This is the recommended approach for production.

---

# Changing the Base Path

Default

```
/actuator
```

Change it:

```
management.endpoints.web.base-path=/manage
```

Now:

```
/manage/health
```

instead of

```
/actuator/health
```

---

# Security Considerations

Since you're learning **Spring Security**, this is especially important.

In production:

- ✅ Expose only the endpoints you need.
    
- ✅ Secure sensitive endpoints (like `beans`, `env`, `heapdump`) so only authorized users (typically admins) can access them.
    
- ❌ Do **not** expose all endpoints (`include=*`) on a public application.
    

For example, you might allow everyone to access health checks:

```
http.authorizeHttpRequests(auth -> auth    .requestMatchers("/actuator/health").permitAll()    .requestMatchers("/actuator/**").hasRole("ADMIN")    .anyRequest().authenticated());
```

---

# Why Companies Use Actuator

In production, Actuator helps operations teams and monitoring tools:

- Monitor application health
    
- Track JVM memory usage
    
- Observe CPU utilization
    
- Measure HTTP request counts and latency
    
- Detect slow endpoints
    
- Debug production issues
    
- Integrate with monitoring systems like Prometheus and Grafana
    

---

# Summary

|Endpoint|Purpose|
|---|---|
|`/actuator`|Lists exposed actuator endpoints|
|`/actuator/health`|Checks if the application is healthy|
|`/actuator/info`|Displays application information|
|`/actuator/metrics`|Shows application and JVM metrics|
|`/actuator/beans`|Lists all Spring beans|
|`/actuator/env`|Displays environment properties|
|`/actuator/mappings`|Lists all request mappings|
|`/actuator/loggers`|View or change logging levels|
|`/actuator/threaddump`|Shows all JVM threads|
|`/actuator/heapdump`|Generates a JVM heap dump|

As you continue learning Spring Boot, Actuator becomes even more valuable when combined with Spring Security, Docker, Kubernetes, and monitoring tools such as Micrometer and Prometheus, which are commonly used in production environments.