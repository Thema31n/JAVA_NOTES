# 1️⃣ Configuration (Spring Boot + JPA)

### application.yml

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/ecommerce
    username: ecommerce_user
    password: secret

  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```

📌 **Production note**  
DDL handled by Flyway/Liquibase, not Hibernate.

---

# 2️⃣ Domain Model (Entities)

## 2.1 User Entity

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String email;

    private String name;
}
```

---

## 2.2 Order Entity (Relationship + Enum)

```java
@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

    private BigDecimal totalAmount;

    private LocalDateTime createdAt;
}
```

```java
public enum OrderStatus {
    NEW, PAID, SHIPPED, CANCELLED
}
```

📌 **Why LAZY?**

- Prevents loading User unless needed
    
- Avoids performance issues
    

---

# 3️⃣ Repository Layer (MOST IMPORTANT PART)

## 3.1 Basic Repository

```java
public interface OrderRepository
        extends JpaRepository<Order, Long> {
}
```

---

## 3.2 Method Naming Queries (CRUD + Business)

### Find orders by status

```java
List<Order> findByStatus(OrderStatus status);
```

### Check existence (common in validation)

```java
boolean existsByIdAndStatus(Long id, OrderStatus status);
```

### Count for dashboard

```java
long countByStatus(OrderStatus status);
```

📌 **Used in real dashboards & validations**

---

## 3.3 Pagination & Sorting (Enterprise MUST)

```java
Page<Order> findByStatus(
    OrderStatus status,
    Pageable pageable
);
```

Used for:

- Admin panels
    
- REST list endpoints
    

---

## 3.4 JPQL Query (Entity-Based Logic)

### Fetch orders for a user

```java
@Query("""
   SELECT o
   FROM Order o
   WHERE o.user.email = :email
   AND o.status = :status
""")
List<Order> findUserOrders(
    @Param("email") String email,
    @Param("status") OrderStatus status
);
```

📌 JPQL joins through **object graph**, not SQL joins.

---

## 3.5 JPQL Projection (Performance Optimization)

### Interface Projection

```java
public interface OrderSummary {
    Long getId();
    BigDecimal getTotalAmount();
    OrderStatus getStatus();
}
```

```java
@Query("""
   SELECT o.id AS id,
          o.totalAmount AS totalAmount,
          o.status AS status
   FROM Order o
   WHERE o.status = :status
""")
List<OrderSummary> findOrderSummaries(
    @Param("status") OrderStatus status
);
```

✔ Avoids loading full entities  
✔ Very common in real APIs

---

## 3.6 Native SQL Query (Reporting Case)

### Monthly revenue report

```java
@Query(
  value = """
     SELECT DATE_TRUNC('month', created_at) AS month,
            SUM(total_amount) AS revenue
     FROM orders
     WHERE status = 'PAID'
     GROUP BY month
     ORDER BY month
  """,
  nativeQuery = true
)
List<Object[]> findMonthlyRevenue();
```

📌 **Typical use case**

- Finance reports
    
- Analytics
    
- Heavy aggregation
    

---

# 4️⃣ Service Layer (Business Logic + Transactions)

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;

    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    @Transactional
    public Order placeOrder(Order order) {
        order.setStatus(OrderStatus.NEW);
        order.setCreatedAt(LocalDateTime.now());
        return orderRepository.save(order);
    }

    @Transactional(readOnly = true)
    public Page<Order> getOrders(
            OrderStatus status,
            int page,
            int size
    ) {
        Pageable pageable = PageRequest.of(
            page, size, Sort.by("createdAt").descending()
        );
        return orderRepository.findByStatus(status, pageable);
    }
}
```

📌 **Why @Transactional here?**

- Service layer defines transaction boundaries
    
- Repository should stay thin
    

---

# 5️⃣ REST Layer (End-User API)

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @GetMapping
    public Page<Order> getOrders(
        @RequestParam OrderStatus status,
        @RequestParam int page,
        @RequestParam int size
    ) {
        return orderService.getOrders(status, page, size);
    }
}
```

📌 **Real-world note**

- In production → return DTOs, not entities
    

---

# 6️⃣ Request Flow (MEMORIZE FOR INTERVIEWS)

```
HTTP Request
 → Controller
 → Service (@Transactional)
 → Repository
 → EntityManager
 → Hibernate
 → Database
```

---

# 7️⃣ What This Example Demonstrates (IMPORTANT)

✔ Spring Boot auto-config  
✔ Entities & relationships  
✔ Repository abstraction  
✔ Method naming queries  
✔ JPQL  
✔ Native SQL  
✔ Pagination & sorting  
✔ Projections  
✔ Transactions  
✔ REST integration

This is **90% of real Spring Data JPA usage**.

---

# 8️⃣ Common Real-World Mistakes (DO NOT DO)

❌ Business logic in repositories  
❌ Returning entities directly to clients  
❌ No pagination  
❌ Overusing native SQL  
❌ Ignoring LAZY loading  
❌ No projections for large reads

---

# 9️⃣ Interview Questions Based on THIS Demo

### Q: Why projections?

**Answer:**  
To reduce memory usage and improve query performance.

---

### Q: JPQL vs Native SQL?

**Answer:**  
JPQL is portable and entity-based, native SQL is DB-specific and used for reporting.

---

### Q: Why transactions in service layer?

**Answer:**  
To keep repositories reusable and control transactional boundaries.

---

# 🔟 Things to Memorize (FINAL)

- Repositories are runtime proxies
    
- JPQL works on entities, not tables
    
- Native SQL breaks portability
    
- Pagination is mandatory for large datasets
    
- Transactions belong in service layer
    
- Projections are performance tools
    
- Spring Data JPA trades control for productivity
    
