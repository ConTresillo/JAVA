# 🟠 `java.lang.Iterable` — Methods, Pitfalls, Fixes

> `Iterable<T>` is the **root interface** that enables the enhanced `for-each` loop.

---

## 1️⃣ `iterator()`

### **Method**
```java
Iterator<T> iterator();
```

### **Correct usage**
```java
for (T x : iterable) {
    // uses iterator() internally
}
```

### **Common pitfalls**
- ❌ Assuming `iterator()` returns a new independent iterator every time  
- ❌ Modifying collection directly while iterating
- ❌ Calling `next()` without checking `hasNext()`

### **Failure example**
```java
Iterator<Integer> it = list.iterator();
list.add(10);          // structural modification
it.next();             // may throw ConcurrentModificationException
```

### **Correct alternative**
```java
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    int x = it.next();
    if (x == 5) it.remove();   // legal removal
}
```

---

## 2️⃣ `forEach(Consumer<? super T> action)` (default)

### **Method**
```java
default void forEach(Consumer<? super T> action)
```

### **Correct usage**
```java
iterable.forEach(x -> System.out.println(x));
```

### **Common pitfalls**
- ❌ Modifying collection inside `forEach`
- ❌ Expecting checked exceptions to propagate cleanly
- ❌ Assuming execution order for all implementations

### **Failure example**
```java
iterable.forEach(x -> {
    if (x == 3) iterable.remove(x); // invalid
});
```

**Failure:** `ConcurrentModificationException`

### **Correct alternative**
```java
Iterator<T> it = iterable.iterator();
while (it.hasNext()) {
    T x = it.next();
    if (condition(x)) it.remove();
}
```

---

## 3️⃣ `spliterator()` (default)

### **Method**
```java
default Spliterator<T> spliterator()
```

### **Correct usage**
```java
Spliterator<T> sp = iterable.spliterator();
sp.forEachRemaining(System.out::println);
```

### **Common pitfalls**
- ❌ Assuming parallelism automatically
- ❌ Assuming characteristics (ORDERED, SORTED, SIZED)
- ❌ Using for mutation-heavy logic

### **Failure example**
```java
iterable.spliterator().trySplit().forEachRemaining(...);
```

**Failure:** `trySplit()` may return `null`

### **Correct alternative**
```java
Spliterator<T> sp = iterable.spliterator();
if (sp.trySplit() != null) {
    // safe to split
}
```

---

## 🚨 Conceptual Pitfalls (Very Important)

### ❌ `Iterable` ≠ `Collection`
- `Iterable` has **NO size**
- `Iterable` has **NO add/remove**
- `Iterable` has **NO stream()**

```java
Iterable<Integer> it = list;
it.size();   // ❌ compile-time error
```

✅ Correct:
```java
Collection<Integer> c = list;
c.size();
```

---

### ❌ Assuming reusability
Some `Iterable`s are **single-use** (e.g., IO-backed).

```java
Iterable<String> lines = Files.lines(path)::iterator;
for (String s : lines) {}   // OK
for (String s : lines) {}   // ❌ may fail or be empty
```

---

### ❌ Assuming thread-safety
`Iterable` provides **NO concurrency guarantees**.

```java
for (T x : iterable) {
    // unsafe if another thread mutates collection
}
```

---

## 🧠 Mental Model (Exam + Design)

- `Iterable` exists **only** to support iteration
- It is the **minimum contract** for `for-each`
- Mutation rules depend on the **Iterator**, not Iterable
- All safety rules live in the **implementation**, not interface

---

## 📌 Summary Table

| Method        | Purpose                     | Common Failure |
|---------------|-----------------------------|----------------|
| `iterator()`  | Core traversal              | CME, misuse of `next()` |
| `forEach()`   | Lambda-based iteration      | Structural modification |
| `spliterator()` | Bulk / parallel traversal | Wrong assumptions |

---

## ✅ Golden Rule

> If you only know something is `Iterable`,  
> **iterate — do nothing else.**

No size.  
No mutation.  
No assumptions.

---

