# Java Memory Concepts
## Arrays, Stack vs Heap, Pass by Value

---

## 1. Arrays in Java

An **array** is a collection of elements of the same data type stored in contiguous memory locations.

### Example:
```java
int[] arr = {10, 20, 30};
```

### Key Points:
- Arrays are **objects** in Java.
- The **reference variable (`arr`) is stored in stack memory**.
- The **actual array elements are stored in heap memory**.

---

## 2. Stack vs Heap Memory

Java uses two main types of memory:

### Stack Memory

Used for:
- Local variables
- Method calls
- References (addresses of objects)

### Example:
```java
int a = 10;
int[] arr = {1, 2, 3};
```

Stored in Stack:
- `a = 10`
- `arr = reference to array in heap`

---

### Heap Memory

Used for:
- Objects
- Arrays

### Example:
```java
int[] arr = {1, 2, 3};
```

Stored in Heap:
- `{1, 2, 3}` (actual data)

---

### Visualization:

```
Stack Memory        Heap Memory
------------        -----------------
a = 10              [1, 2, 3]
arr ------->        (array object)
```

---

## 3. Pass by Value in Java

Java is **always pass by value**.

---

### Case 1: Primitive Data Types

```java
void change(int x) {
    x = 50;
}

int a = 10;
change(a);
System.out.println(a); // Output: 10
```

### Explanation:
- A **copy of the value** is passed to the method.
- Original variable remains unchanged.

---

### Case 2: Objects / Arrays

```java
void change(int[] arr) {
    arr[0] = 100;
}

int[] arr = {1, 2, 3};
change(arr);
System.out.println(arr[0]); // Output: 100
```

### Explanation:
- A **copy of the reference** is passed.
- Both references point to the same object in heap.
- So, changes reflect in the original array.

---

## Important Concept

Even for objects:
- Java passes **reference by value**, not by reference.

---

## Summary Table

| Concept | Description |
|--------|-------------|
| Array | Collection of same type elements (stored in heap) |
| Stack | Stores local variables and references |
| Heap | Stores objects and arrays |
| Pass by Value | Java always passes a copy of value |

---

## Interview Tip

If asked:
> Is Java pass by value or pass by reference?

**Correct Answer:**
Java is always pass by value (even for object references).

---

## End of Notes

