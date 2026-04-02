# ☕ Java Control Flow


## 🧠 What is Control Flow?

Control Flow defines the **order in which statements are executed** in a program.

👉 Simple:

> It controls how your program runs step-by-step

---

## 📌 Example

```java
int a = 10;

if (a > 5) {
    System.out.println("Greater");
}
```

👉 Here, execution depends on a condition

---

# 🔹 Types of Control Flow

```
Control Flow
├── Conditional Statements
├── Looping Statements
└── Jump Statements
```

---

# 1️⃣ Conditional Statements

👉 Used to make decisions

---

## ✅ if Statement

```java
if (condition) {
    // code runs if condition is true
}
```

### Example:

```java
int age = 18;

if (age >= 18) {
    System.out.println("Eligible to vote");
}
```

---

## ✅ if-else Statement

```java
if (condition) {
    // true block
} else {
    // false block
}
```

### Example:

```java
int num = 5;

if (num % 2 == 0) {
    System.out.println("Even");
} else {
    System.out.println("Odd");
}
```

---

## ✅ else-if Ladder

```java
if (condition1) {
} else if (condition2) {
} else {
}
```

### Example:

```java
int marks = 75;

if (marks >= 90) {
    System.out.println("A");
} else if (marks >= 70) {
    System.out.println("B");
} else {
    System.out.println("C");
}
```

---

## ✅ switch Statement

👉 Used when multiple cases exist

```java
switch (value) {
    case 1:
        // code
        break;
    case 2:
        // code
        break;
    default:
        // code
}
```

### Example:

```java
int day = 2;

switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    default:
        System.out.println("Invalid");
}
```

---

# 2️⃣ Looping Statements

👉 Used to repeat code multiple times

---

## 🔁 for Loop

```java
for (initialization; condition; update) {
    // code
}
```

### Example:

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

---

## 🔁 while Loop

```java
while (condition) {
    // code
}
```

### Example:

```java
int i = 1;

while (i <= 5) {
    System.out.println(i);
    i++;
}
```

---

## 🔁 do-while Loop

```java
do {
    // code
} while (condition);
```

### Example:

```java
int i = 1;

do {
    System.out.println(i);
    i++;
} while (i <= 5);
```

👉 Runs at least once

---

# 3️⃣ Jump Statements

👉 Used to control loop execution

---

## 🔹 break

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) break;
    System.out.println(i);
}
```

👉 Stops loop

---

## 🔹 continue

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) continue;
    System.out.println(i);
}
```

👉 Skips current iteration

---

## 🔹 return

```java
return value;
```

👉 Exits method

---

# ⚠️ Important Concepts

## 🔥 1. Infinite Loop

```java
while (true) {
    // runs forever
}
```

---

## 🔥 2. Nested Loops

```java
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        System.out.println(i + " " + j);
    }
}
```

---

## 🔥 3. switch without break

```java
int x = 1;

switch (x) {
    case 1:
        System.out.println("One");
    case 2:
        System.out.println("Two");
}
```

👉 Output:

```
One
Two
```

👉 This is called **fall-through**

---

# 🎯 Real Example

```java
public class Main {
    public static void main(String[] args) {

        for (int i = 1; i <= 5; i++) {

            if (i % 2 == 0) {
                continue;
            }

            System.out.println(i);
        }
    }
}
```

👉 Output:

```
1
3
5
```

---

# 🧠 Final Summary

* Control Flow decides execution order
* Types:

  * Conditional → if, switch
  * Looping → for, while, do-while
  * Jump → break, continue, return
* Important:

  * Loops can be infinite
  * switch has fall-through
  * continue skips, break stops

---


