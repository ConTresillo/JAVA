# 🟠 `java.util.Queue` — Methods, Pitfalls, Failures, Fixes

> `Queue<E>` extends [`Collection<E>`](Collections.md) and represents a **FIFO (typically) ordered collection** designed for **holding elements prior to processing**.

---

## 🔑 Core Design Rule (Very Important)

Queue methods come in **pairs**:
- one **throws exception on failure**
- one **returns special value on failure**

This distinction is the #1 exam + interview trap.

---

## 1️⃣ `add(E e)` vs `offer(E e)`

### **Methods**
```java
boolean add(E e);
boolean offer(E e);
```

### **Correct usage**
```java
queue.offer(10);
```

### **Pitfalls**
- ❌ Assuming both behave the same
- ❌ Using `add()` on capacity-restricted queues

### **Failure example**
```java
Queue<Integer> q = new ArrayBlockingQueue<>(1);
q.add(1);
q.add(2);
```

**Failure:** `IllegalStateException`

### **Correct alternative**
```java
if (!q.offer(2)) {
    // handle queue full
}
```

---

## 2️⃣ `remove()` vs `poll()`

### **Methods**
```java
E remove();
E poll();
```

### **Correct usage**
```java
Integer x = queue.poll();
```

### **Pitfalls**
- ❌ Using `remove()` on empty queue
- ❌ Expecting `null` from `remove()`

### **Failure example**
```java
Queue<Integer> q = new LinkedList<>();
q.remove();
```

**Failure:** `NoSuchElementException`

### **Correct alternative**
```java
Integer x = q.poll(); // returns null if empty
```

---

## 3️⃣ `element()` vs `peek()`

### **Methods**
```java
E element();
E peek();
```

### **Correct usage**
```java
Integer x = queue.peek();
```

### **Pitfalls**
- ❌ Using `element()` without emptiness check
- ❌ Assuming removal happens

### **Failure example**
```java
Queue<Integer> q = new LinkedList<>();
q.element();
```

**Failure:** `NoSuchElementException`

### **Correct alternative**
```java
Integer x = q.peek(); // null if empty
```

---

## 4️⃣ Iteration over Queue

### **Correct usage**
```java
for (Integer x : queue) {
    // traversal only
}
```

### **Pitfalls**
- ❌ Assuming iteration order equals removal order
- ❌ Modifying queue during iteration

### **Failure**
```java
for (Integer x : queue) {
    queue.poll();
}
```

**Failure:** `ConcurrentModificationException`

### **Correct alternative**
```java
while (!queue.isEmpty()) {
    queue.poll();
}
```

---

## 5️⃣ `null` elements

### **Rule**
Most Queue implementations **do NOT allow null elements**.

### **Failure example**
```java
Queue<Integer> q = new ArrayDeque<>();
q.offer(null);
```

**Failure:** `NullPointerException`

### **Correct understanding**
- `null` is reserved as a **sentinel return value**
- Allows distinguishing between *empty* and *value*

---

## 6️⃣ Ordering misconceptions

### **Pitfalls**
- ❌ Queue always FIFO → not always
- ❌ Iteration order equals priority order

### **Example**
```java
Queue<Integer> q = new PriorityQueue<>();
```

- Removal order → priority-based
- Iteration order → unspecified

---

## 7️⃣ Thread-safety assumption

### **Pitfall**
```java
Queue<Integer> q = new LinkedList<>();
```

❌ Not thread-safe

### **Correct alternatives**
```java
Queue<Integer> q = new ConcurrentLinkedQueue<>();
Queue<Integer> q = new ArrayBlockingQueue<>(10);
```

---

## 🚨 Conceptual Pitfalls (Exam Favorites)

### ❌ Queue ≠ Deque
- Queue: single-ended access (conceptually)
- Deque: double-ended access

---

### ❌ Queue ≠ List
- No index-based access
- No positional insertion

```java
queue.get(0); // ❌ compile-time error
```

---

### ❌ Assuming capacity is infinite
Some queues are **bounded**.

---

## 🧠 Mental Model

- Queue = **buffer between producer and consumer**
- Failure handling is **API-level**, not exception-only
- Choose method based on **failure semantics**
- Order depends on **implementation**

---

## 📌 Method Summary Table

| Operation | Throws Exception | Returns Special Value |
|---------|-----------------|-----------------------|
| Insert  | `add()`         | `offer()`             |
| Remove  | `remove()`      | `poll()`              |
| Inspect | `element()`    | `peek()`              |

---

## ✅ Golden Rule

> In Queue, **never guess failure behavior**.  
> Choose methods deliberately.

Exceptions → control-flow heavy  
Special values → flow-safe

---
