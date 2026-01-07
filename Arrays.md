## 🟠 `java.util.Arrays` — Methods, Pitfalls, Fixes

> `Arrays` is a **utility class** providing **static methods** for operating on Java arrays (sorting, searching, comparing, filling, copying, streaming).

---

## 1️⃣ `sort(...)`

### **Method**

```java
static void sort(int[] a)
static void sort(int[] a, int fromIndex, int toIndex)
static <T extends Comparable<? super T>> void sort(T[] a)
static <T> void sort(T[] a, Comparator<? super T> c)
static <T> void sort(T[] a, int fromIndex, int toIndex, Comparator<? super T> c)
```

### **Correct usage**

```java
int[] a = {3,1,2};
Arrays.sort(a);

String[] s = {"b","a"};
Arrays.sort(s, Comparator.reverseOrder());
```

### **Common pitfalls**

- ❌ Assuming stability for primitive sorts (not guaranteed)
    
- ❌ Forgetting `toIndex` is **exclusive**
    
- ❌ Using `Comparator` with primitive arrays (not allowed)
    

### **Failure example**

```java
Arrays.sort(a, 1, a.length + 1);
```

**Failure:** `ArrayIndexOutOfBoundsException`

### **Correct alternative**

```java
Arrays.sort(a, 1, a.length);
```

---

## 2️⃣ `parallelSort(...)`

### **Method**

```java
static void parallelSort(int[] a)
static <T extends Comparable<? super T>> void parallelSort(T[] a)
static <T> void parallelSort(T[] a, Comparator<? super T> c)
```

### **Correct usage**

```java
Arrays.parallelSort(a);
```

### **Common pitfalls**

- ❌ Expecting deterministic thread usage
    
- ❌ Using for small arrays (overhead dominates)
    

### **Failure example**

```java
Arrays.parallelSort(null);
```

**Failure:** `NullPointerException`

### **Correct alternative**

```java
if (a.length > 10_000) Arrays.parallelSort(a);
```

---

## 3️⃣ `binarySearch(...)`

### **Method**

```java
static int binarySearch(int[] a, int key)
static <T> int binarySearch(T[] a, T key, Comparator<? super T> c)
```

### **Correct usage**

```java
int idx = Arrays.binarySearch(a, 5);
```

### **Common pitfalls**

- ❌ Using on **unsorted** arrays
    
- ❌ Misinterpreting negative return value
    

### **Failure example**

```java
int[] a = {3,1,2};
Arrays.binarySearch(a, 2);
```

**Failure:** Undefined result

### **Correct alternative**

```java
Arrays.sort(a);
Arrays.binarySearch(a, 2);
```

---

## 4️⃣ `equals(...)`

### **Method**

```java
static boolean equals(int[] a, int[] a2)
static boolean equals(Object[] a, Object[] a2)
```

### **Correct usage**

```java
Arrays.equals(a, b);
```

### **Common pitfalls**

- ❌ Expecting deep comparison for nested arrays
    

### **Failure example**

```java
int[][] x = {{1}};
int[][] y = {{1}};
Arrays.equals(x, y);
```

**Failure:** returns `false`

### **Correct alternative**

```java
Arrays.deepEquals(x, y);
```

---

## 5️⃣ `deepEquals(...)`

### **Method**

```java
static boolean deepEquals(Object[] a1, Object[] a2)
```

### **Correct usage**

```java
Arrays.deepEquals(x, y);
```

### **Common pitfalls**

- ❌ Using with primitive 2D arrays via `Object[]` cast incorrectly
    

### **Failure example**

```java
Arrays.deepEquals(new int[]{1}, new int[]{1});
```

**Failure:** compile-time mismatch

### **Correct alternative**

```java
Arrays.equals(new int[]{1}, new int[]{1});
```

---

## 6️⃣ `fill(...)`

### **Method**

```java
static void fill(int[] a, int val)
static <T> void fill(T[] a, T val)
```

### **Correct usage**

```java
Arrays.fill(a, 0);
```

### **Common pitfalls**

- ❌ Assuming new array creation
    
- ❌ Using mutable object as fill value
    

### **Failure example**

```java
List[] arr = new List[3];
Arrays.fill(arr, new ArrayList<>());
arr[0].add(1);
```

**Failure:** all indices modified

### **Correct alternative**

```java
for (int i=0;i<arr.length;i++) arr[i]=new ArrayList<>();
```

---

## 7️⃣ `copyOf(...)`

### **Method**

```java
static int[] copyOf(int[] original, int newLength)
static <T> T[] copyOf(T[] original, int newLength)
```

### **Correct usage**

```java
int[] b = Arrays.copyOf(a, a.length);
```

### **Common pitfalls**

- ❌ Expecting deep copy
    

