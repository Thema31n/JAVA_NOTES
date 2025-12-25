# 1️⃣ Why It’s Important to Follow REST Guidelines

### Why guidelines exist

REST guidelines are not cosmetic. They:

- Improve **developer experience**
    
- Reduce **learning time**
    
- Prevent **breaking changes**
    
- Enable **API longevity**
    

### Real-world impact

Bad APIs cause:

- Client bugs
    
- Misuse of endpoints
    
- Costly refactors
    

Good APIs:

- Are self-explanatory
    
- Require less documentation
    
- Scale across teams
    

---

### Interview tip

Say:

> “REST guidelines optimize for clarity, consistency, and long-term maintainability, not just correctness.”

---

### Things to memorize

- APIs are **contracts**
    
- Breaking contracts breaks clients
    

---

# 2️⃣ Use Nouns to Represent Resources (Not Verbs)

### Rule

Endpoints represent **resources**, not actions.

❌ Bad

```
/getUsers
/createOrder
/updateProfile
```

✅ Good

```
/users
/orders
/profiles
```

### Why

- HTTP methods already represent actions
    
- Verbs in URLs duplicate semantics
    

### Correct usage

```http
GET /users
POST /users
PUT /users/42
DELETE /users/42
```

---

### Interview tip

Say:

> “Actions belong to HTTP methods, not URLs.”

---

### Things to memorize

- URLs = nouns
    
- Methods = verbs
    

---

# 3️⃣ Use Pluralized Nouns for Resources

### Rule

Collections should always be **plural**.

❌ Bad

```
/user
/order
```

✅ Good

```
/users
/orders
```

### Why

- Consistency
    
- Avoid ambiguity between collection and single resource
    

### Example

```http
GET /users        # collection
GET /users/42     # single resource
```

---

### Interview tip

If asked “Why plural?”

> “Pluralization avoids confusion and improves readability.”

---

### Things to memorize

- Collection endpoints are plural
    
- IDs specify singular instances
    

---

# 4️⃣ Use Hyphens and Lowercase

### Rule

- Use **lowercase**
    
- Use **hyphens (-)** instead of underscores or camelCase
    

❌ Bad

```
/UserProfiles
/user_profiles
/userProfiles
```

✅ Good

```
/user-profiles
```

### Why

- URLs are case-sensitive
    
- Hyphens are more readable
    
- Industry convention
    

---

### Real-world example

```
/credit-card-transactions
```

---

### Interview tip

Say:

> “Lowercase and hyphens avoid ambiguity and parsing issues.”

---

### Things to memorize

- No camelCase
    
- No underscores
    

---

# 5️⃣ Use Forward Slashes for Hierarchy (Not Trailing Slash)

### Rule

Use `/` to express **resource hierarchy**.

✅ Good

```
/users/42/orders
```

❌ Bad

```
/users/42/orders/
```

### Why avoid trailing slash

- Creates duplicate URLs
    
- Cache inconsistency
    
- SEO and routing issues
    

---

### Hierarchy meaning

```http
GET /users/42/orders
```

Means:

> Orders **belong to** user 42

---

### Interview tip

Say:

> “Hierarchy reflects resource relationships, not operations.”

---

### Things to memorize

- `/` = relationship
    
- No trailing `/`
    

---

# 6️⃣ Avoid Using File Extensions

### Rule

Do NOT include `.json`, `.xml`, etc.

❌ Bad

```
/users.json
/orders.xml
```

✅ Good

```
/users
/orders
```

### Why

- Format belongs in **headers**, not URLs
    
- Allows format evolution
    

### Correct way

```http
Accept: application/json
```

---

### Interview tip

Say:

> “Content negotiation is handled via headers, not URL extensions.”

---

### Things to memorize

- URLs identify resources
    
- Headers define representation
    

---

# 7️⃣ API Versioning

### Why versioning matters

APIs evolve:

- New fields
    
- Changed behavior
    
- Removed functionality
    

Versioning prevents **breaking clients**.

> **Rule:** Version **only when breaking changes occur**, not for every enhancement.

---

### Versioning Types

#### a) Version at the Beginning of the Resource Name (URI Versioning)

```
/api/v1/users
/api/v2/users
```

- Most common and practical
    
- Very explicit
    
- Easy routing and debugging
    

---

#### b) Version as an Additional Header

```http
X-API-Version: 1
```

- URL remains unchanged
    
- Less visible, harder to debug
    
- Requires strong documentation
    

---

#### c) Version in the Subdomain

```
v1.api.company.com/users
v2.api.company.com/users
```

