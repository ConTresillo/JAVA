# 🟠 java.lang.String — Methods, Pitfalls, Fixes

> `String` is an **immutable sequence of UTF-16 code units** representing text in Java.

---

## 1️⃣ `length()`

### **Method**

```java
public int length();
```

### **Correct usage**

```java
int n = s.length();
```

### **Common pitfalls**

- ❌ Assuming it returns number of characters (graphemes)
    
- ❌ Using it for Unicode-visible length
    

### **Failure example**

```java
"😊".length(); // returns 2
```

**Failure:** Misinterpreting UTF-16 code units as characters

### **Correct alternative**

```java
s.codePointCount(0, s.length());
```

---

## 2️⃣ `isEmpty()`

### **Method**

```java
public boolean isEmpty();
```

### **Correct usage**

```java
if (s.isEmpty()) { }
```

### **Common pitfalls**

- ❌ Confusing empty with blank
    
- ❌ Calling on null reference
    

### **Failure example**

```java
s.isEmpty(); // when s == null
```

**Failure:** NullPointerException

### **Correct alternative**

```java
s != null && s.isEmpty();
```

---

## 3️⃣ `charAt(int index)`

### **Method**

```java
public char charAt(int index);
```

### **Correct usage**

```java
char c = s.charAt(0);
```

### **Common pitfalls**

- ❌ Out-of-bounds index
    
- ❌ Assuming one char = one Unicode character
    

### **Failure example**

```java
s.charAt(s.length());
```

**Failure:** StringIndexOutOfBoundsException

### **Correct alternative**

```java
if (i >= 0 && i < s.length()) s.charAt(i);
```

---

## 4️⃣ `substring(int beginIndex)`

## 5️⃣ `substring(int beginIndex, int endIndex)`

### **Method**

```java
public String substring(int beginIndex);
public String substring(int beginIndex, int endIndex);
```

### **Correct usage**

```java
s.substring(1, 3);
```

### **Common pitfalls**

- ❌ endIndex treated as inclusive
    
- ❌ Negative or reversed indices
    

### **Failure example**

```java
s.substring(3, 1);
```

**Failure:** StringIndexOutOfBoundsException

### **Correct alternative**

```java
s.substring(l, r); // [l, r)
```

---

## 6️⃣ `equals(Object anObject)`

### **Method**

```java
public boolean equals(Object obj);
```

### **Correct usage**

```java
s.equals("abc");
```

### **Common pitfalls**

- ❌ Using `==` for content comparison
    

### **Failure example**

```java
s == "abc";
```

**Failure:** Reference comparison

### **Correct alternative**

```java
"abc".equals(s);
```

---

## 7️⃣ `equalsIgnoreCase(String anotherString)`

### **Method**

```java
public boolean equalsIgnoreCase(String anotherString);
```

### **Correct usage**

```java
s.equalsIgnoreCase("ABC");
```

### **Common pitfalls**

- ❌ Locale-sensitive expectations
    

### **Failure example**

```java
"i".equalsIgnoreCase("İ");
```

**Failure:** Locale rules not applied

### **Correct alternative**

```java
s.toLowerCase(Locale.ROOT).equals(t.toLowerCase(Locale.ROOT));
```

---

## 8️⃣ `compareTo(String anotherString)`

## 9️⃣ `compareToIgnoreCase(String str)`

### **Method**

```java
public int compareTo(String anotherString);
public int compareToIgnoreCase(String str);
```

### **Correct usage**

```java
s.compareTo(t) < 0;
```

### **Common pitfalls**

- ❌ Assuming alphabetical per locale
    

### **Failure example**

```java
"a".compareTo("Z"); // > 0
```

### **Correct alternative**

```java
Collator.getInstance().compare(a, b);
```

---

## 🔟 `startsWith(String prefix)`

## 1️⃣1️⃣ `startsWith(String prefix, int toffset)`

## 1️⃣2️⃣ `endsWith(String suffix)`

### **Method**

