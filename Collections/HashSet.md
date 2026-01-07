# 🟠 `java.util.HashSet` — Methods, Pitfalls, Fixes

> `HashSet<E>` is a **hash-table–backed, unordered set** that stores **unique elements** based on `equals()` and `hashCode()`.

---

## 1️⃣ `HashSet()`

### **Method**

```java
public HashSet()
```

### **Correct usage**

```java
Set<Integer> s = new HashSet<>();
```

### **Common pitfalls**

- ❌ Assuming insertion order is preserved
    
- ❌ Expecting sorted behavior
    
- ❌ Thinking capacity grows exactly by element count
    

### **Failure example**

```java
Set<Integer> s = new HashSet<>();
s.add(3);
s.add(1);
s.add(2);
System.out.println(s); // not guaranteed [1,2,3]
```

### **Correct alternative**

```java
Set<Integer> s = new LinkedHashSet<>(); // preserves insertion order
```

---

## 2️⃣ `HashSet(Collection<? extends E> c)`

### **Method**

```java
public HashSet(Collection<? extends E> c)
```

### **Correct usage**

```java
List<Integer> list = List.of(1, 2, 2, 3);
Set<Integer> s = new HashSet<>(list);
```

### **Common pitfalls**

- ❌ Expecting duplicates to remain
    
- ❌ Assuming order from collection is preserved
    

### **Failure example**

```java
List<Integer> list = List.of(1, 1, 1);
Set<Integer> s = new HashSet<>(list);
System.out.println(s.size()); // 1
```

### **Correct alternative**

```java
// Use List if duplicates matter
```

---

## 3️⃣ `boolean add(E e)`

### **Method**

```java
public boolean add(E e)
```

### **Correct usage**

```java
Set<String> s = new HashSet<>();
s.add("A");
```

### **Common pitfalls**

- ❌ Ignoring return value
    
- ❌ Assuming duplicates overwrite
    
- ❌ Using mutable keys
    

### **Failure example**

```java
s.add("A");
s.add("A"); // silently ignored
```

**Failure:** logical error (no insertion)

### **Correct alternative**

```java
if (!s.add("A")) {
    // duplicate detected
}
```

---

## 4️⃣ `boolean remove(Object o)`

### **Method**

```java
public boolean remove(Object o)
```

### **Correct usage**

```java
s.remove("A");
```

### **Common pitfalls**

- ❌ Removing while iterating without iterator
    
- ❌ Assuming exception on missing element
    

### **Failure example**

```java
for (String x : s) {
    s.remove(x); // ConcurrentModificationException
}
```

### **Correct alternative**

```java
Iterator<String> it = s.iterator();
while (it.hasNext()) {
    it.next();
    it.remove();
}
```

---

## 5️⃣ `boolean contains(Object o)`

### **Method**

```java
public boolean contains(Object o)
```

### **Correct usage**

```java
if (s.contains("A")) { }
```

### **Common pitfalls**

- ❌ Assuming reference equality
    
- ❌ Broken `equals()` / `hashCode()`
    

### **Failure example**

```java
class P { int x; }
Set<P> s = new HashSet<>();
s.add(new P());
s.contains(new P()); // false
```

### **Correct alternative**

```java
// Override equals() and hashCode()
```

---

## 6️⃣ `int size()`

### **Method**

```java
public int size()
```

### **Correct usage**

```java
int n = s.size();
```

### **Common pitfalls**

- ❌ Assuming capacity
    
- ❌ Using as performance metric
    

### **Failure example**

```java
// size() != internal bucket count
```

### **Correct alternative**

```java
// size() == number of unique elements
```

---

## 7️⃣ `boolean isEmpty()`

### **Method**

```java
public boolean isEmpty()
```

### **Correct usage**

```java
if (s.isEmpty()) { }
```

### **Common pitfalls**

- ❌ Checking size == 0 repeatedly
    

### **Failure example**

```java
if (s.size() == 0) { } // works but noisier
```

### **Correct alternative**

```java
if (s.isEmpty()) { }
```

---

## 8️⃣ `void clear()`

### **Method**

```java
public void clear()
```

### **Correct usage**

```java
s.clear();
```

