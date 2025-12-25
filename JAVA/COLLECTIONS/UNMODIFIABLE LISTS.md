## **1) Using `Collections.unmodifiableList(...)`**

This wraps an existing list:

```java
List<String> modifiable = new ArrayList<>();
modifiable.add("A");
modifiable.add("B");

List<String> unmodifiable = Collections.unmodifiableList(modifiable);

unmodifiable.add("C"); // throws UnsupportedOperationException
```

Important:  
This is only a wrapper. If you change the underlying list, the wrapper changes as well.

---

## **2) Using `List.of(...)` (Java 9+)**

Creates an **immutable** list directly:

```java
List<String> list = List.of("A", "B", "C");
list.add("D"); // throws UnsupportedOperationException
```

Unlike the Collections wrapper, this list truly does not change because there is no backing modifiable list.

---

## **3) Using `Collections.emptyList()` or `Collections.singletonList(...)`**

Examples:

```java
List<String> empty = Collections.emptyList();
List<String> single = Collections.singletonList("X");
```

Both are unmodifiable.

---

# **Important Distinction**

**unmodifiable != immutable**

Unmodifiable list:  
You cannot change it through its API, but underlying data might still change if the source collection is modified.

Immutable list:  
No internal state can ever change.

`List.of(...)` produces immutable lists.

`Collections.unmodifiableList(...)` produces unmodifiable views.
