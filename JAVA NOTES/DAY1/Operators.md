# ☕ Java Operators 


## 🧠 What are Operators?

Operators are **symbols** used to perform operations on variables and values.

👉 Simple:

> Operators help us **calculate, compare, and manipulate data**

---

## 📌 Example

```java
int a = 10;
int b = 5;

int sum = a + b; // + is an operator
```

---

# 🔹 Types of Operators in Java

```
Operators
├── Arithmetic Operators
├── Relational (Comparison) Operators
├── Logical Operators
├── Assignment Operators
├── Unary Operators
├── Bitwise Operators
├── Ternary Operator
```

---

# 1️⃣ Arithmetic Operators

👉 Used for mathematical operations

| Operator | Meaning        | Example |
| -------- | -------------- | ------- |
| +        | Addition       | a + b   |
| -        | Subtraction    | a - b   |
| *        | Multiplication | a * b   |
| /        | Division       | a / b   |
| %        | Modulus        | a % b   |

---

## 📌 Example

```java
int a = 10, b = 3;

System.out.println(a + b); // 13
System.out.println(a - b); // 7
System.out.println(a * b); // 30
System.out.println(a / b); // 3
System.out.println(a % b); // 1
```

---

# 2️⃣ Relational (Comparison) Operators

👉 Used to compare values (result = true/false)

| Operator | Meaning          |
| -------- | ---------------- |
| ==       | Equal to         |
| !=       | Not equal        |
| >        | Greater than     |
| <        | Less than        |
| >=       | Greater or equal |
| <=       | Less or equal    |

---

## 📌 Example

```java
int a = 10, b = 5;

System.out.println(a > b);  // true
System.out.println(a == b); // false
```

---

# 3️⃣ Logical Operators

👉 Used with boolean values

| Operator | Meaning |
| -------- | ------- |
| &&       | AND     |
| ||       | OR      |
| !        | NOT     |

---

## 📌 Example

```java
boolean x = true;
boolean y = false;

System.out.println(x && y); // false
System.out.println(x || y); // true
System.out.println(!x);     // false
```

---

# 4️⃣ Assignment Operators

👉 Used to assign values

| Operator | Example |
| -------- | ------- |
| =        | a = 10  |
| +=       | a += 5  |
| -=       | a -= 5  |
| *=       | a *= 5  |
| /=       | a /= 5  |

---

## 📌 Example

```java
int a = 10;

a += 5; // a = 15
a -= 3; // a = 12
```

---

# 5️⃣ Unary Operators

👉 Work on single operand

| Operator | Meaning   |
| -------- | --------- |
| +        | Positive  |
| -        | Negative  |
| ++       | Increment |
| --       | Decrement |

---

## 📌 Example

```java
int x = 5;

x++; // 6
x--; // 5
```

---

## 🔥 Pre vs Post Increment

```java
int a = 5;

System.out.println(++a); // 6 (first increase)
System.out.println(a++); // 6 (print then increase)
```

---

# 6️⃣ Bitwise Operators

👉 Work on binary numbers

| Operator | Meaning     |
| -------- | ----------- |
| &        | AND         |
| |        | OR          |
| ^        | XOR         |
| ~        | NOT         |
| <<       | Left Shift  |
| >>       | Right Shift |

---

## 📌 Example

```java
int a = 5;  // 0101
int b = 3;  // 0011

System.out.println(a & b); // 1
System.out.println(a | b); // 7
```

---

# 7️⃣ Ternary Operator

👉 Shortcut for if-else

```java
condition ? value1 : value2;
```

---

## 📌 Example

```java
int a = 10, b = 20;

int max = (a > b) ? a : b;

System.out.println(max); // 20
```

---

# ⚠️ Important Concepts

## 🔥 1. Integer Division

```java
int a = 10 / 3; // Output: 3
```

👉 Decimal part removed

---

## 🔥 2. Operator Precedence

Order of execution:

```
(), ++, -- 
*, /, %
+, -
<, >, <=, >=
==, !=
&&
||
=
```

---

## 🔥 3. Short-Circuit (Logical)

```java
if (false && true) {
    // second condition NOT checked
}
```

---

# 🎯 Real Example

```java
public class Main {
    public static void main(String[] args) {
        int a = 10, b = 5;

        int result = (a + b) * 2;

        boolean check = (a > b) && (b > 0);

        System.out.println(result); // 30
        System.out.println(check);  // true
    }
}
```

---

# 🧠 Final Summary

* Operators perform operations on data
* Types:

  * Arithmetic
  * Relational
  * Logical
  * Assignment
  * Unary
  * Bitwise
  * Ternary
* Important:

  * Precedence matters
  * Integer division removes decimal
  * Short-circuit saves performance

---

