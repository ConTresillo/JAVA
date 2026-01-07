# 🟠 `java.util.HashMap` — Methods, Pitfalls, Fixes

> `HashMap<K,V>` is a **hash-table–based, non-synchronized, unordered Map implementation** permitting **one null key** and **multiple null values**.

---

## 1️⃣ `put(K key, V value)`

### **Method**

```java
V put(K key, V value)
```

### **Correct usage**

```java
map.put("a", 1);
```

### **Common pitfalls**

- ❌ Assuming insertion order is preserved
    
- ❌ Assuming overwrite is rejected
    
- ❌ Expecting thread safety
    

### **Failure example**

```java
map.put("a", 1);
map.put("a", 2);
```

**Failure:** silently overwrites value

### **Correct alternative**

```java
map.putIfAbsent("a", 1);
```

---

## 2️⃣ `get(Object key)`

### **Method**

```java
V get(Object key)
```

### **Correct usage**

```java
Integer v = map.get("a");
```

### **Common pitfalls**

- ❌ Assuming key must exist
    
- ❌ Confusing `null` value vs missing key
    

### **Failure example**

```java
map.get("missing").toString();
```

**Failure:** `NullPointerException`

### **Correct alternative**

```java
map.getOrDefault("missing", 0);
```

---

## 3️⃣ `containsKey(Object key)`

### **Method**

```java
boolean containsKey(Object key)
```

### **Correct usage**

```java
if (map.containsKey(k)) { ... }
```

### **Common pitfalls**

- ❌ Using `get() != null` instead
    

### **Failure example**

```java
map.put("x", null);
map.get("x") != null;
```

**Failure:** false negative

### **Correct alternative**

```java
map.containsKey("x");
```

---

## 4️⃣ `containsValue(Object value)`

### **Method**

```java
boolean containsValue(Object value)
```

### **Correct usage**

```java
map.containsValue(10);
```

### **Common pitfalls**

- ❌ Assuming O(1) time
    

### **Failure example**

```java
// Used inside loop assuming fast lookup
```

**Failure:** O(n) scan

### **Correct alternative**

```java
// redesign: reverse map or use Set
```

---

## 5️⃣ `remove(Object key)`

### **Method**

```java
V remove(Object key)
```

### **Correct usage**

```java
map.remove("a");
```

### **Common pitfalls**

- ❌ Assuming failure throws exception
    

### **Failure example**

```java
map.remove("missing").toString();
```

**Failure:** `NullPointerException`

### **Correct alternative**

```java
map.remove("missing"); // safe
```

---

## 6️⃣ `remove(Object key, Object value)`

### **Method**

```java
boolean remove(Object key, Object value)
```

### **Correct usage**

```java
map.remove("a", 1);
```

### **Common pitfalls**

- ❌ Assuming key-only removal
    

---

## 7️⃣ `putIfAbsent(K key, V value)`

### **Method**

```java
V putIfAbsent(K key, V value)
```

### **Correct usage**

```java
map.putIfAbsent("a", 1);
```

### **Common pitfalls**

- ❌ Confusing with `computeIfAbsent`
    

---

## 8️⃣ `getOrDefault(Object key, V defaultValue)`

### **Method**

```java
V getOrDefault(Object key, V defaultValue)
```

### **Correct usage**

```java
map.getOrDefault("x", 0);
```

### **Common pitfalls**

- ❌ Assuming it inserts default
    

### **Failure example**

```java
map.getOrDefault("x", 0);
map.containsKey("x");
```

**Failure:** key not added

### **Correct alternative**

```java
map.putIfAbsent("x", 0);
```

---

## 9️⃣ `compute(...)`

### **Method**

```java
V compute(K key, BiFunction<? super K,? super V,? extends V> f)
```

### **Correct usage**

```java
map.compute("a", (k,v) -> v==null ? 1 : v+1);
```

### **Common pitfalls**

- ❌ Returning `null` unintentionally
    

### **Failure example**

```java
map.compute("a", (k,v) -> null);
```

**Failure:** key removed

---

## 🔟 `computeIfAbsent(...)`

### **Method**

```java
V computeIfAbsent(K key, Function<? super K,? extends V> f)
```

### **Correct usage**

```java
map.computeIfAbsent("a", k -> 1);
```

