## What is a Mapper in Java?

A **Mapper** in Java is a component or pattern used to **convert one object type to another**, most commonly between:

- **Entity ↔ DTO**
    
- **Domain model ↔ API response**
    
- **Request model ↔ Business object**
    

The goal is to **separate concerns** and avoid exposing internal models directly.

---

## Why Do We Use Mappers?

Interviewers love this question. Key reasons:

1. **Separation of layers**
    
    - Prevents tight coupling between persistence, business, and presentation layers
        
2. **Security**
    
    - Avoid exposing sensitive entity fields (e.g., passwords, internal IDs)
        
3. **Maintainability**
    
    - Changes in one layer don’t break others
        
4. **Clean Architecture**
    
    - Encourages Single Responsibility Principle (SRP)
        

---

## Common Use Case Example

```java
// Entity
class User {
    private Long id;
    private String username;
    private String password;
}
```

```java
// DTO
class UserDTO {
    private Long id;
    private String username;
}
```

The **Mapper** converts between them, ensuring `password` is never exposed.

---

## Manual Mapper Example

```java
public class UserMapper {

    public static UserDTO toDto(User user) {
        if (user == null) return null;

        UserDTO dto = new UserDTO();
        dto.setId(user.getId());
        dto.setUsername(user.getUsername());
        return dto;
    }

    public static User toEntity(UserDTO dto) {
        if (dto == null) return null;

        User user = new User();
        user.setId(dto.getId());
        user.setUsername(dto.getUsername());
        return user;
    }
}
```

✅ **Pros**

- Full control
    
- No external dependencies
    

❌ **Cons**

- Boilerplate code
    
- Error-prone for large models
    

---

## Popular Mapping Libraries (Very Important for Interviews)

### 1️⃣ MapStruct (Most Preferred)

- Compile-time mapping
    
- Fast and type-safe
    
- No reflection
    

```java
@Mapper(
    componentModel = "spring",
    unmappedTargetPolicy = ReportingPolicy.IGNORE
)
public interface UserMapper {

    UserDTO toDto(User user);
    User toEntity(UserDTO dto);
}
```

---

## Explanation of `@Mapper(componentModel = "spring", unmappedTargetPolicy = ...)`

### `@Mapper`

Tells **MapStruct** to generate an implementation of this interface **at compile time**.

---

### `componentModel = "spring"`

> Makes the generated mapper a **Spring Bean**

MapStruct generates something equivalent to:

```java
@Component
public class UserMapperImpl implements UserMapper { }
```

✔ Can be injected with `@Autowired` / constructor injection  
✔ Testable  
✔ Spring-idiomatic

---

### `unmappedTargetPolicy`

Controls what MapStruct does **when a target field is not mapped**.

---

## All `ReportingPolicy` Options (Important)

```java
ReportingPolicy.IGNORE
ReportingPolicy.WARN
ReportingPolicy.ERROR
```

### 1️⃣ `ReportingPolicy.IGNORE`

```java
@Mapper(unmappedTargetPolicy = ReportingPolicy.IGNORE)
```

- Ignores unmapped target fields
    
- No warning, no error
    
- Fields remain `null` or default
    

✅ Useful for:

- Partial mappings
    
- Large entities
    
- Update requests
    

❌ Risk:

- Can hide missing required fields
    

---

### 2️⃣ `ReportingPolicy.WARN` (default)

```java
@Mapper(unmappedTargetPolicy = ReportingPolicy.WARN)
```

- Compilation succeeds
    
- Compiler **warns** about unmapped fields
    

✅ Balanced choice  
⚠️ Warnings are often ignored in large projects

---

### 3️⃣ `ReportingPolicy.ERROR` (strict / safest)

```java
@Mapper(unmappedTargetPolicy = ReportingPolicy.ERROR)
```

- Compilation **fails**
    
- Forces explicit mapping
    

✅ Best for:

- Core domain entities
    
- Critical data
    
- High-safety systems
    

⭐ **Recommended for production domain models**

---

## Best Practice (Interview Gold)

Prefer **explicit mapping over IGNORE**:

```java
@Mapper(componentModel = "spring")
public interface UserMapper {

    @Mapping(target = "password", ignore = true)
    UserDTO toDto(User user);
}
```

✔ Self-documenting  
✔ Safe  
✔ Compile-time protection

---

## ModelMapper (Alternative)

- Runtime mapping using reflection
    

```java
ModelMapper modelMapper = new ModelMapper();
UserDTO dto = modelMapper.map(user, UserDTO.class);
```

✅ Pros:

- Quick setup
    
- Minimal code
    

❌ Cons:

- Slower
    
- Runtime errors
    
- Harder to debug
    

---

## Best Practices (Interview Gold)

- Use **DTOs** for APIs, not entities
    
- Prefer **MapStruct** in enterprise applications
    
- Keep mappers **stateless**
    
- Place mappers in a dedicated `mapper` package
    
- Avoid business logic inside mappers
    

---

## Typical Interview Questions & Short Answers

**Q: Why not return entities directly?**  
👉 Tight coupling, security risks, poor API design.

**Q: MapStruct vs ModelMapper?**  
👉 MapStruct is compile-time, faster, safer. ModelMapper is runtime and slower.

**Q: Why `componentModel = "spring"`?**  
👉 To make the mapper injectable as a Spring bean.

**Q: When should you avoid `ReportingPolicy.IGNORE`?**  
👉 For core entities where missing fields are dangerous.

---

## One-Line Summary (Perfect Closing Answer)

> _A mapper in Java transforms objects between layers—typically entities and DTOs—to enforce clean architecture, security, and maintainability, with MapStruct providing fast and type-safe compile-time mapping._
