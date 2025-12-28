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
    
    - Avoid exposing sensitive entity fields (e.g., passwords)
        
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

The **Mapper** converts between them.

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
    

```java
@Mapper(componentModel = "spring")
public interface UserMapper {

    UserDTO toDto(User user);
    User toEntity(UserDTO dto);
}
```

✅ Pros:

- No reflection
    
- Excellent performance
    
- Compile-time errors
    

❌ Cons:

- Less flexible for dynamic mappings
    

---

### 2️⃣ ModelMapper

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

### 3️⃣ Dozer (Less Popular Now)

- XML or annotation-based
    
- Reflection-heavy
    
- Generally discouraged in modern projects
    

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
👉 Because it causes tight coupling, security risks, and poor API design.

**Q: MapStruct vs ModelMapper?**  
👉 MapStruct is compile-time, faster, safer. ModelMapper is runtime and slower.

**Q: Where should mapping logic live?**  
👉 In a dedicated mapper layer, not in services or controllers.

---

## One-Line Summary (Perfect Closing Answer)

> _A mapper in Java is used to transform objects between layers—typically entities and DTOs—to enforce clean architecture, security, and maintainability._
