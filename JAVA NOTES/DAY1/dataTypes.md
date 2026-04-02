## ☕ Java Data Types 

## 📌 What is a Data Type?

A **data type** defines:

* What kind of value a variable can store
* How much memory it will take
* What operations can be performed on it

👉 Example:

```java
int age = 25;
```

* `int` → data type
* `age` → variable
* `25` → value

---

# 🧠 Types of Data Types in Java

Java has **2 main categories**:

![Java Data Types](https://media.geeksforgeeks.org/wp-content/uploads/20250619155321977504/data_types_in_java.webp)

---

# 🔹 1. Primitive Data Types

👉 These are **basic built-in types**
👉 They store **actual values directly in memory**

---

## 📦 List of Primitive Data Types

| Type    | Size          | Range                  | Example                |
| ------- | ------------- | ---------------------- | ---------------------- |
| byte    | 1 byte        | -128 to 127            | `byte b = 10;`         |
| short   | 2 bytes       | -32,768 to 32,767      | `short s = 1000;`      |
| int     | 4 bytes       | -2³¹ to 2³¹-1          | `int a = 100;`         |
| long    | 8 bytes       | very large             | `long l = 100000L;`    |
| float   | 4 bytes       | decimal                | `float f = 10.5f;`     |
| double  | 8 bytes       | high precision decimal | `double d = 10.55;`    |
| char    | 2 bytes       | Unicode characters     | `char c = 'A';`        |
| boolean | JVM dependent | true/false             | `boolean flag = true;` |

---

## 🔸 Integer Types (Whole Numbers)

### ✅ int (Most Used)

```java
int x = 10;
```

* Default integer type
* Best for general use

---

### ✅ byte

```java
byte b = 100;
```

* Saves memory
* Rarely used in normal apps

---

### ✅ short

```java
short s = 2000;
```

* Less common

---

### ✅ long

```java
long l = 10000000000L;
```

⚠️ Must use `L` at the end

---

## 🔸 Floating Types (Decimals)

### ✅ float

```java
float f = 10.5f;
```

⚠️ Must use `f`

---

### ✅ double (Default)

```java
double d = 10.555;
```

* More precise than float
* Default decimal type

---

## 🔸 Character Type

```java
char ch = 'A';
```

* Stores single character
* Uses Unicode

```java
char ch = 65; // Output: A
```

---

## 🔸 Boolean Type

```java
boolean isActive = true;
```

* Only:

  * true
  * false

❌ Cannot use 0 or 1 like C/C++

---

# 🔹 2. Non-Primitive (Reference) Data Types

👉 These store **reference (address)**, not actual data
👉 Actual data is stored in **heap memory**

---

## 📦 Examples

### ✅ String

```java
String name = "Aditya";
```

---

### ✅ Array

```java
int[] arr = {1, 2, 3};
```

---

### ✅ Class & Object

```java
class Student {
    int age;
}
```

---

## 📌 Key Difference

| Feature  | Primitive  | Non-Primitive |
| -------- | ---------- | ------------- |
| Stores   | Value      | Reference     |
| Memory   | Stack      | Heap          |
| Size     | Fixed      | Depends       |
| Examples | int, float | String, Array |

---

# ⚙️ Memory Concept

## 🔹 Primitive

```java
int a = 10;
```

* Stored in **stack**
* Direct value stored

---

## 🔹 Non-Primitive

```java
String s = "Hello";
```

* Stack → reference (`s`)
* Heap → actual value ("Hello")

---

# ⚡ Default Values (Class Level)

| Type    | Default Value |
| ------- | ------------- |
| int     | 0             |
| float   | 0.0           |
| double  | 0.0           |
| boolean | false         |
| char    | '\u0000'      |
| object  | null          |

---

# 🔄 Type Casting

## 🔹 Implicit (Automatic)

```java
int a = 10;
double b = a;
```

---

## 🔹 Explicit (Manual)

```java
double d = 10.5;
int a = (int) d; // Output: 10
```

⚠️ Data loss possible

---

# 🧩 Important Concepts

## 🔥 1. Default Types

* Integer → `int`
* Decimal → `double`

---

## 🔥 2. String is NOT primitive

```java
String s = "Hello";
```

---

## 🔥 3. char vs String

* char → `'A'`
* String → `"ABC"`

---

## 🔥 4. Wrapper Classes

| Primitive | Wrapper   |
| --------- | --------- |
| int       | Integer   |
| double    | Double    |
| char      | Character |

```java
Integer num = 10;
```

👉 Used in collections (ArrayList, etc.)

---

# 🎯 Real Example

```java
public class Main {
    public static void main(String[] args) {
        int age = 22;
        double salary = 50000.75;
        char grade = 'A';
        boolean isSelected = true;
        String name = "Aditya";

        System.out.println(age);
        System.out.println(salary);
        System.out.println(grade);
        System.out.println(isSelected);
        System.out.println(name);
    }
}
```

---

# 🧠 Final Summary

* Data Types define what kind of data we store
* Two types:

  * Primitive → actual value
  * Non-Primitive → reference
* Most used:

  * int
  * double
  * String
* float → use `f`
* long → use `L`