### **Failure example**

```java
int[][] b = Arrays.copyOf(a, a.length);
```

**Failure:** inner arrays shared

### **Correct alternative**

```java
for (int i=0;i<a.length;i++) b[i]=Arrays.copyOf(a[i], a[i].length);
```

---

## 8️⃣ `copyOfRange(...)`

### **Method**

```java
static int[] copyOfRange(int[] original, int from, int to)
```

### **Correct usage**

```java
Arrays.copyOfRange(a, 1, 3);
```

### **Common pitfalls**

- ❌ Forgetting `to` is exclusive
    

### **Failure example**

```java
Arrays.copyOfRange(a, -1, 2);
```

**Failure:** `ArrayIndexOutOfBoundsException`

### **Correct alternative**

```java
Arrays.copyOfRange(a, 0, Math.min(2, a.length));
```

---

## 9️⃣ `toString(...)`

### **Method**

```java
static String toString(int[] a)
```

### **Correct usage**

```java
System.out.println(Arrays.toString(a));
```

### **Common pitfalls**

- ❌ Using for nested arrays
    

### **Failure example**

```java
System.out.println(Arrays.toString(new int[][]{{1}}));
```

**Failure:** prints object references

### **Correct alternative**

```java
Arrays.deepToString(new int[][]{{1}});
```

---

## 🔟 `deepToString(...)`

### **Method**

```java
static String deepToString(Object[] a)
```

### **Correct usage**

```java
Arrays.deepToString(obj2d);
```

### **Common pitfalls**

- ❌ Using with primitive top-level arrays
    

---

## 1️⃣1️⃣ `hashCode(...)` / `deepHashCode(...)`

### **Method**

```java
static int hashCode(int[] a)
static int deepHashCode(Object[] a)
```

### **Correct usage**

```java
Arrays.hashCode(a);
```

### **Common pitfalls**

- ❌ Expecting consistency with `Object.hashCode()`
    

---

## 1️⃣2️⃣ `asList(...)`

### **Method**

```java
static <T> List<T> asList(T... a)
```

### **Correct usage**

```java
List<Integer> l = Arrays.asList(1,2,3);
```

### **Common pitfalls**

- ❌ Assuming resizable list
    
- ❌ Using with primitive arrays
    

### **Failure example**

```java
List<int[]> l = Arrays.asList(new int[]{1,2});
l.add(new int[]{3});
```

**Failure:** `UnsupportedOperationException`

### **Correct alternative**

```java
new ArrayList<>(Arrays.asList(1,2,3));
```

---

## 1️⃣3️⃣ `stream(...)`

### **Method**

```java
static IntStream stream(int[] a)
static <T> Stream<T> stream(T[] a)
```

### **Correct usage**

```java
Arrays.stream(a).sum();
```

### **Common pitfalls**

- ❌ Confusing with `Collection.stream()`
    

---

## 1️⃣4️⃣ `setAll(...)` / `parallelSetAll(...)`

### **Method**

```java
static void setAll(int[] a, IntUnaryOperator gen)
static void parallelSetAll(int[] a, IntUnaryOperator gen)
```

### **Correct usage**

```java
Arrays.setAll(a, i -> i*i);
```

### **Common pitfalls**

- ❌ Side effects in generator
    

---

## 🚨 Conceptual Pitfalls (Very Important)

- ❌ `Arrays.equals()` ≠ deep comparison
    
    ```java
    Arrays.equals(new int[][]{{1}}, new int[][]{{1}});
    ```
    
    ✅ Use `deepEquals`
    
- ❌ `asList()` ≠ `ArrayList`
    
    ```java
    Arrays.asList(1,2).add(3);
    ```
    
    ✅ Wrap with `new ArrayList<>(...)`
    
- ❌ `binarySearch()` on unsorted data  
    ✅ Always sort first
    

---

## 🧠 Mental Model (Exam + Design)

- Utility class: **no instances, all static**
    
- Operates on **raw arrays**, not collections
    
- Guarantees **performance**, not flexibility
    
- Deep behavior is **explicit**, never implicit
    
- Rules live in **method contract**, not runtime checks
    

---

## 📌 Summary Table

|Method|Purpose|Common Pitfall|
|---|---|---|
|`sort`|Ordering|Wrong index bounds|
|`binarySearch`|Lookup|Array not sorted|
|`equals`|Compare|Not deep|
|`asList`|View|Fixed-size|
|`copyOf`|Resize|Shallow copy|
|`stream`|Streams|Primitive vs object|
|`fill`|Assign|Shared mutable refs|

---

## ✅ Golden Rule

Arrays are **low-level, fixed-size, shallow by default**.  
Depth, mutability, and resizing are **your responsibility**.  
Never assume collection-like behavior.