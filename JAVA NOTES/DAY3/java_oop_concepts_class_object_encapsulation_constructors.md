# Java OOP Concepts
## Class, Object, Encapsulation, Constructors

---

## 1. Class in Java

A **class** is a blueprint or template used to create objects. It defines properties (variables) and behaviors (methods).

### Example:
```java
class Student {
    String name;
    int age;

    void display() {
        System.out.println(name + " " + age);
    }
}
```

### Key Points:
- A class does not occupy memory until an object is created.
- It can contain variables, methods, constructors, and blocks.

---

## 2. Object in Java

An **object** is an instance of a class. It represents a real-world entity.

### Example:
```java
Student s1 = new Student();
s1.name = "Aditya";
s1.age = 21;
s1.display();
```

### Key Points:
- Object is created using the `new` keyword.
- Each object has its own copy of instance variables.
- Objects are stored in **heap memory**.

---

## 3. Encapsulation

**Encapsulation** is the process of wrapping data (variables) and code (methods) into a single unit and restricting direct access.

### Why Encapsulation?
- To protect data (data hiding)
- To control access using methods
- To make code more secure and maintainable

### Example:
```java
class Student {
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

### Key Points:
- Use `private` for variables
- Use `public` getter and setter methods

---

## 4. Constructors

A **constructor** is a special method used to initialize objects. It is called automatically when an object is created.

### Rules:
- Constructor name must be same as class name
- No return type (not even `void`)

---

### Types of Constructors

#### 1. Default Constructor

```java
class Student {
    String name;

    Student() {
        name = "Unknown";
    }
}
```

#### 2. Parameterized Constructor

```java
class Student {
    String name;

    Student(String name) {
        this.name = name;
    }
}
```

#### 3. Copy Constructor (Custom Implementation)

```java
class Student {
    String name;

    Student(Student s) {
        this.name = s.name;
    }
}
```

---

## Constructor Example

```java
class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    void display() {
        System.out.println(name + " " + age);
    }
}

public class Main {
    public static void main(String[] args) {
        Student s1 = new Student("Aditya", 21);
        s1.display();
    }
}
```

---

## Important Concept: this Keyword

The `this` keyword refers to the current object.

### Example:
```java
this.name = name;
```

Used to differentiate between instance variables and parameters.

---

## Summary Table

| Concept | Description |
|--------|-------------|
| Class | Blueprint to create objects |
| Object | Instance of a class |
| Encapsulation | Data hiding using private variables and public methods |
| Constructor | Special method to initialize objects |

---

## Interview Tips

- Class is a blueprint, object is a real instance.
- Encapsulation = data hiding + controlled access.
- Constructor is automatically called during object creation.
- Java does not have a built-in copy constructor like C++, but it can be implemented manually.

---

## End of Notes