```java
public boolean startsWith(String prefix);
public boolean startsWith(String prefix, int toffset);
public boolean endsWith(String suffix);
```

### **Correct usage**

```java
s.startsWith("pre");
```

### **Common pitfalls**

- ❌ Confusing substring search with prefix check
    

### **Failure example**

```java
s.startsWith("mid");
```

### **Correct alternative**

```java
s.contains("mid");
```

---

## 1️⃣3️⃣ `indexOf(...)`

## 1️⃣4️⃣ `lastIndexOf(...)`

### **Method**

```java
public int indexOf(int ch);
public int indexOf(String str);
public int indexOf(String str, int fromIndex);
public int lastIndexOf(String str);
```

### **Correct usage**

```java
int i = s.indexOf("ab");
```

### **Common pitfalls**

- ❌ Forgetting `-1` sentinel
    

### **Failure example**

```java
s.substring(s.indexOf("x"));
```

**Failure:** -1 passed to substring

### **Correct alternative**

```java
int i = s.indexOf("x");
if (i != -1) s.substring(i);
```

---

## 1️⃣5️⃣ `contains(CharSequence s)`

### **Method**

```java
public boolean contains(CharSequence s);
```

### **Correct usage**

```java
s.contains("abc");
```

### **Common pitfalls**

- ❌ Assuming regex support
    

### **Failure example**

```java
s.contains("a.*b");
```

### **Correct alternative**

```java
s.matches("a.*b");
```

---

## 1️⃣6️⃣ `replace(char oldChar, char newChar)`

## 1️⃣7️⃣ `replace(CharSequence target, CharSequence replacement)`

### **Method**

```java
public String replace(char oldChar, char newChar);
public String replace(CharSequence target, CharSequence replacement);
```

### **Correct usage**

```java
s.replace("a", "b");
```

### **Common pitfalls**

- ❌ Expecting regex behavior
    

### **Failure example**

```java
s.replace(".", "_");
```

### **Correct alternative**

```java
s.replaceAll("\\.", "_");
```

---

## 1️⃣8️⃣ `replaceAll(String regex, String replacement)`

## 1️⃣9️⃣ `replaceFirst(String regex, String replacement)`

### **Method**

```java
public String replaceAll(String regex, String replacement);
public String replaceFirst(String regex, String replacement);
```

### **Correct usage**

```java
s.replaceAll("\\d+", "#");
```

### **Common pitfalls**

- ❌ Forgetting regex escaping
    

### **Failure example**

```java
s.replaceAll(".", "_");
```

**Failure:** Replaces every character

### **Correct alternative**

```java
s.replace(".", "_");
```

---

## 2️⃣0️⃣ `split(String regex)`

## 2️⃣1️⃣ `split(String regex, int limit)`

### **Method**

```java
public String[] split(String regex);
public String[] split(String regex, int limit);
```

### **Correct usage**

```java
s.split(",");
```

### **Common pitfalls**

- ❌ Trailing empty strings dropped
    

### **Failure example**

```java
"a,b,".split(",");
```

### **Correct alternative**

```java
"a,b,".split(",", -1);
```

---

## 2️⃣2️⃣ `toLowerCase()`

## 2️⃣3️⃣ `toUpperCase()`

## 2️⃣4️⃣ `toLowerCase(Locale locale)`

## 2️⃣5️⃣ `toUpperCase(Locale locale)`

### **Method**

```java
public String toLowerCase();
public String toUpperCase();
public String toLowerCase(Locale locale);
public String toUpperCase(Locale locale);
```

### **Correct usage**

```java
s.toLowerCase(Locale.ROOT);
```

### **Common pitfalls**

- ❌ Locale-dependent bugs
    

### **Failure example**

```java
s.toLowerCase(); // Turkish locale
```

### **Correct alternative**

```java
s.toLowerCase(Locale.ROOT);
```

---

## 2️⃣6️⃣ `trim()`

## 2️⃣7️⃣ `strip()`

## 2️⃣8️⃣ `stripLeading()`

## 2️⃣9️⃣ `stripTrailing()`