- Clear separation
    
- Useful for large or legacy systems
    
- Adds operational complexity
    

---

#### d) Version as an Additional Field in the Accept / Content-Type Header

```http
Accept: application/json;version=1
Content-Type: application/json;version=1
```

- Uses standard HTTP headers
    
- Clean URLs
    
- Less readable and easy to misuse
    

---

#### e) Version as a Media Type (Media Type Versioning)

```http
Accept: application/vnd.company.v1+json
```

- Most REST-pure approach
    
- Enables content negotiation
    
- Rarely used due to complexity and tooling limitations
    

---

### Best practice

- **Most common in real world**: URI versioning
    
- **Most REST-compliant**: Media type versioning
    
- Version only when **breaking changes** are introduced
    

---

### Interview tip

Say:

> “URI versioning is the most practical, while media-type versioning is the most REST-compliant.”

---

### Things to memorize

- Versioning is about compatibility
    
- v1 should live long
    
- Avoid versioning via query parameters
    

---

# 8️⃣ Use Query Parameters Correctly

### Rule

Query parameters are for:

- Filtering
    
- Sorting
    
- Pagination
    
- Searching
    

❌ Bad

```
/users/active
/users/sortByName
```

✅ Good

```
/users?status=active
/users?sort=name
```

---

### Pagination example

```http
GET /users?page=2&limit=20
```

### Filtering example

```http
GET /orders?status=completed&date=2025-01-01
```

---

### Interview tip

Say:

> “Query parameters modify the representation, not the resource.”

---

### Things to memorize

- Queries ≠ resources
    
- No business logic in URLs
    

---

# 9️⃣ Punctuations for Lists (Comma-Separated Values)

### Rule

Use commas for lists.

❌ Bad

```
/users?id=1&id=2&id=3
```

✅ Good

```
/users?id=1,2,3
```

---

### Real-world example

```http
GET /products?category=books,electronics
```

---

### Interview tip

Mention:

> “Comma-separated values simplify parsing and readability.”

---

### Things to memorize

- Lists = commas
    
- Avoid repeated params
    

---

# 🔟 Intuitive Names (No Jargon)

### Rule

Names should be **business-friendly**, not technical.

❌ Bad

```
/cust-mgmt
/usr-svc
```

✅ Good

```
/customers
/users
```

---

### Why

- APIs are used by humans
    
- Business language survives longer
    

---

### Interview tip

Say:

> “Use domain language, not internal system jargon.”

---

### Things to memorize

- APIs are public contracts
    
- Clarity > cleverness
    

---

# 1️⃣1️⃣ No Abbreviations (No Abridging)

### Rule

Avoid shortening words.

❌ Bad

```
/acct
/txn
/usr
```

✅ Good

```
/accounts
/transactions
/users
```

---

### Why

- Ambiguity
    
- Onboarding difficulty
    
- Poor readability
    

---

### Interview tip

Say:

> “Explicit names reduce cognitive load.”

---

### Things to memorize

- Save keystrokes, lose clarity
    

---

# 1️⃣2️⃣ Avoid Special Characters

### Rule

Avoid:

- `@`
    
- `#`
    
- `$`
    
- `%`
    
- Spaces
    

✅ Allowed:

- Letters
    
- Numbers
    
- Hyphens
    

---

### Bad example

```
/user@profile
/order#details
```

---

### Interview tip

Say:

> “Special characters cause encoding and routing issues.”

---

### Things to memorize

- URLs should be predictable
    
- Simplicity wins
    

---

# 1️⃣3️⃣ Keep API Consistent (Most Critical Rule)

### Rule

Consistency across:

- Naming
    
- Pagination
    
- Error formats
    
- Versioning
    
- Status codes
    

---

### Bad consistency

```
/users
/customers
/getOrders
```

### Good consistency

```
/users
/users/{id}
 /users/{id}/orders
```

---

### Real-world example

If pagination is:

```
?page=1&limit=20
```

Use it **everywhere**.

---

### Interview tip (Strong Answer)

> “Consistency reduces documentation and prevents client-side bugs.”

---

### Things to memorize

- Inconsistency is technical debt
    
- APIs outlive implementations
    

---

## ✅ Final Memorization Checklist (Interview Gold)

- URLs are nouns
    
- Plural resource names
    
- Hyphens + lowercase
    
- No trailing slashes
    
- No file extensions
    
- Version only when breaking
    
- Queries for filtering, not resources
    
- No jargon, no abbreviations
    
- Consistency beats perfection
    
