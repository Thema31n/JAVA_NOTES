## 1️⃣ Dependencies (Maven)

```xml
<dependencies>

  <!-- Hibernate -->
  <dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>5.6.15.Final</version>
  </dependency>

  <!-- Ehcache -->
  <dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>3.10.8</version>
  </dependency>

  <!-- Hibernate Ehcache integration -->
  <dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-ehcache</artifactId>
    <version>5.6.15.Final</version>
  </dependency>

  <!-- H2 DB (demo) -->
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.1.214</version>
  </dependency>

</dependencies>
```

---

## 2️⃣ Hibernate Configuration (`hibernate.cfg.xml`)

```xml
<hibernate-configuration>
  <session-factory>

    <!-- DB -->
    <property name="hibernate.dialect">
      org.hibernate.dialect.H2Dialect
    </property>

    <property name="hibernate.connection.driver_class">
      org.h2.Driver
    </property>

    <property name="hibernate.connection.url">
      jdbc:h2:mem:testdb
    </property>

    <property name="hibernate.hbm2ddl.auto">create</property>
    <property name="hibernate.show_sql">true</property>

    <!-- SECOND LEVEL CACHE -->
    <property name="hibernate.cache.use_second_level_cache">true</property>
    <property name="hibernate.cache.use_query_cache">true</property>

    <property name="hibernate.cache.region.factory_class">
      org.hibernate.cache.ehcache.EhCacheRegionFactory
    </property>

    <!-- Metrics -->
    <property name="hibernate.generate_statistics">true</property>

    <!-- Entity -->
    <mapping class="com.demo.Product"/>

  </session-factory>
</hibernate-configuration>
```

---

## 3️⃣ Ehcache Configuration (`ehcache.xml`)

```xml
<config xmlns="http://www.ehcache.org/v3">

  <cache alias="com.demo.Product">
    <resources>
      <heap unit="entries">100</heap>
    </resources>
  </cache>

  <cache alias="productQueries">
    <resources>
      <heap unit="entries">100</heap>
    </resources>
  </cache>

</config>
```

---

## 4️⃣ Entity with Second-Level Cache

```java
@Entity
@Table(name = "product")
@Cacheable
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class Product {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @Version
    private Long version;

    // getters & setters
}
```

✔ Enables **L2 cache**  
✔ Uses **READ_WRITE**  
✔ Uses **optimistic locking**

---

## 5️⃣ Bootstrap Hibernate

```java
public class HibernateUtil {

    private static final SessionFactory sessionFactory;

    static {
        sessionFactory = new Configuration()
                .configure()
                .buildSessionFactory();
    }

    public static SessionFactory getSessionFactory() {
        return sessionFactory;
    }
}
```

---

## 6️⃣ Insert Sample Data

```java
Session session = HibernateUtil.getSessionFactory().openSession();
session.beginTransaction();

Product p = new Product();
p.setName("Laptop");

session.save(p);
session.getTransaction().commit();
session.close();
```

---

## 7️⃣ Second-Level Cache DEMO

```java
// SESSION 1
Session s1 = HibernateUtil.getSessionFactory().openSession();
Product p1 = s1.get(Product.class, 1L);
s1.close();

// SESSION 2
Session s2 = HibernateUtil.getSessionFactory().openSession();
Product p2 = s2.get(Product.class, 1L);
s2.close();
```

### What happens?

|Call|Result|
|---|---|
|Session 1|SQL executed → DB|
|Session 2|**NO SQL** → L2 cache|

✔ Entity served from **Second-Level Cache**

---

## 8️⃣ Query Cache DEMO

```java
Session session = HibernateUtil.getSessionFactory().openSession();

Query<Product> query = session.createQuery(
    "from Product where name = :name",
    Product.class
);

query.setParameter("name", "Laptop");
query.setHint("org.hibernate.cacheable", true);
query.setHint("org.hibernate.cacheRegion", "productQueries");

List<Product> result1 = query.list();
List<Product> result2 = query.list();

session.close();
```

### What happens?

|Execution|Behavior|
|---|---|
|First|SQL → DB|
|Second|**NO SQL** → Query Cache + L2 Cache|

✔ Query cache stores **IDs**  
✔ L2 cache stores **entity state**

---

## 9️⃣ Verify Cache Hits (Statistics)

```java
Statistics stats =
    HibernateUtil.getSessionFactory()
        .getStatistics();

System.out.println("L2 Hit Count: " + stats.getSecondLevelCacheHitCount());
System.out.println("Query Cache Hit Count: " + stats.getQueryCacheHitCount());
```

---

## 🧠 What This Demo Shows

✔ First-Level Cache (Session scoped)  
✔ Second-Level Cache (shared)  
✔ Query Cache (IDs only)  
✔ Proper configuration  
✔ Real behavior (SQL on/off)

---

## ✅ Mental Model (Demo Summary)

```
Session.get() → L1 → L2 → DB
Query (cacheable) → Query Cache → L2 → DB
```
