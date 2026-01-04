## 🔵 java.util.ArrayDeque — Behavior, Pitfalls, Failures, Fixes

> `ArrayDeque<E>` is a **resizable-array implementation of [[Deque]]**.  
> It is **not synchronized**, **does not allow nulls**, and is usually **faster than LinkedList**.

---

## 🔑 Core Identity (Must Know)

- Implements: [`Deque<E>`](Deque.md)
- Backed by: **circular array**
- Allows: FIFO + LIFO
- Disallows: `null`
- Thread-safe: ❌ No

---

## 1️⃣ Creation

### **Correct usage**
```java
Deque<Integer> d = new ArrayDeque<>();
```

### **Pitfalls**
- ❌ Using `Stack` instead
- ❌ Using `LinkedList` without need for node-level operations

---

## 2️⃣ Insertion Operations

### **Methods**
```java
addFirst(e) / offerFirst(e)
addLast(e)  / offerLast(e)
push(e)
```

### **Correct usage**
```java
d.offerLast(10);
d.offerFirst(5);
```

### **Pitfalls**
- ❌ Assuming capacity limit → capacity grows automatically
- ❌ Expecting insertion at arbitrary index → not supported

---

## 3️⃣ Removal Operations

### **Methods**
```java
removeFirst() / pollFirst()
removeLast()  / pollLast()
pop()
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

## 4️⃣ Inspection (Peek)

### **Methods**
```java
getFirst() / peekFirst()
getLast()  / peekLast()
```

### **Pitfalls**
- ❌ Using `getFirst()` without emptiness check
- ❌ Assuming inspection removes element

---

## 5️⃣ Stack Replacement (IMPORTANT)

### **Correct usage**
```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
stack.pop();
```

### **Why preferred over Stack**
- No synchronization overhead
- Cleaner API
- Better performance

---

## 6️⃣ `null` Elements (Hard Rule)

### **Failure example**
```java
d.add(null);
```

**Failure:** `NullPointerException`

### **Reason**
`null` is reserved as a **sentinel value** for `poll()` / `peek()`.

---

## 7️⃣ Iteration Pitfalls

### **Pitfalls**
- ❌ Modifying deque during iteration
- ❌ Assuming iteration order equals stack order

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

## 8️⃣ Performance Characteristics (Exam Favorite)

| Operation | Complexity |
|---------|------------|
| addFirst / addLast | O(1) amortized |
| removeFirst / removeLast | O(1) amortized |
| contains | O(n) |
| iteration | O(n) |

### **Pitfalls**
- ❌ Random access is O(1) → no indexing support
- ❌ Shrinks automatically on removal → capacity may remain larger

---

## 9️⃣ Comparison with LinkedList (Very Important)

| Aspect | ArrayDeque | LinkedList |
|-----|-----------|------------|
| Memory | Compact | Node-heavy |
| Cache locality | High | Low |
| Null allowed | ❌ No | ✅ Yes |
| Deque ops | Faster | Slower |

---

## 🔟 Thread-safety Misconception

### **Pitfall**
```java
Deque<Integer> d = new ArrayDeque<>();
```

❌ Not thread-safe

### **Correct alternatives**
```java
Deque<Integer> d = new ConcurrentLinkedDeque<>();
Deque<Integer> d = new LinkedBlockingDeque<>();
```

---

## 🚨 Conceptual Pitfalls

### ❌ ArrayDeque ≠ ArrayList
- No index access
- No random insertion

---

### ❌ ArrayDeque ≠ Queue only
- Supports both FIFO and LIFO
- Misuse causes logic bugs

---

## 🧠 Mental Model

- Circular buffer
- Head + tail pointers
- Grows dynamically
- Optimized for **ends**, not middle

---

## ✅ Golden Rule

> Use `ArrayDeque` when you need  
> **fast stack or queue behavior** without thread safety.

Avoid for:
- random access
- null elements
- concurrent mutation

---
