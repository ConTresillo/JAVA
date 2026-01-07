# 🟠 `java.util.Deque` — Methods, Pitfalls, Failures, Fixes

> `Deque<E>` extends [`Queue<E>`](Queue.md) and represents a **double-ended [queue](Queue.md)**  
> (insertion + removal at both head and tail).

---

## 🔑 Core Design Rule (Critical)

Every Deque operation exists in **three semantic forms**:
1. **Exception-based**
2. **Special-value-based**
3. **Blocking** (only in `BlockingDeque`)

This note covers **non-blocking Deque only**.

---

## 1️⃣ Insertion at Head

### **Methods**
```java
void addFirst(E e);
boolean offerFirst(E e);
```

### **Correct usage**
```java
deque.offerFirst(10);
```

### **Pitfalls**
- ❌ Using `addFirst` on capacity-restricted deque
- ❌ Assuming both methods behave identically

### **Failure example**
```java
Deque<Integer> d = new ArrayDeque<>(1);
d.addFirst(1);
d.addFirst(2);
```

**Failure:** `IllegalStateException`

### **Correct alternative**
```java
if (!d.offerFirst(2)) {
    // handle full deque
}
```

---

## 2️⃣ Insertion at Tail

### **Methods**
```java
void addLast(E e);
boolean offerLast(E e);
```

### **Pitfalls**
- ❌ Assuming Deque tail == [[Queue]] tail always
- ❌ Forgetting capacity constraints

---

## 3️⃣ Removal from Head

### **Methods**
```java
E removeFirst();
E pollFirst();
```

### **Failure example**
```java
Deque<Integer> d = new ArrayDeque<>();
d.removeFirst();
```

**Failure:** `NoSuchElementException`

### **Correct alternative**
```java
Integer x = d.pollFirst(); // null if empty
```

---

## 4️⃣ Removal from Tail

### **Methods**
```java
E removeLast();
E pollLast();
```

### **Pitfalls**
- ❌ Expecting FIFO semantics
- ❌ Assuming empty-safe removal

---

## 5️⃣ Inspection (Peek Operations)

### **Methods**
```java
E getFirst();
E peekFirst();
E getLast();
E peekLast();
```

### **Failure example**
```java
d.getFirst();
```

**Failure:** `NoSuchElementException`

### **Correct alternative**
```java
d.peekFirst(); // null if empty
```

---

## 6️⃣ Stack Behavior (LIFO)

Deque can act as a **Stack replacement**.

### **Correct usage**
```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
stack.pop();
```

### **Pitfalls**
- ❌ Using legacy `Stack`
- ❌ Mixing stack and [[Queue]] semantics unintentionally

### **Mapping**
| Stack | Deque |
|-----|-------|
| `push` | `addFirst` |
| `pop` | `removeFirst` |
| `peek` | `peekFirst` |

---

## 7️⃣ `null` Elements

### **Rule**
Most Deque implementations **do NOT allow null**.

### **Failure example**
```java
Deque<Integer> d = new ArrayDeque<>();
d.add(null);
```

**Failure:** `NullPointerException`

### **Reason**
`null` is reserved as a **failure sentinel** for `poll`/`peek`.

---

## 8️⃣ Iteration Pitfalls

### **Pitfalls**
- ❌ Modifying deque while iterating
- ❌ Assuming iteration order reflects insertion order

### **Failure**
```java
for (int x : d) {
    d.pollFirst();
}
```

**Failure:** `ConcurrentModificationException`

### **Correct**
```java
while (!d.isEmpty()) {
    d.pollFirst();
}
```

---

## 9️⃣ Descending Iterator

### **Method**
```java
Iterator<E> descendingIterator();
```

### **Pitfalls**
- ❌ Thinking it reverses deque
- ❌ Assuming structural safety

---

## 🔟 Thread-safety Assumption

### **Pitfall**
```java
Deque<Integer> d = new ArrayDeque<>();
```

❌ Not thread-safe

### **Correct alternatives**
```java
Deque<Integer> d = new ConcurrentLinkedDeque<>();
```

---

## 🚨 Conceptual Pitfalls (Exam Traps)

### ❌ Deque ≠ [[Queue]]
- [[Queue]] → usually single-ended
- Deque → always double-ended

---

### ❌ Deque ≠ Stack
- Deque supports both FIFO and LIFO
- Stack is a restricted abstraction

---

### ❌ Iteration order ≠ removal order
Especially true for custom implementations.

---

## 🧠 Mental Model

- Deque = **generalized [[queue]]**
- Supports **FIFO + LIFO**
- Failure behavior is explicit via method choice
- Choose operations deliberately

---

## 📌 Method Pair Summary

| Operation | Exception | Special Value |
|--------|-----------|---------------|
| Add First | `addFirst` | `offerFirst` |
| Add Last | `addLast` | `offerLast` |
| Remove First | `removeFirst` | `pollFirst` |
| Remove Last | `removeLast` | `pollLast` |
| Inspect First | `getFirst` | `peekFirst` |
| Inspect Last | `getLast` | `peekLast` |

---

## ✅ Golden Rule

> If you need **both ends**, use `Deque`.  
> If you need **one end**, use [`Queue`](Queue.md).

Never mix semantics blindly.

---