### **Common pitfalls**

- ❌ Expecting recomputation when value exists
    

---

## 1️⃣1️⃣ `computeIfPresent(...)`

### **Method**

```java
V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> f)
```

### **Correct usage**

```java
map.computeIfPresent("a", (k,v) -> v+1);
```

---

## 1️⃣2️⃣ `merge(...)`

### **Method**

```java
V merge(K key, V value,
        BiFunction<? super V,? super V,? extends V> f)
```

### **Correct usage**

```java
map.merge("a", 1, Integer::sum);
```

### **Common pitfalls**

- ❌ Returning `null` from remapping
    

### **Failure example**

```java
map.merge("a", 1, (o,n) -> null);
```

**Failure:** entry removed

---

## 1️⃣3️⃣ `keySet()`

### **Method**

```java
Set<K> keySet()
```

### **Correct usage**

```java
for (K k : map.keySet()) { ... }
```

### **Common pitfalls**

- ❌ Modifying map during iteration
    

---

## 1️⃣4️⃣ `values()`

### **Method**

```java
Collection<V> values()
```

### **Correct usage**

```java
for (V v : map.values()) { ... }
```

### **Common pitfalls**

- ❌ Assuming uniqueness
    

---

## 1️⃣5️⃣ `entrySet()`

### **Method**

```java
Set<Map.Entry<K,V>> entrySet()
```

### **Correct usage**

```java
for (var e : map.entrySet())
    e.setValue(e.getValue()+1);
```

### **Common pitfalls**

- ❌ Structural modification outside iterator
    

---

## 1️⃣6️⃣ `size()`, `isEmpty()`, `clear()`

### **Method**

```java
int size()
boolean isEmpty()
void clear()
```

### **Correct usage**

```java
map.clear();
```

---

## 1️⃣7️⃣ `replace(...)`

### **Method**

```java
V replace(K key, V value)
boolean replace(K key, V oldValue, V newValue)
```

### **Correct usage**

```java
map.replace("a", 1, 2);
```

---

## 🚨 Conceptual Pitfalls (Very Important)

- ❌ `HashMap` ≠ ordered map
    
    ```java
    map.keySet();
    ```
    
    ✅ Use `LinkedHashMap` if order matters
    
- ❌ `get()` ≠ existence check
    
    ```java
    map.get(k) == null
    ```
    
    ✅ Use `containsKey`
    
- ❌ Thread-safe by default
    
    ```java
    new HashMap<>();
    ```
    
    ✅ Use `ConcurrentHashMap`
    

---

## 🧠 Mental Model (Exam + Design)

- Hash-based **key → bucket → entry**
    
- Average O(1), worst-case O(n)
    
- Allows **one null key**
    
- Equality via `hashCode()` + `equals()`
    
- Ordering and synchronization are **explicitly excluded**
    

---

## 📌 Summary Table

|Method|Purpose|Common Pitfall|
|---|---|---|
|`put`|Insert|Silent overwrite|
|`get`|Lookup|Null confusion|
|`containsKey`|Presence|Misusing get|
|`compute`|Re-map|Null removes key|
|`merge`|Combine|Null deletes|
|`entrySet`|Iterate|Concurrent mod|
|`asMap`|—|❌ not present|

---

## ✅ Golden Rule

`HashMap` gives **fast lookup, no order, no safety**.  
Nulls are allowed; overwrites are silent.  
If you need guarantees, choose a different Map.

# ➕ HashMap Iteration — Procedures & Edge-Case Rules (Addition Only)

> This section **extends iteration semantics only**. It does **not** redefine `HashMap`.

---

## 1️⃣ Canonical Iteration Procedure (Read-Only)

### **Preferred**

```java
for (Map.Entry<K,V> e : map.entrySet()) {
    K k = e.getKey();
    V v = e.getValue();
}
```


**Why this is canonical**

- Single traversal
    
- No secondary lookups
    
- Key–value coherence guaranteed
    

**Invariant**

- ❗ Safe **only if no structural modification occurs**
    

- - - 
### ➕ `Map.Entry<K,V>` — What It Is

> `Map.Entry<K,V>` is a **live view of a single key–value pair** inside a `Map`, exposed only during iteration.

### Key facts

- Represents **one mapping**, not a copy
    
