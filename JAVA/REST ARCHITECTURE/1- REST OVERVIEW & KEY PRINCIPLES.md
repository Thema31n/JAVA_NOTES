# 1️⃣ Definition of REST

### What is REST?

**REST (Representational State Transfer)** is an **architectural style** for designing **distributed systems**, especially web APIs.  
It defines a **set of constraints** that, when followed, lead to scalable, maintainable, and loosely coupled systems.

> **Important**: REST is **NOT** a protocol, framework, or standard.

### Why REST exists

Before REST:

- Tight coupling between client & server
    
- Hard to scale
    
- APIs were inconsistent
    

REST solves:

- Scalability
    
- Simplicity
    
- Evolvability
    
- Interoperability
    

### Real-world analogy

Think of REST like:

- A **restaurant menu**
    
- The menu defines _what you can do_
    
- The kitchen implementation can change without affecting customers
    

---

### Interview tip

If you say _“REST is an architectural style that uses HTTP”_, that’s **incorrect**.

Correct:

> “REST is an architectural style; HTTP is commonly used to implement RESTful systems.”

---

### Things to memorize

- REST = architectural style
    
- Focuses on **resources**, not actions
    
- Uses **representations** (JSON, XML, etc.)
    

---

# 2️⃣ REST API

### What is a REST API?

A **REST API** is an API that **follows REST constraints**.

Many APIs call themselves REST, but:

- If they violate constraints → **not RESTful**
    

### Example REST API

```http
GET /users/42
```

Response:

```json
{
  "id": 42,
  "name": "Ahmed",
  "email": "ahmed@email.com"
}
```

This:

- Exposes a **resource** (`users`)
    
- Uses a **representation** (JSON)
    
- Uses standard HTTP semantics
    

---

### Common misconception

❌ “Any API over HTTP is REST”  
✅ Only APIs that follow REST constraints are RESTful

---

### Interview tip

Say:

> “A REST API is an API that adheres to REST constraints such as statelessness, uniform interface, and cacheability.”

---

### Things to memorize

- REST API ≠ HTTP API
    
- REST APIs are **resource-oriented**
    

---

# 3️⃣ REST Principles / Constraints

REST has **6 constraints**.  
5 are mandatory, 1 is optional.

1. Client–Server
    
2. Stateless
    
3. Cacheable
    
4. Uniform Interface
    
5. Layered System
    
6. Code on Demand (optional)
    

---

# 4️⃣ Client–Server Communication

### Definition

The client and server are **separate concerns**:

- Client → UI / UX
    
- Server → Data & business logic
    

They communicate **only via requests & responses**.

### Example

- Mobile app (client)
    
- Backend API (server)
    
- Database (server-side concern only)
    

```http
GET /products
```

Client doesn’t care:

- Where data is stored
    
- How it’s processed
    

---

### Why it matters

- Independent evolution
    
- Better scalability
    
- Easier maintenance
    

---

### Interview tip

Say:

> “Client-server separation allows each side to evolve independently without breaking the other.”

---

### Things to memorize

- Client ≠ Server responsibilities
    
- Loose coupling is key
    

---

# 5️⃣ Stateless Communication

### Definition

Each request must contain **all the information** needed to process it.  
The server **does not store client state** between requests.

### Example

```http
GET /orders
Authorization: Bearer eyJhbGciOi...
```

Server does NOT remember:

- Who you are
    
- Your previous request
    

Everything is in:

- Headers
    
- URL
    
- Body
    

---

### What is NOT stateless

❌ Server session memory  
❌ Server remembers logged-in users

---

### Real-world analogy

ATM machine:

- You insert card + PIN every time
    
- ATM doesn’t remember you from last visit
    

---

### Pros

- Easy scaling
    
- No session replication
    
- Better reliability
    

### Cons

- Larger requests
    
- Client responsibility increases
    

---

### Interview tip

Common trick question:

> “JWT makes REST stateful?”

Answer:

> “No. JWT stores state on the client, not the server.”

---

### Things to memorize

- Server stores **resource state**, not **client state**
    
- Every request is independent
    

---

# 6️⃣ Caching

### Definition

Responses must define whether they are **cacheable** or not.

### Why caching matters

- Reduces latency
    
- Reduces server load
    
