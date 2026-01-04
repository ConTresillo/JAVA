## 🔹 String ↔ Numeric (int, long, float, double)

### **String → Numeric**
```java
String s = "123";
int x = Numeric.parseNumeric(s);
```
## Eg:
### **String → long**
```java
String s = "123";
int x = Integer.parseInt(s);
```
**Result:** `x = 123`

**Pitfalls**
- ❌ `"12.3"` → `NumberFormatException`
- ❌ `"12a"` partially parsed → parsing is all-or-nothing
- ❌ `valueOf` avoids errors → same rules, same exception

---

### **String → long**
```java
String s = "9876543210";
long x = Long.parseLong(s);
```
**Result:** `x = 9876543210L`

**Pitfalls**
- ❌ Overflow beyond `Long.MAX_VALUE`
- ❌ Whitespace allowed automatically → must `trim()`

---

### **String → double**
```java
String s = "3.14";
double x = Double.parseDouble(s);
```
**Result:** `x = 3.14`

**Pitfalls**
- ❌ Locale commas (`"3,14"`) work → invalid
- ❌ Exact precision → floating-point rounding applies

---

### **Numeric → String**
```java
Numeric x = 42;
String s = String.valueOf(x);
```
**Result:** `s = "42"`

**Pitfalls**
- ❌ `x.toString()` → primitives have no methods (works for NUMERIC)
- ❌ `"" + x` preferred → works but hides intent

---

## 🔹 Numeric ↔ Numeric

### **long → int (narrowing)**
```java
long x = 3000000000L;
int y = (int) x;
```
**Result:** unexpected value

**Pitfalls**
- ❌ Java checks overflow → silently truncates bits
- ❌ Cast validates range → it does not

---

### **double → int**
```java
double x = 3.9;
int y = (int) x;
```
**Result:** `y = 3`

**Pitfalls**
- ❌ Rounding happens → truncation toward zero
- ❌ Negative rounds down → `-3.9 → -3`

---

## 🔹 char ↔ Numeric

### **char → int**
```java
char c = '9';
int x = c;
```
**Result:** `x = 57`

**Pitfalls**
- ❌ Numeric value expected → Unicode code point
- ❌ ASCII only → Java uses Unicode

**Correct**
```java
int x = c - '0';
```

---

## 🔹 Wrapper ↔ Primitive

### **Integer → int**
```java
Integer x = null;
int y = x;
```
**Failure:** `NullPointerException`

**Pitfalls**
- ❌ Auto-unboxing is null-safe → it is not

**Correct**
```java
int y = (x != null) ? x : 0;
```

---

### **Integer comparison**
```java
Integer a = 1000;
Integer b = 1000;
System.out.println(a == b);
```
**Result:** `false`

**Pitfalls**
- ❌ `==` compares values → compares references
- ❌ Integer cache applies always → only `-128..127`

**Correct**
```java
a.equals(b);
```

---

## 🔹 Array Conversions

### **int[] → List\<Integer\>**
```java
int[] arr = {1,2,3};
List<Integer> list = new ArrayList<>();
for (int x : arr) list.add(x);
```
**Result:** `[1, 2, 3]`

**Pitfalls**
- ❌ `Arrays.asList(arr)` → produces `List<int[]>`
- ❌ Streams auto-box primitives → they do not

---

### **List\<Integer\> → int[]**
```java
List<Integer> list = List.of(1,2,3);
int[] arr = list.stream().mapToInt(i -> i).toArray();
```
**Result:** `{1, 2, 3}`

**Pitfalls**
- ❌ `toArray()` returns `int[]` → returns `Object[]`
- ❌ Null elements safe → unboxing causes NPE

---

## 🔹 Boolean Conversions

### **String → boolean**
```java
String s = "yes";
boolean b = Boolean.parseBoolean(s);
```
**Result:** `false`

**Pitfalls**
- ❌ `"yes"` → true → only `"true"` maps to true
- ❌ Invalid input throws → never throws

---

## 🔹 Object ↔ String

### **Object → String**
```java
Object obj = null;
String s = obj.toString();
```
**Failure:** `NullPointerException`

**Correct**
```java
String s = String.valueOf(obj);
```

**Pitfalls**
- ❌ `toString()` is null-safe → it is not
- ❌ Output is meaningful → depends on override