- `getKey()` and `getValue()` read the current state
    
- `setValue(V)` **mutates the map’s value** for that key
    
- Entry objects are **valid only during iteration**
    

### Common misconceptions

- ❌ It is an independent object
    
- ❌ Safe to store and use later
    

```java
Map.Entry<K,V> e = map.entrySet().iterator().next();
map.clear();
e.getValue();   // ❌ undefined / unreliable
```

### Correct mental model

- Think of `Map.Entry` as a **cursor into the map**, not data you own
    
- Structural changes invalidate existing entries
### ** Why this is canonical**

- Single traversal
    
- No secondary lookups
    
- Key–value coherence guaranteed
    

### **Invariant**

- ❗ Safe **only if no structural modification occurs**
    

---

## 2️⃣ Key-Set Iteration (Lookup-Based)

```java
for (K k : map.keySet()) {
    V v = map.get(k);
}
```

### **When acceptable**

- Read-only logic
    
- Value access is conditional
    

### **Hidden cost**

- ❌ Extra hash lookup per iteration
    
- ❌ Fragile if values may be `null`
    

---

## 3️⃣ Values-Only Iteration

```java
for (V v : map.values()) {
    // value-only logic
}
```

### **Constraints**

- ❌ No key access
    
- ❌ No safe removal by key
    
- ❌ Ambiguous when duplicates exist
    

---

## 4️⃣ Iterator-Based Removal (ONLY Legal Way)

```java
Iterator<Map.Entry<K,V>> it = map.entrySet().iterator();
while (it.hasNext()) {
    Map.Entry<K,V> e = it.next();
    if (e.getValue() == 0) {
        it.remove();   // ✔ legal
    }
}
```

### **Rule**

- ✅ `Iterator.remove()` is the **only** mutation allowed during iteration
    

---

## 5️⃣ ❌ Illegal Structural Modification Patterns

### **Case A: Direct remove**

```java
for (K k : map.keySet()) {
    map.remove(k);
}
```

**Failure:** `ConcurrentModificationException`

---

### **Case B: put during iteration**

```java
for (Map.Entry<K,V> e : map.entrySet()) {
    map.put("x", 10);
}
```

**Failure:** `ConcurrentModificationException`

---

## 6️⃣ Safe Structural Modification Strategies

### **Strategy 1: Collect → Mutate**

```java
List<K> toRemove = new ArrayList<>();
for (var e : map.entrySet()) {
    if (e.getValue() == 0) toRemove.add(e.getKey());
}
toRemove.forEach(map::remove);
```

---

### **Strategy 2: Iterator Removal (Preferred)**

```java
Iterator<K> it = map.keySet().iterator();
while (it.hasNext()) {
    if (it.next().equals("x")) it.remove();
}
```

---

## 7️⃣ `forEach(...)` Iteration Semantics

```java
map.forEach((k,v) -> {
    System.out.println(k + ":" + v);
});
```

### **Rules**

- ❌ No structural modification allowed
    
- ❌ Exceptions propagate immediately
    
- ❌ Order is unspecified
    

---

## 8️⃣ Entry Mutation vs Structural Mutation

### **Allowed**

```java
for (var e : map.entrySet()) {
    e.setValue(e.getValue() + 1);   // ✔ value update
}
```

### **Forbidden**

```java
for (var e : map.entrySet()) {
    map.remove(e.getKey());        // ❌ structure change
}
```

---

## 9️⃣ Fail-Fast vs Weakly-Consistent (Exam Trap)

|Map Type|Iterator Behavior|
|---|---|
|`HashMap`|Fail-fast|
|`LinkedHashMap`|Fail-fast|
|`ConcurrentHashMap`|Weakly consistent|

```java
// HashMap
→ throws CME

// ConcurrentHashMap
→ no CME, but snapshot-like view
```

---

## 🔁 Iteration Decision Table (Quick Recall)

|Goal|Use|
|---|---|
|Read key + value|`entrySet()`|
|Read keys only|`keySet()`|
|Read values only|`values()`|
|Remove while iterating|`Iterator.remove()`|
|Parallel safety|`ConcurrentHashMap`|

---

## 🧠 One-Line Mental Rule

**Iterate with `entrySet()`.  
Mutate only through the iterator.  
Anything else is undefined behavior.**