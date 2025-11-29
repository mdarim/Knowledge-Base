# 🚀 **Java 8 Nashorn JavaScript Engine**

Java 8 introduced the **Nashorn JavaScript Engine**, a **lightweight, high-performance JavaScript engine** that enables developers to execute **JavaScript code from within Java applications**. It replaced the older **Rhino engine** and provided better performance and compliance with the **ECMAScript 5.1 specification**.

---

## ✅ **What is Nashorn?**

- **Nashorn** is a JavaScript engine that is built into the **Java Development Kit (JDK)**.
- It allows you to **embed JavaScript code** in your Java applications.
- You can call **Java methods from JavaScript** and **JavaScript functions from Java**.

---

## ✅ **How to Run JavaScript Code Using Nashorn**

You can run JavaScript code in two ways:

1. **Using `jjs` Command-Line Tool**
2. **Using Nashorn in a Java Program (`ScriptEngine`)**

---

### 🔷 **1. Using `jjs` Command-Line Tool**

The **`jjs`** tool allows you to run JavaScript code directly from the command line.

#### 🖥️ **Example:**
```bash
$ jjs
jjs> print("Hello, Nashorn!");
```

**Output:**
```
Hello, Nashorn!
```

You can also run a **JavaScript file** using the `jjs` tool.

#### 🖥️ **Example:**
```bash
$ jjs myscript.js
```

---

### 🔷 **2. Using Nashorn in a Java Program (`ScriptEngine`)**

The **`ScriptEngineManager`** class allows you to run JavaScript code from a Java application.

#### 📄 **Example: Running JavaScript in Java**

```java
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class NashornExample {
    public static void main(String[] args) {
        // Create a ScriptEngineManager
        ScriptEngineManager manager = new ScriptEngineManager();

        // Get the Nashorn engine
        ScriptEngine engine = manager.getEngineByName("nashorn");

        // Execute JavaScript code
        try {
            engine.eval("print('Hello from Nashorn!');");
        } catch (ScriptException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Hello from Nashorn!
```

---

## ✅ **Passing Java Variables to JavaScript**

You can pass **Java variables** to **JavaScript** using the `put()` method of the `ScriptEngine`.

#### 📄 **Example: Passing Variables**
```java
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class PassVariablesExample {
    public static void main(String[] args) {
        ScriptEngineManager manager = new ScriptEngineManager();
        ScriptEngine engine = manager.getEngineByName("nashorn");

        // Passing Java variables to JavaScript
        engine.put("name", "Alice");
        engine.put("age", 25);

        try {
            engine.eval("print('Name: ' + name);");
            engine.eval("print('Age: ' + age);");
        } catch (ScriptException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Name: Alice
Age: 25
```

---

## ✅ **Calling Java Methods from JavaScript**

Nashorn allows JavaScript to access **Java classes** and **methods**.

#### 📄 **Example: Using Java Methods in JavaScript**
```java
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class JavaMethodExample {
    public static void main(String[] args) {
        ScriptEngineManager manager = new ScriptEngineManager();
        ScriptEngine engine = manager.getEngineByName("nashorn");

        try {
            // Using Java classes in JavaScript
            engine.eval("var javaDate = new java.util.Date();");
            engine.eval("print('Current Date: ' + javaDate);");
        } catch (ScriptException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Current Date: Tue Dec 24 14:30:00 GST 2024
```

---

## ✅ **Calling JavaScript Functions from Java**

You can define a **JavaScript function** and call it from Java using **`Invocable`**.

#### 📄 **Example: Calling JavaScript Functions**
```java
import javax.script.Invocable;
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class CallFunctionExample {
    public static void main(String[] args) {
        ScriptEngineManager manager = new ScriptEngineManager();
        ScriptEngine engine = manager.getEngineByName("nashorn");

        try {
            // Define a JavaScript function
            engine.eval("function greet(name) { return 'Hello, ' + name; }");

            // Invoke the function from Java
            Invocable invocable = (Invocable) engine;
            String result = (String) invocable.invokeFunction("greet", "Alice");
            System.out.println(result);
        } catch (ScriptException | NoSuchMethodException e) {
            e.printStackTrace();
        }
    }
}
```

**Output:**
```
Hello, Alice
```

---

## ✅ **Advantages of Nashorn**

| **Advantage**               | **Description**                                      |
|-----------------------------|------------------------------------------------------|
| High Performance            | Faster than the older Rhino engine.                 |
| Easy Integration            | Seamless integration between Java and JavaScript.    |
| Full Access to Java Classes  | JavaScript code can use Java classes and methods.    |
| Supports Lambda Expressions | Can take advantage of Java 8 lambda expressions.     |

---

## ✅ **Disadvantages of Nashorn**

| **Disadvantage**         | **Description**                                        |
|--------------------------|--------------------------------------------------------|
| Deprecated in Java 11    | Nashorn was marked **deprecated** starting from **Java 11**. |
| Limited ECMAScript Support | Supports up to ECMAScript 5.1 (does not fully support modern JS). |

---

## ✅ **Summary of Key Concepts**

| **Concept**              | **Description**                                       |
|--------------------------|-------------------------------------------------------|
| `ScriptEngine`            | Interface to execute JavaScript code in Java.         |
| `ScriptEngineManager`     | Manages different script engines (e.g., Nashorn).     |
| `Invocable`               | Interface to call JavaScript functions from Java.     |
| `jjs`                     | Command-line tool to execute JavaScript code.         |

---

## ✅ **Real-World Use Cases**

1. **Embedding JavaScript in Java Applications**
2. **Dynamic Code Execution**
3. **Custom Scripting for Business Rules**
4. **Testing and Prototyping**

---

## ✅ **Deprecation of Nashorn**

- **Nashorn was deprecated in Java 11** and **removed in Java 15**.
- **Alternative:**  
  Use **GraalVM** or **JavaScript libraries** like **Rhino** or **Graal.js** for running JavaScript in modern Java applications.

---

### ✅ **Alternatives to Nashorn**

| **JavaScript Engine** | **Description**                                      |
|-----------------------|------------------------------------------------------|
| **Graal.js**          | Part of **GraalVM**, supports modern JavaScript.      |
| **Rhino**             | Older JavaScript engine, still maintained.            |

---