### **Method**

```java
public String trim();
public String strip();
public String stripLeading();
public String stripTrailing();
```

### **Correct usage**

```java
s.strip();
```

### **Common pitfalls**

- ❌ `trim()` not Unicode-aware
    

### **Failure example**

```java
"\u2003".trim().isEmpty(); // false
```

### **Correct alternative**

```java
"\u2003".strip().isEmpty();
```

---

## 3️⃣0️⃣ `isBlank()`

### **Method**

```java
public boolean isBlank();
```

### **Correct usage**

```java
s.isBlank();
```

### **Common pitfalls**

- ❌ Confusing with `isEmpty()`
    

### **Failure example**

```java
" ".isEmpty(); // false
```

### **Correct alternative**

```java
" ".isBlank(); // true
```

---

## 3️⃣1️⃣ `toCharArray()`

### **Method**

```java
public char[] toCharArray();
```

### **Correct usage**

```java
char[] a = s.toCharArray();
```

### **Common pitfalls**

- ❌ Assuming modifiable String
    

### **Failure example**

```java
a[0] = 'x'; // does not change s
```

### **Correct alternative**

```java
new String(a);
```

---

## 3️⃣2️⃣ `getBytes()`

## 3️⃣3️⃣ `getBytes(Charset charset)`

### **Method**

```java
public byte[] getBytes();
public byte[] getBytes(Charset charset);
```

### **Correct usage**

```java
s.getBytes(StandardCharsets.UTF_8);
```

### **Common pitfalls**

- ❌ Platform default encoding
    

### **Failure example**

```java
s.getBytes(); // platform dependent
```

### **Correct alternative**

```java
s.getBytes(StandardCharsets.UTF_8);
```

---

## 3️⃣4️⃣ `intern()`

### **Method**

```java
public native String intern();
```

### **Correct usage**

```java
s.intern();
```

### **Common pitfalls**

- ❌ Assuming performance benefit
    

### **Failure example**

```java
manyStrings.intern();
```

**Failure:** Metaspace pressure

### **Correct alternative**

```java
Use only for canonical keys
```

---

## 3️⃣5️⃣ `valueOf(...)` (static)

### **Method**

```java
public static String valueOf(Object obj);
```

### **Correct usage**

```java
String.valueOf(x);
```

### **Common pitfalls**

- ❌ Using `obj.toString()` directly
    

### **Failure example**

```java
obj.toString(); // when obj == null
```

### **Correct alternative**

```java
String.valueOf(obj);
```

---

## 🚨 Conceptual Pitfalls (Very Important)

- ❌ **String vs StringBuilder**
    
    ```java
    s += x; // creates new String
    ```
    
    ✅ Use `StringBuilder` in loops
    
- ❌ **Length vs characters**
    
    ```java
    s.length();
    ```
    
    ✅ Use `codePointCount`
    
- ❌ **trim vs strip**
    
    ```java
    s.trim();
    ```
    
    ✅ Prefer `strip()` for Unicode
    

---

## 🧠 Mental Model (Exam + Design)

- Immutable value object
    
- Backed by UTF-16 code units
    
- All mutating ops return new String
    
- Equality defined by content, not identity
    
- Encoding concerns live outside String
    
- Performance rules live in usage, not API
    

---

## 📌 Summary Table

|Method|Purpose|Common Pitfall|
|---|---|---|
|`length()`|UTF-16 length|Not character count|
|`equals()`|Content compare|Using `==`|
|`substring()`|Slice|Index misuse|
|`split()`|Tokenize|Regex rules|
|`replace()`|Literal replace|Expecting regex|
|`trim()`|ASCII trim|Not Unicode|
|`strip()`|Unicode trim|Requires Java 11+|
|`getBytes()`|Encode|Platform default|
|`intern()`|Canonicalize|Memory misuse|

---

## ✅ Golden Rule

Strings are **immutable values**, not buffers.  
Indices are **UTF-16 code units**, not characters.  
When performance or mutation matters, **StringBuilder exists for a reason**.