- Improves scalability
    

---

### Example

```http
GET /products
Cache-Control: max-age=3600
```

Browser or CDN:

- Can reuse response for 1 hour
    

---

### What should be cached?

- GET responses
    
- Public data
    
- Rarely changing data
    

### What should NOT be cached?

- User-specific data
    
- Financial data
    
- Authentication responses
    

---

### Interview tip

Mention:

- `Cache-Control`
    
- `ETag`
    
- `Last-Modified`
    

---

### Things to memorize

- Only **safe** operations should be cached
    
- Caching is a REST constraint, not optional
    

---

# 7️⃣ Uniform Interface (Most Important)

### Definition

A consistent way to interact with resources.

It has **4 sub-constraints**:

---

## a) Resource Identification

Resources are identified by **URIs**.

✅ `/users/42`  
❌ `/getUser?id=42`

---

## b) Manipulation via Representations

Client sends **representation** of resource.

```http
PUT /users/42
{
  "name": "New Name"
}
```

---

## c) Self-descriptive Messages

Each request/response explains itself:

- HTTP method
    
- Headers
    
- Media type
    

---

## d) HATEOAS (Advanced)

Server provides **links** to next actions.

```json
{
  "id": 42,
  "links": [
    { "rel": "orders", "href": "/users/42/orders" }
  ]
}
```

---

### Interview tip

Most APIs skip HATEOAS — say:

> “HATEOAS is part of REST but rarely implemented in practice.”

---

### Things to memorize

- Uniform interface reduces coupling
    
- URLs = nouns, not verbs
    

---

# 8️⃣ Layered System

### Definition

Client does **not know** whether it’s talking to:

- API
    
- Load balancer
    
- Cache
    
- Gateway
    

### Example

Client → CDN → API Gateway → Microservice → DB

Client only sees:

```http
GET /products
```

---

### Benefits

- Security
    
- Scalability
    
- Flexibility
    

---

### Interview tip

Say:

> “Layered systems enable proxies, gateways, and caches transparently.”

---

### Things to memorize

- Client sees only one layer
    
- Improves system evolution
    

---

# 9️⃣ Code on Demand (Optional)

### Definition

Server can send **executable code** to client.

### Example

- JavaScript sent to browser
    

```html
<script>
  alert("Hello")
</script>
```

---

### Why optional

- Reduces portability
    
- Security concerns
    

---

### Interview tip

Mention:

> “It’s the only optional REST constraint.”

---

### Things to memorize

- Rare in APIs
    
- Common in web apps
    

---

# 🔟 Resource

### Definition

A **resource** is any meaningful entity:

- User
    
- Order
    
- Product
    

### Examples

- `/users`
    
- `/orders/99`
    
- `/products/10/reviews`
    

---

### Key idea

Resource ≠ database table  
Resource = **concept**

---

### Things to memorize

- Resources are nouns
    
- Identified by URIs
    

---

# 1️⃣1️⃣ Resource Method

### What is it?

Operations performed on resources using HTTP methods.

|Method|Meaning|
|---|---|
|GET|Read|
|POST|Create|
|PUT|Replace|
|PATCH|Partial update|
|DELETE|Remove|

---

### Example

```http
POST /orders
```

Creates new order

---

### Interview tip

Say:

> “HTTP methods define semantics, not actions.”

---

### Things to memorize

- GET is safe
    
- PUT is idempotent
    
- POST is not idempotent
    

---

# 1️⃣2️⃣ REST vs HTTP

### REST

- Architectural style
    
- Defines constraints
    
- Conceptual
    

### HTTP

- Protocol
    
- Defines transport rules
    
- Concrete
    

---

### Relationship

- REST can use HTTP
    
- REST can use other protocols
    
- HTTP ≠ REST
    

---

### Interview killer answer

> “HTTP is a protocol; REST is an architectural style that often uses HTTP as its implementation.”

---

### Things to memorize

- REST ≠ HTTP
    
- REST uses HTTP semantics properly
    

---

## ✅ Final Interview Summary (Memorize This)

- REST is an architectural style
    
- REST APIs are resource-based
    
- Statelessness is mandatory
    
- Uniform interface is the core constraint
    
- Not all HTTP APIs are RESTful
    
- REST improves scalability and maintainability
    
