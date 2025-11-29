# 📚 **Java 15: Hidden Classes (JEP 371)**

**Hidden Classes** is a feature introduced in **Java 15** that improves the **dynamic generation of classes** at runtime. These classes are **not discoverable by other classes** and are specifically designed for use by frameworks and libraries that **generate classes on the fly** (e.g., **proxies**, **dynamic bytecode**, and **framework optimizations**).

---

## 🔎 **What Are Hidden Classes?**

A **Hidden Class** is a class that:

- **Cannot be referenced directly by bytecode** from other classes.
- **Does not appear in the classpath**.
- Can only be **accessed reflectively** by the framework or library that created it.
- Can **be unloaded independently**, making memory management more efficient.

Hidden classes are particularly useful for:

- **Dynamic proxy classes**.
- **Mocking frameworks**.
- **Bytecode manipulation libraries** like **ASM** or **Javassist**.
- **Java agents** and **instrumentation tools**.

---

## 🛠 **Key Methods in `java.lang.invoke.MethodHandles` for Hidden Classes**

Java 15 adds new methods to the **`MethodHandles.Lookup`** class to define hidden classes.

| **Method**                                                                                                                           | **Description**                                        |
|--------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| `defineHiddenClass(byte[] bytecode, boolean initialize, MethodHandles.Lookup.ClassOption... options)`                                | Defines a hidden class from bytecode.                  |
| `defineHiddenClassWithClassData(byte[] bytecode, Object classData, boolean initialize, MethodHandles.Lookup.ClassOption... options)` | Defines a hidden class with associated **class data**. |

---

## 🛠 **Example: Creating a Hidden Class Using `MethodHandles`**

Here’s a **simple example** demonstrating how to create and use a **hidden class**.

```java
import java.lang.invoke.MethodHandles;
import java.lang.invoke.MethodHandles.Lookup;
import java.lang.reflect.Method;

public class HiddenClassExample {
    public static void main(String[] args) throws Throwable {
        // Get a Lookup object
        Lookup lookup = MethodHandles.lookup();

        // Bytecode for a simple class (compiled in advance)
        byte[] bytecode = {
            // Simplified bytecode for demo purposes
        };

        // Define the hidden class
        Class<?> hiddenClass = lookup.defineHiddenClass(bytecode, true).lookupClass();

        // Reflectively invoke a method from the hidden class
        Method method = hiddenClass.getDeclaredMethod("sayHello");
        method.invoke(hiddenClass.getDeclaredConstructor().newInstance());
    }
}
```

---

## ⚙️ **Key Properties of Hidden Classes**

| **Property**                     | **Description**                               |
|----------------------------------|-----------------------------------------------|
| **Hidden from the classpath**    | Hidden classes are not listed in classpath.   |
| **Accessible only reflectively** | They can be accessed only through reflection. |
| **Unloadable independently**     | Hidden classes can be unloaded independently. |
| **Can’t be discovered**          | They are not discoverable by other classes.   |

---

## 🧩 **Use Cases for Hidden Classes**

1. **Dynamic Proxies**: Create lightweight, temporary classes to handle dynamic proxies without polluting the classpath.
2. **Mocking Libraries**: Generate classes at runtime for testing purposes.
3. **Instrumentation Tools**: Java agents can modify and monitor application behavior dynamically.
4. **Bytecode Manipulation**: Libraries like **ASM** and **Javassist** can generate and inject classes on the fly.

---

## 🎯 **Benefits of Hidden Classes**

- **Improved Performance**: Reduces classpath pollution and speeds up dynamic class loading.
- **Better Memory Management**: Hidden classes can be unloaded independently, avoiding memory leaks.
- **Enhanced Security**: Hidden classes cannot be accessed by unintended parts of the application.

---

## 📖 **Summary**

| **Feature**    | **Description**                                            |
|----------------|------------------------------------------------------------|
| Hidden Classes | Dynamically generated classes that are not discoverable.   |
| Use Cases      | Dynamic proxies, mocking libraries, bytecode manipulation. |
| Benefits       | Improved performance, memory management, and security.     |

---
