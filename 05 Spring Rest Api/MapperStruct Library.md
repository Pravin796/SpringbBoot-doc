  Great topic 🙌 MapStruct is **very popular in Spring Boot projects** for converting Entities ⇄ DTOs cleanly.

---

# What is MapStruct?

**MapStruct** is a **Java annotation processor** that generates mapping code at **compile time** to convert one object to another.

Most commonly used for:

- Entity → DTO
    
- DTO → Entity
    
- Request → Entity
    
- Entity → Response
    

Instead of writing manual mapping like:

```
UserDto dto = new UserDto();dto.setId(user.getId());dto.setName(user.getName());dto.setEmail(user.getEmail());
```

MapStruct generates this code automatically 🚀

---

# Why we use MapStruct?

### Problems without MapStruct

Manual mapping causes:

- Too much boilerplate code 😵
    
- Easy to forget fields → bugs
    
- Hard to maintain in big projects
    
- Performance issues if using reflection libraries (like ModelMapper)
    

### MapStruct advantages

✔ Compile-time generated code (FAST ⚡)  
✔ No reflection  
✔ Type safe (compile errors if mapping wrong)  
✔ Clean architecture (DTO layer separated)

Think of MapStruct as **Lombok but for mapping**.

---

# How MapStruct Works (Simple Flow)

```
Entity <----> Mapper Interface <----> DTO                    |          MapStruct generates implementation
```

You only write an **interface**, MapStruct generates the class behind the scenes.

---

# Step-by-Step Implementation in Spring Boot

We’ll build a simple example:

```
User Entity  ⇄  UserDto
```

---

# 1️⃣ Add Dependencies

### Maven

```
<dependencies>    <!-- MapStruct -->    <dependency>        <groupId>org.mapstruct</groupId>        <artifactId>mapstruct</artifactId>        <version>1.5.5.Final</version>    </dependency>    <!-- Annotation Processor -->    <dependency>        <groupId>org.mapstruct</groupId>        <artifactId>mapstruct-processor</artifactId>        <version>1.5.5.Final</version>        <scope>provided</scope>    </dependency></dependencies>
```

⚠️ VERY IMPORTANT → enable annotation processing in IntelliJ  
Settings → Build → Compiler → Annotation Processors → ✔ Enable

---

# 2️⃣ Create Entity

```
@Entity@Getter @Setterpublic class User {    @Id    @GeneratedValue(strategy = GenerationType.IDENTITY)    private Long id;    private String name;    private String email;    private String password;}
```

---

# 3️⃣ Create DTO

DTO is used to **hide sensitive data** like password.

```
@Getter @Setterpublic class UserDto {    private Long id;    private String name;    private String email;}
```

Notice → password removed 🔐

---

# 4️⃣ Create Mapper Interface (MAIN PART)

```
import org.mapstruct.Mapper;@Mapper(componentModel = "spring")public interface UserMapper {    UserDto toDto(User user);    User toEntity(UserDto dto);}
```

That’s it 😍  
No implementation needed.

At compile time MapStruct generates:

```
UserMapperImpl.java (auto generated)
```

Spring automatically injects it as a bean.

---

# 5️⃣ Use Mapper in Service Layer

```
@Service@RequiredArgsConstructorpublic class UserService {    private final UserRepository userRepository;    private final UserMapper userMapper;    public List<UserDto> getAllUsers() {        return userRepository.findAll()                .stream()                .map(userMapper::toDto)                .toList();    }    public UserDto createUser(UserDto dto) {        User user = userMapper.toEntity(dto);        User saved = userRepository.save(user);        return userMapper.toDto(saved);    }}
```

See how clean this becomes ✨

No manual mapping anywhere.

---

# 6️⃣ Controller Example

```
@RestController@RequestMapping("/users")@RequiredArgsConstructorpublic class UserController {    private final UserService userService;    @GetMapping    public List<UserDto> getUsers(){        return userService.getAllUsers();    }    @PostMapping    public UserDto createUser(@RequestBody UserDto dto){        return userService.createUser(dto);    }}
```

---

# Advanced MapStruct Features (Important for Interviews)

## 🔹 1. Ignore fields

```
@Mapper(componentModel = "spring")public interface UserMapper {    @Mapping(target = "password", ignore = true)    User toEntity(UserDto dto);    UserDto toDto(User user);}
```

---

## 🔹 2. Different field names

```
class User {   private String fullName;}class UserDto {   private String name;}
```

Mapping:

```
@Mapping(source = "fullName", target = "name")UserDto toDto(User user);
```

---

## 🔹 3. Mapping Lists automatically

```
List<UserDto> toDtoList(List<User> users);
```

MapStruct generates loop automatically 🤯

---

## 🔹 4. Nested Mapping (very common)

Example:

```
Order → contains → UserOrderDto → contains → UserDto
```

```
@Mapper(componentModel = "spring", uses = UserMapper.class)public interface OrderMapper {    OrderDto toDto(Order order);}
```

MapStruct will reuse UserMapper automatically.

---

# When to Use MapStruct?

Use MapStruct when project has:

- DTO layer
    
- Large APIs
    
- Clean architecture
    
- Microservices
    
- Production apps
    

Almost every **real Spring Boot project** uses it.

---

# Summary

MapStruct is used to:

|Without MapStruct|With MapStruct|
|---|---|
|Manual setters|Auto generated mapping|
|Boilerplate code|Clean code|
|Runtime reflection|Compile-time fast|
|Hard to maintain|Easy & safe|

---

If you want, next we can implement MapStruct in your **Hospital Management project** (Doctor ↔ DoctorDto, Patient ↔ PatientDto). That would be a perfect real-world practice 😊