### **Common pitfalls**

- ❌ Assuming capacity reset
    
- ❌ Expecting old iterators to work
    

### **Failure example**

```java
Iterator<String> it = s.iterator();
s.clear();
it.next(); // ConcurrentModificationException
```

### **Correct alternative**

```java
// Discard iterators after clear()
```

---

## 9️⃣ `Iterator<E> iterator()`

### **Method**

```java
public Iterator<E> iterator()
```

### **Correct usage**

```java
Iterator<String> it = s.iterator();
```

### **Common pitfalls**

- ❌ Structural modification during iteration
    
- ❌ Assuming order
    

### **Failure example**

```java
Iterator<String> it = s.iterator();
s.add("X");
it.next(); // ConcurrentModificationException
```

### **Correct alternative**

```java
// Modify only via it.remove()
```

---

## 🔟 `Object[] toArray()`

### **Method**

```java
public Object[] toArray()
```

### **Correct usage**

```java
Object[] arr = s.toArray();
```

### **Common pitfalls**

- ❌ Assuming typed array
    

### **Failure example**

```java
String[] a = (String[]) s.toArray(); // ClassCastException
```

### **Correct alternative**

```java
String[] a = s.toArray(new String[0]);
```

---

## 1️⃣1️⃣ `boolean addAll(Collection<? extends E> c)`

### **Method**

```java
public boolean addAll(Collection<? extends E> c)
```

### **Correct usage**

```java
s.addAll(List.of(1, 2, 3));
```

### **Common pitfalls**

- ❌ Expecting duplicates to increase size
    

### **Failure example**

```java
s.addAll(List.of(1, 1, 1));
```

### **Correct alternative**

```java
// Size increases only for new unique elements
```

---

## 1️⃣2️⃣ `boolean retainAll(Collection<?> c)`

### **Method**

```java
public boolean retainAll(Collection<?> c)
```

### **Correct usage**

```java
s.retainAll(List.of(1, 2));
```

### **Common pitfalls**

- ❌ Assuming it removes from `c`
    

### **Failure example**

```java
c.retainAll(s); // wrong direction
```

### **Correct alternative**

```java
s.retainAll(c);
```

---

## 1️⃣3️⃣ `boolean removeAll(Collection<?> c)`

### **Method**

```java
public boolean removeAll(Collection<?> c)
```

### **Correct usage**

```java
s.removeAll(List.of(1, 2));
```

### **Common pitfalls**

- ❌ Confusing with retainAll
    

### **Failure example**

```java
s.removeAll(s); // clears set
```

### **Correct alternative**

```java
// Use clear() if intent is full removal
```

---

## 🚨 Conceptual Pitfalls (Very Important)

- ❌ **HashSet vs ArrayList**
    

```java
list.contains(x); // O(n)
set.contains(x);  // O(1) average
```

✅ Use `HashSet` for membership checks.

- ❌ **HashSet vs TreeSet**
    

```java
new HashSet<>(list); // unordered
new TreeSet<>(list); // sorted
```

✅ Sorting is NOT a HashSet feature.

- ❌ **Mutable keys**
    

```java
set.add(obj);
obj.field = 10;
set.contains(obj); // may fail
```

✅ Keys must be effectively immutable.

---

## 🧠 Mental Model (Exam + Design)

- Exists to provide **fast membership tests**
    
- Guarantees **uniqueness**, not order
    
- Relies entirely on `hashCode()` + `equals()`
    
- Iteration order is **intentionally undefined**
    
- Rules live in `Set` interface; behavior in `HashSet` implementation
    

---

## 📌 Summary Table

|Method|Purpose|Common Pitfall|
|---|---|---|
|add|Insert element|Ignoring duplicate return|
|remove|Delete element|Modifying during iteration|
|contains|Membership test|Broken equals/hashCode|
|iterator|Traverse set|Assuming order|
|toArray|Export elements|Wrong casting|
|retainAll|Intersection|Direction confusion|

---

## ✅ Golden Rule

HashSet enforces **uniqueness, not order**.  
Correctness depends on **stable equals/hashCode**.  
If order or sorting matters, **HashSet is the wrong tool**.