# ☕ Java Variables 

## 🧠 What is a Variable?

A **variable** is a **container** that stores data (value) in memory.

👉 In simple words:
A variable is a name given to a memory location where data is stored.

---

## 📌 Example

```java
int age = 25;
```

* `int` → data type
* `age` → variable name
* `25` → value

---

## 📦 Real-Life Analogy

Think of a variable like a box:

```
Box Name → age  
Value inside → 25
```

Whenever you use `age`, Java will use the stored value (25).

---

## ⚙️ Syntax of Variable

```java
dataType variableName = value;
```

### Examples:

```java
int number = 10;
double salary = 50000.5;
char grade = 'A';
boolean isActive = true;
String name = "Aditya";
```

---

# 🔹 Types of Variables in Java

Java has 3 main types of variables:

```
Variables
├── Local Variable
├── Instance Variable
└── Static Variable
```

---

## 1️⃣ Local Variable

👉 Declared inside a method

```java
public class Test {
    public static void main(String[] args) {
        int x = 10; // local variable
    }
}
```

### Key Points:

* Scope → only inside the method
* Must be initialized before use
* No default value

---

## 2️⃣ Instance Variable (Non-static)

👉 Declared inside class but outside methods

```java
class Student {
    int age; // instance variable
}
```

### Key Points:

* Each object gets its own copy
* Stored in heap memory
* Default values are assigned

---

## 3️⃣ Static Variable (Class Variable)

👉 Declared using `static` keyword

```java
class Student {
    static String college = "ABC College";
}
```

### Key Points:

* Shared among all objects
* Only one copy exists
* Memory efficient

---

# ⚡ Rules for Naming Variables

## ✅ Valid Names:

```java
int age;
int studentName;
int _value;
int $price;
```

---

## ❌ Invalid Names:

```java
int 1age;     // cannot start with number
int class;    // reserved keyword
```

---

## 🧠 Best Practices

* Use **camelCase**

  * `studentName`
  * `totalMarks`
* Use meaningful names

---

# 🔄 Changing Variable Value

```java
int x = 10;
x = 20;
```

👉 Output:

```
20
```

---

# ⚠️ Important Concepts

## 🔥 Declaration

```java
int x;
```

---

## 🔥 Initialization

```java
x = 10;
```

---

## 🔥 Declaration + Initialization

```java
int x = 10;
```

---

## 🔥 Multiple Variables

```java
int a = 1, b = 2, c = 3;
```

---

# ⚙️ Default Values (Instance Variables)

| Type    | Default Value |
| ------- | ------------- |
| int     | 0             |
| double  | 0.0           |
| boolean | false         |
| char    | '\u0000'      |
| object  | null          |

---

# 🎯 Real Example

```java
public class Main {
    String name = "Aditya"; // instance variable
    static String college = "XYZ"; // static variable

    public static void main(String[] args) {
        int age = 22; // local variable

        System.out.println(age);
        System.out.println(college);
    }
}
```

---

# 🧠 Final Summary

* Variable = container for storing data
* Must have:

  * Data type
  * Name
  * Value
* Types:

  * Local
  * Instance
  * Static
* Follow naming rules (camelCase)


