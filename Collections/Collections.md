# 🟠 `java.util.Collection` — Methods, Pitfalls, Failures, Fixes

> `Collection<E>` extends [`Iterable<E>`](Iterable.md) and represents a **mutable group of elements**.

---

## 1️⃣ `add(E e)`

### **Method**
```java
boolean add(E e);
```

### **Correct usage**
```java
collection.add(10);
```

### **Common pitfalls**
- ❌ Assuming it always returns `true`
- ❌ Assuming duplicates are allowed
- ❌ Assuming ordering is preserved

### **Failure / surprise**
```java
Set<Integer> set = new HashSet<>();
set.add(1);
set.add(1);
```

**Result:** second `add` returns `false`

### **Correct understanding**
- `Set` may reject duplicates
- `List` always allows duplicates

---

## 2️⃣ `remove(Object o)`

### **Method**
```java
boolean remove(Object o);
```

### **Correct usage**
```java
collection.remove(10);
```

### **Common pitfalls**
- ❌ Confusing index vs object removal
- ❌ Removing during enhanced for-loop

### **Failure example**
```java
for (int x : list) {
    if (x == 2) list.remove(x);
}
```

**Failure:** `ConcurrentModificationException`

### **Correct alternative**
```java
Iterator<Integer> it = list.iterator();
while (it.hasNext()) {
    if (it.next() == 2) it.remove();
}
```

---

## 3️⃣ `addAll(Collection<? extends E> c)`

### **Method**
```java
boolean addAll(Collection<? extends E> c);
```

### **Pitfalls**
- ❌ Assuming atomicity
- ❌ Assuming partial success impossible

### **Failure scenario**
```java
collection.addAll(null);
```

**Failure:** `NullPointerException`

---

## 4️⃣ `removeAll(Collection<?> c)`

### **Method**
```java
boolean removeAll(Collection<?> c);
```

### **Pitfalls**
- ❌ Assuming only one occurrence removed
- ❌ Expecting structural safety during iteration

### **Correct usage**
```java
collection.removeAll(List.of(1, 2));
```

---

## 5️⃣ `retainAll(Collection<?> c)`

### **Method**
```java
boolean retainAll(Collection<?> c);
```

### **Pitfalls**
- ❌ Thinking it keeps elements *not* in `c`
- ❌ Forgetting it mutates the collection

### **Mental model**
> Keeps intersection, removes everything else.

---

## 6️⃣ `contains(Object o)`

### **Method**
```java
boolean contains(Object o);
```

### **Pitfalls**
- ❌ Assuming reference comparison
- ❌ Broken `equals()` causes false negatives

### **Failure example**
```java
class A {
    int x;
}
collection.contains(new A()); // false
```

**Reason:** `equals()` not overridden

---

## 7️⃣ `containsAll(Collection<?> c)`

### **Pitfalls**
- ❌ Assuming subset equality
- ❌ Assuming order matters

```java
list.containsAll(List.of(2,1)); // true if elements exist
```

---

## 8️⃣ `size()`

### **Method**
```java
int size();
```

### **Pitfalls**
- ❌ Assuming O(1)
- ❌ Calling repeatedly inside loops

### **Bad**
```java
for (int i = 0; i < collection.size(); i++) { }
```

### **Better**
```java
int n = collection.size();
```

---

## 9️⃣ `isEmpty()`

### **Method**
```java
boolean isEmpty();
```

### **Pitfalls**
- ❌ Using `size() == 0` instead
- ❌ Assuming faster or slower arbitrarily

---

## 🔟 `clear()`

### **Method**
```java
void clear();
```

### **Pitfalls**
- ❌ Expecting capacity to shrink
- ❌ Assuming references cleared elsewhere

---

## 1️⃣1️⃣ `toArray()`

### **Method**
```java
Object[] toArray();
<T> T[] toArray(T[] a);
```

### **Failure example**
```java
Integer[] arr = (Integer[]) collection.toArray();
```

**Failure:** `ClassCastException`

### **Correct alternative**
```java
Integer[] arr = collection.toArray(new Integer[0]);
```

---

## 1️⃣2️⃣ `iterator()`

### **Inherited from [[Iterable]]**

### **Pitfalls**
- ❌ Multiple iterators stay consistent
- ❌ Modification outside iterator allowed

---

## 1️⃣3️⃣ `stream()` / `parallelStream()` (default)

### **Pitfalls**
- ❌ Streams modify source safely
- ❌ Parallel streams always faster

```java
collection.parallelStream().forEach(...); // order not guaranteed
```

---

## 🚨 Major Conceptual Pitfalls

### ❌ `Collection` ≠ `List`
- No indexing
- No positional access

```java
collection.get(0); // ❌ compile-time error
```

---

### ❌ `Collection` ≠ `Set`
- Duplicate rules vary
- Ordering rules vary

---

### ❌ Thread-safety assumption
```java
Collection<Integer> c = new ArrayList<>();
// Not thread-safe
```

---

## 🧠 Mental Model (Very Important)

- `Collection` = **bag of elements**
- Behavior depends on **implementation**
- Mutations invalidate iterators
- `equals()` correctness is foundational

---

## 📌 Summary Table

| Method | Common Failure |
|------|----------------|
| `add` | Duplicate assumptions |
| `remove` | CME during iteration |
| `contains` | Broken equals |
| `toArray` | ClassCastException |
| `stream` | Order & mutation issues |

---

## ✅ Golden Rule

> When working with `Collection`,  
> **know the concrete type or assume nothing.**

Order? maybe.  
Duplicates? maybe.  
Performance? depends.

---
