# 📚 **Java Sequenced Collections (Java 21)**

The **Sequenced Collections** feature introduced in **Java 21** provides a unified API for collections that maintain **element order**. It introduces **new interfaces** to manage the **first** and **last elements** of a collection efficiently, making it easier to work with ordered data structures like lists, queues, and sets.

---

## 📖 **What are Sequenced Collections?**

A **Sequenced Collection** is any collection that maintains a **defined iteration order**. The **`SequencedCollection`** interface in Java 21 provides methods to access elements from **both ends of the collection**—the **first** and **last elements**—and to efficiently add or remove elements at these positions.

It introduces **three new interfaces**:

| **Interface**            | **Extends**     | **Description**                                                |
|--------------------------|-----------------|----------------------------------------------------------------|
| `SequencedCollection<E>` | `Collection<E>` | Supports **ordered access** to both ends of a collection.      |
| `SequencedSet<E>`        | `Set<E>`        | An **ordered set** with **no duplicate elements**.             |
| `SequencedMap<K, V>`     | `Map<K, V>`     | An **ordered map** that maintains the insertion order of keys. |

---

## 🎯 **Key Features of Sequenced Collections**

1. **Efficient Access to First and Last Elements**  
   Access and modify the **first** and **last elements** of a collection easily.

2. **Insertion Order Preservation**  
   The iteration order of the elements is based on their **insertion order**.

3. **Bi-Directional Access**  
   You can **add**, **remove**, or **retrieve elements** from both ends of a collection.

---

## 🧩 **New Methods in SequencedCollection**

| **Method**                 | **Description**                                         |
|----------------------------|---------------------------------------------------------|
| `E getFirst()`             | Returns the **first element** of the collection.        |
| `E getLast()`              | Returns the **last element** of the collection.         |
| `void addFirst(E element)` | Adds an element to the **beginning** of the collection. |
| `void addLast(E element)`  | Adds an element to the **end** of the collection.       |
| `E removeFirst()`          | Removes and returns the **first element**.              |
| `E removeLast()`           | Removes and returns the **last element**.               |

---

## 🚀 **Example of `SequencedCollection`**

```java
import java.util.*;

public class SequencedCollectionExample {
    public static void main(String[] args) {
        // Using a LinkedList as a SequencedCollection
        SequencedCollection<String> names = new LinkedList<>();

        names.addFirst("Alice");
        names.addLast("Bob");
        names.addLast("Charlie");

        System.out.println("First: " + names.getFirst());  // Alice
        System.out.println("Last: " + names.getLast());    // Charlie

        names.removeFirst();
        System.out.println("After removing first: " + names); // [Bob, Charlie]
    }
}
```

---

## 🗂 **Example of `SequencedSet`**

```java
import java.util.*;

public class SequencedSetExample {
    public static void main(String[] args) {
        SequencedSet<String> set = new LinkedHashSet<>();

        set.addFirst("Apple");
        set.addLast("Banana");
        set.addLast("Cherry");

        System.out.println("First: " + set.getFirst()); // Apple
        System.out.println("Last: " + set.getLast());   // Cherry

        set.removeLast();
        System.out.println("After removing last: " + set); // [Apple, Banana]
    }
}
```

---

## 📋 **Example of `SequencedMap`**

```java
import java.util.*;

public class SequencedMapExample {
    public static void main(String[] args) {
        SequencedMap<Integer, String> map = new LinkedHashMap<>();

        map.put(1, "One");
        map.put(2, "Two");
        map.put(3, "Three");

        System.out.println("First Entry: " + map.getFirstEntry()); // 1=One
        System.out.println("Last Entry: " + map.getLastEntry());   // 3=Three

        map.removeFirstEntry();
        System.out.println("After removing first entry: " + map);  // {2=Two, 3=Three}
    }
}
```

---

## 🔍 **Common Implementations of Sequenced Collections**

| **Interface**         | **Common Implementations** |
|-----------------------|----------------------------|
| `SequencedCollection` | `LinkedList`, `ArrayDeque` |
| `SequencedSet`        | `LinkedHashSet`, `TreeSet` |
| `SequencedMap`        | `LinkedHashMap`, `TreeMap` |

---

## 📈 **Benefits of Sequenced Collections**

| **Benefit**             | **Description**                                            |
|-------------------------|------------------------------------------------------------|
| 🚀 **Simplifies Code**  | No need for workarounds to access first/last elements.     |
| 📊 **Efficient Access** | Provides efficient methods to access and modify both ends. |
| 🧹 **Cleaner API**      | Makes code more readable and maintainable.                 |
| 💾 **Insertion Order**  | Maintains the order of elements as they were added.        |

---

## 🧪 **Real-World Use Cases**

| **Use Case**               | **Description**                                   |
|----------------------------|---------------------------------------------------|
| 🌐 **Web Applications**    | Managing **user sessions** in a structured order. |
| 🧮 **Data Processing**     | Keeping track of **recently processed items**.    |
| 📄 **Document Management** | Handling **version history** of documents.        |
| 🧩 **Caching Systems**     | Implementing **LRU (Least Recently Used)** cache. |

---

## 📝 **Summary**

| **Feature**          | **Description**                                       |
|----------------------|-------------------------------------------------------|
| **Introduced In**    | Java 21                                               |
| **Main Interfaces**  | `SequencedCollection`, `SequencedSet`, `SequencedMap` |
| **Key Methods**      | `addFirst()`, `addLast()`, `getFirst()`, `getLast()`  |
| **Main Benefit**     | Efficient, ordered access to both ends of collections |
| **Common Use Cases** | Session management, caching, data processing          |

---

Would you like more examples on **SequencedMap use cases** or details on **performance optimizations**? 😊
