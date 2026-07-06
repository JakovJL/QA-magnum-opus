# Java Fundamentals

## Table of Contents

- [[#What is Java]]
- [[#JDK vs JRE vs JVM]]
- [[#First Java Program]]
- [[#Package and Import]]
- [[#Naming Conventions]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

> [!info] Video Course (Zaur Tregulov)
> Course 1 (OCA): L1 intro & print, L7 packages & access modifiers, L10 import & comments. JVM/JDK/bytecode is covered deeper in this note.

---

## What is Java

Java is a high-level, object-oriented programming language. Its main feature is platform independence — Java code runs on any device that has a JVM (Java Virtual Machine).

### How it works

1. You write `.java` source files
2. The **Java compiler** (`javac`) compiles them into `.class` files with bytecode
3. The **JVM** interprets or JIT-compiles bytecode into machine code for your OS

> [!info] Platform Independence
> Bytecode is the same on every platform. Only the JVM needs a different version for Windows, Linux, or macOS. This is called **Write Once, Run Anywhere (WORA)**.

---

## JDK vs JRE vs JVM

| Component | Full Name | What it includes |
|---|---|---|
| **JVM** | Java Virtual Machine | Executes bytecode. Core of Java |
| **JRE** | Java Runtime Environment | JVM + standard libraries (`java.lang`, `java.util`, etc.) |
| **JDK** | Java Development Kit | JRE + development tools (`javac`, `jar`, `jdb`) |

> [!tip] Quick Rule
> To **run** Java programs → install JRE. To **write and compile** Java → install JDK. The JDK includes a JRE, so you only need the JDK.

```java
// Check your JDK version in terminal:
// javac -version   →  javac 21.0.1
// java -version    →  openjdk version "21.0.1"
```

---

## First Java Program

Every Java application starts with a `main` method. This is the entry point — the JVM looks for `public static void main(String[] args)` and runs it.

```java
// HelloWorld.java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

**Line by line:**

| Line | Explanation |
|---|---|
| `public class HelloWorld` | Declares a class named `HelloWorld`. File name must match class name |
| `public static void main(...)` | The entry point of the program |
| `String[] args` | Command-line arguments passed when you run the program |
| `System.out.println(...)` | Prints text to the console |

### Compile and Run

```bash
javac HelloWorld.java   # produces HelloWorld.class
java HelloWorld         # runs the bytecode
# Output: Hello, World!
```

---

## Package and Import

A **package** is a folder that groups related classes. It avoids name conflicts and organizes your code.

```java
package com.innowise.training;   // declares which package this class belongs to

import java.util.Scanner;        // imports a specific class
import java.util.*;              // imports all classes from java.util
```

**Convention:** packages use reverse domain name. For Innowise: `com.innowise.training.module2`.

> [!caution] No Package → Default Package
> If you don't declare a package, the class goes into the **default package**. Fine for small examples but bad for real projects. Always use packages.

### Directory Structure

```
src/
└── main/
    └── java/
        └── com/
            └── innowise/
                └── training/
                    └── HelloWorld.java
                    └── utils/
                        └── Helper.java
```

---

## Naming Conventions

| What | Convention | Example |
|---|---|---|
| Class names | PascalCase | `HelloWorld`, `TestRunner`, `LoginPage` |
| Method names | camelCase | `printReport()`, `getUserName()` |
| Variable names | camelCase | `totalCount`, `userName`, `age` |
| Constants | UPPER_SNAKE_CASE | `MAX_SIZE`, `DEFAULT_TIMEOUT` |
| Packages | all lowercase | `com.innowise.training` |

```java
public class UserAccount {                               // PascalCase — class
    private static final int MAX_LOGIN_ATTEMPTS = 3;     // UPPER_SNAKE — constant
    private String userName;                              // camelCase — variable

    public String getUserName() {                         // camelCase — method
        return userName;
    }
}
```

> [!info] Why Conventions Matter
> In automation projects you read other people's code every day. Consistent naming makes the code predictable. When you see `LoginPage` you instantly know it's a class. When you see `getDriver()` you know it's a method.


---

## Interview Questions

### Beginner Questions

**1. What is the difference between JDK, JRE, and JVM?**
JVM (Java Virtual Machine) executes bytecode — it is the core runtime. JRE is JVM + standard class libraries (you need it to *run* Java programs). JDK is JRE + development tools like `javac` and `jar` (you need it to *develop* Java programs). The JDK includes a JRE, so installing the JDK is enough for both development and running.

**2. What does "Write Once, Run Anywhere" mean?**
Java source code is compiled into bytecode (`.class` files). Bytecode is platform-independent — it runs on any device that has a JVM. Only the JVM needs to be platform-specific. So you write the code once and it runs on Windows, Linux, macOS, or any other platform with a JVM.

**3. What is a package in Java?**
A package is a namespace that organizes classes. It prevents naming conflicts, controls access (default access modifier), and makes code easier to manage. Packages use reverse domain naming: `com.innowise.training`.

**4. What are the naming conventions in Java?**
Classes — PascalCase (`HelloWorld`). Methods and variables — camelCase (`getUserName`, `totalCount`). Constants — UPPER_SNAKE_CASE (`MAX_SIZE`). Packages — all lowercase (`com.innowise`).

**5. What happens if you don't declare a package?**
The class goes into the *default package*. This is acceptable for small examples but strongly discouraged in real projects because classes from different projects can collide.

**6. What is the `String[] args` parameter in main?**
It is an array of strings that holds command-line arguments passed when you run the program. For example, `java MyApp arg1 arg2` makes `args[0] = "arg1"` and `args[1] = "arg2"`.

### Intermediate Questions

**1. Why is the main method declared `public static void`?**
`public` — the JVM needs to access it from outside the class. `static` — the JVM calls it without creating an object (there is no object yet when the program starts). `void` — the main method does not return a value to the JVM.

**2. What is the difference between bytecode and machine code?**
Bytecode (`.class` files) is an intermediate representation that the JVM interprets or JIT-compiles. It is not specific to any physical processor. Machine code is native binary instructions for a specific CPU architecture (x86, ARM). Bytecode → JIT compiler → machine code at runtime.

**3. What is the difference between `javac` and `java`?**
`javac` is the Java compiler — it translates `.java` source files into `.class` bytecode files. `java` is the launcher — it starts the JVM and loads the specified class's main method.

**4. Can a Java source file have more than one public class?**
No. Each source file can have at most one `public` class, and the file name must match that class name. You can have multiple non-public classes in the same file.

### Advanced Questions

**1. Is it possible to have a `main` method that is not `public`?**
Yes, it compiles. But the JVM will not find it at startup and you will get a `NoSuchMethodError` at runtime. The JVM specifically looks for `public static void main(String[])`.

**2. Can you compile and run Java code without a `main` method?**
Compile — yes. A class without `main` compiles fine. Run — no. The JVM needs an entry point. However, a class can be run without `main` using JUnit or by being referenced by another class that does have `main`.

**3. If a class is compiled successfully but the `.class` file is deleted, can the class still run?**
No. The JVM executes `.class` files. If the bytecode is missing, the JVM cannot load the class. However, if the class was already loaded into memory by the ClassLoader before the file was deleted, it will continue to work — the JVM reads the `.class` file only once, at class loading time.

**4. Why does Java use both a compiler and an interpreter?**
Java uses a compiler (`javac`) to produce bytecode (portable, platform-independent). At runtime, the JVM uses an interpreter to execute bytecode initially, and a JIT (Just-In-Time) compiler to translate hot paths into native machine code for performance. This hybrid approach gives both portability and speed.

### Code Questions

**1. Explain each line of this program.**

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

- `public class HelloWorld` — declares a class; the file must be named `HelloWorld.java`
- `public static void main(String[] args)` — the entry point; JVM calls it without an object (`static`), from outside the class (`public`), and it returns nothing (`void`)
- `String[] args` — command-line arguments
- `System.out.println(...)` — prints to the console

**2. Will this code compile? If yes, what happens at runtime?**

```java
// File: App.java
public class App {
    private static void main(String[] args) {
        System.out.println("Hi");
    }
}
```

It compiles — visibility of `main` is not checked at compile time. At runtime the JVM does not find `public static void main(String[])` and throws `NoSuchMethodError`. The fix is to make `main` `public`.

