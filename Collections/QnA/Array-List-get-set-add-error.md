This error comes from a **misunderstanding of what `new ArrayList<>(max)` does** and **how list assignment works in Java**.

---

## What your code says to Java

```java
List<List<Integer>> L = new ArrayList<>(max);
L.get(0) = new ArrayList<>();
```

### Line 1 — capacity, not elements

```java
new ArrayList<>(max)
```

- This sets **internal capacity only**
    
- **Size is still 0**
    
- No elements exist
    
- Valid indices: **none**
    

So immediately:

```java
L.get(0)   // ❌ IndexOutOfBoundsException
```

---

## Second error — illegal assignment

Even if index 0 existed, this is **invalid Java syntax**:

```java
L.get(0) = new ArrayList<>(); // ❌
```

Reason:

- `get()` returns a **value**
    
- You cannot assign to a method call
    

Java requires:

```java
L.set(index, value);
```

---

## Correct mental model (must internalize)

| Concept     | Meaning                   |
| ----------- | ------------------------- |
| capacity    | reserved space            |
| size        | number of actual elements |
| `get(i)`    | read                      |
| `set(i, v)` | replace existing element  |
| `add(v)`    | append                    |
| `add(i, v)` | insert                    |

Capacity ≠ Size.

---

## Correct ways to do what you want

### ✅ Case 1: You want `max` empty lists

```java
List<List<Integer>> L = new ArrayList<>(max);
for (int i = 0; i < max; i++) {
    L.add(new ArrayList<>());
}
```

Now:

- `L.size() == max`
    
- `L.get(i)` is valid
    

---

### ✅ Case 2: You want to replace an existing index

```java
L.set(0, new ArrayList<>());
```

⚠️ Only valid **after elements exist**

---

### ❌ What does NOT work

```java
new ArrayList<>(max);  // does NOT create elements
L.get(0);              // invalid when size == 0
L.get(0) = something;  // invalid syntax
```

---

## One-sentence rule (exam-safe)

> **`ArrayList(capacity)` reserves memory only; elements exist only after `add()` is called.**

If you remember just that, this class of bugs disappear.

---

If you want next:

- we can map this to how vectors work internally, or
    
- contrast this with `int[]` where size _is_ fixed and real.