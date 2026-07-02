In a **Spring Boot project**, **Maven dependencies** are the libraries your project needs to run. They are written inside the **`pom.xml`** file.

Maven automatically **downloads these libraries from the internet** and adds them to your project.

---

# 1. What is Maven?

**Maven** is a **build automation tool** used for Java projects.

It helps to:

- Manage dependencies (libraries)
- Build the project
- Run tests
- Package the application

Example libraries:

- Spring Boot
- Hibernate
- MySQL Connector
- Lombok

---

# 2. Where Dependencies Are Written

In Spring Boot, dependencies are written in **`pom.xml`**

Example structure:

<project>  
    <dependencies>  
  
        <!-- Spring Boot Web -->  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-web</artifactId>  
        </dependency>  
  
    </dependencies>  
</project>

---

# 3. Important Spring Boot Dependencies

## 1️⃣ Web Dependency (For APIs)

Used for creating **REST APIs and web applications**.

<dependency>  
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-web</artifactId>  
</dependency>

This includes:

- Spring MVC
- Embedded Tomcat
- Jackson (JSON conversion)

---

## 2️⃣ JPA Dependency (Database)

Used for **database operations**.

<dependency>  
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-data-jpa</artifactId>  
</dependency>

Includes:

- Hibernate
- JPA
- ORM tools

---

## 3️⃣ MySQL Driver

Used to connect **Spring Boot with MySQL database**.

<dependency>  
 <groupId>com.mysql</groupId>  
 <artifactId>mysql-connector-j</artifactId>  
</dependency>

---

## 4️⃣ Lombok (Optional but Useful)

Reduces boilerplate code like **getters, setters, constructors**.

<dependency>  
 <groupId>org.projectlombok</groupId>  
 <artifactId>lombok</artifactId>  
 <optional>true</optional>  
</dependency>

Example without Lombok:

private String name;  
  
public String getName(){  
 return name;  
}

With Lombok:

@Getter  
@Setter  
private String name;

---

## 5️⃣ Testing Dependency

Used for **unit testing**.

<dependency>  
 <groupId>org.springframework.boot</groupId>  
 <artifactId>spring-boot-starter-test</artifactId>  
 <scope>test</scope>  
</dependency>

---

# 4. Example Complete Dependencies

A basic Spring Boot project usually has:

<dependencies>  
  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>  
  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-data-jpa</artifactId>  
    </dependency>  
  
    <dependency>  
        <groupId>com.mysql</groupId>  
        <artifactId>mysql-connector-j</artifactId>  
    </dependency>  
  
    <dependency>  
        <groupId>org.projectlombok</groupId>  
        <artifactId>lombok</artifactId>  
        <optional>true</optional>  
    </dependency>  
  
</dependencies>

---

# 5. What is "starter" in Spring Boot?

Example:

spring-boot-starter-web

Starter means **a bundle of multiple dependencies**.

For example:

`spring-boot-starter-web` includes:

- Spring MVC
- Jackson
- Validation
- Embedded Tomcat

So you **don't need to add them separately**.

---

# 6. Maven Lifecycle (Important)

Common Maven commands:

mvn clean  
mvn install  
mvn spring-boot:run

Example:

Run Spring Boot project:

mvn spring-boot:run

---

# 7. Important for Interviews

They often ask:

**What is Maven dependency?**

Answer:

> Maven dependency is an external library required by a project, which is declared in the `pom.xml` file and automatically downloaded by Maven from a central repository.