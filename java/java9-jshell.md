**JShell** is a **Java REPL (Read-Eval-Print Loop)** introduced in **Java 9** that allows developers to quickly write, evaluate, and test Java code snippets without the need to create a full Java class or method. It’s an interactive tool that is particularly useful for experimenting with Java APIs, learning Java, or testing code fragments on the fly.

---

## ✅ **Key Features of JShell:**
- **Instant feedback:** Evaluate Java expressions immediately.
- **Quick prototyping:** No need to create full classes or `main()` methods.
- **Easy to use:** Supports tab completion, history, and shortcuts.
- **Exploratory programming:** Useful for testing APIs, debugging, and learning Java.

---

## ✅ **How to Launch JShell:**

### **Step 1: Open the Terminal and Run JShell**

```bash
jshell
```

After running the command, you’ll see a prompt like this:

```text
|  Welcome to JShell -- Version 23
|  For an introduction type: /help intro
```

---

## ✅ **Basic Commands in JShell:**

### 🔧 **Simple Arithmetic:**
```java
jshell> 2 + 3
$1 ==> 5
```

### 🔧 **Defining Variables:**
```java
jshell> int x = 10;
x ==> 10

jshell> String message = "Hello, JShell!";
message ==> "Hello, JShell!"
```

### 🔧 **Defining Methods:**
```java
jshell> int add(int a, int b) {
   ...> return a + b;
   ...> }
|  created method add(int, int)

jshell> add(5, 3)
$2 ==> 8
```

### 🔧 **Defining Classes:**
```java
jshell> class Person {
   ...> String name;
   ...> int age;
   ...> }
|  created class Person
```

---

## ✅ **JShell Commands:**

| **Command**         | **Description**                          |
|---------------------|------------------------------------------|
| `/help`             | Displays the list of available commands. |
| `/vars`             | Shows all variables defined in the session. |
| `/methods`          | Shows all methods defined in the session. |
| `/classes`          | Shows all classes defined in the session. |
| `/imports`          | Shows all imported packages.             |
| `/list`             | Lists all snippets entered in the session. |
| `/save <file>`      | Saves the session to a file.             |
| `/open <file>`      | Opens a saved session from a file.       |
| `/exit`             | Exits JShell.                            |

---

## ✅ **Example Session in JShell:**

```text
jshell> int num1 = 10;
num1 ==> 10

jshell> int num2 = 20;
num2 ==> 20

jshell> int sum = num1 + num2;
sum ==> 30

jshell> System.out.println("Sum: " + sum);
Sum: 30
```

---

## ✅ **Using JShell for Exploring Java 8+ Features:**

### 🔧 **Example: Using Streams and Lambdas in JShell:**

```java
jshell> List<String> names = List.of("Alice", "Bob", "Charlie");
names ==> [Alice, Bob, Charlie]

jshell> names.stream().filter(name -> name.startsWith("A")).forEach(System.out::println);
Alice
```

---

## ✅ **Saving and Loading Sessions:**

### **Save Session to a File:**
```bash
/jshell> /save mysession.jsh
```

### **Load Session from a File:**
```bash
/jshell> /open mysession.jsh
```

---

## ✅ **JShell Tips:**

1. **Use Tab Completion:** Press **Tab** to auto-complete commands or class names.
2. **Check History:** Use `/list` to view previously entered snippets.
3. **Edit Snippets:** Use `/edit` to modify an existing snippet.
4. **Reset the Environment:** Use `/reset` to clear all defined snippets.

---

## ✅ **Benefits of JShell:**

| **Use Case**           | **Description**                                      |
|------------------------|------------------------------------------------------|
| **Exploratory Coding**  | Quickly try out code snippets without creating a project. |
| **Learning Tool**       | Great for beginners to learn Java concepts interactively. |
| **Debugging**           | Test small pieces of code during development.        |
| **API Testing**         | Quickly test Java APIs and libraries.               |

---

## ✅ **JShell Example: Calculating Factorial**

```java
jshell> int factorial(int n) {
   ...> if (n <= 1) return 1;
   ...> return n * factorial(n - 1);
   ...> }
|  created method factorial(int)

jshell> factorial(5)
$3 ==> 120
```

---

## ✅ **Exiting JShell:**
To exit the JShell session, use the following command:

```bash
/exit
```

---

JShell is an excellent tool for **quick prototyping** and **learning Java concepts** interactively. Let me know if you need more examples or tips! 😊
