Here’s a detailed guide on **advanced Java file handling features** for working with **file contents**, including **reading**, **writing**, **searching**, **processing large files**, and **performing operations on file contents efficiently**.

---

## 📚 **Advanced Java File Content Features**

| **Feature**                  | **Description**                                           | **Key Classes/Methods**             |
|------------------------------|-----------------------------------------------------------|-------------------------------------|
| **Reading Large Files**       | Efficiently read large files without loading the entire content into memory. | `BufferedReader`, `Files.lines()`   |
| **Writing to Files**          | Write to files efficiently and handle exceptions properly. | `BufferedWriter`, `Files.write()`   |
| **File Searching**            | Search for specific content within files.                 | `Files.lines()`, `Pattern`, `Stream`|
| **Processing Large Files**    | Process large files line by line to save memory.          | `Files.newBufferedReader()`, `Stream` |
| **NIO (Non-blocking I/O)**    | Use Java NIO for faster, non-blocking file operations.     | `Files`, `Path`, `FileChannel`      |
| **File Watcher**              | Monitor file changes in real-time.                        | `WatchService`                      |

---

## ✅ **1. Efficient File Reading (Large Files)**

### 🛠 **Using `Files.lines()` (Java 8+)**
Efficiently read large files line by line using **Streams**.

```java
import java.nio.file.*;
import java.util.stream.Stream;

public class ReadLargeFileExample {
    public static void main(String[] args) {
        Path path = Paths.get("largefile.txt");
        try (Stream<String> lines = Files.lines(path)) {
            lines.forEach(System.out::println); // Process each line
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

### 🛠 **Using `BufferedReader` for Large Files**

```java
import java.io.*;

public class BufferedReaderExample {
    public static void main(String[] args) {
        try (BufferedReader br = new BufferedReader(new FileReader("largefile.txt"))) {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line); // Process each line
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **2. Writing to Files Efficiently**

### 🛠 **Using `Files.write()` (Java 7+)**

```java
import java.nio.file.*;
import java.util.List;
import java.util.Arrays;

public class WriteToFileExample {
    public static void main(String[] args) {
        Path path = Paths.get("output.txt");
        List<String> lines = Arrays.asList("Line 1", "Line 2", "Line 3");
        try {
            Files.write(path, lines); // Write lines to the file
            System.out.println("File written successfully!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

### 🛠 **Using `BufferedWriter`**

```java
import java.io.*;

public class BufferedWriterExample {
    public static void main(String[] args) {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"))) {
            bw.write("Line 1\n");
            bw.write("Line 2\n");
            bw.write("Line 3\n");
            System.out.println("File written successfully!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **3. Searching for Content in Files**

### 🛠 **Using `Files.lines()` and Regular Expressions**

```java
import java.nio.file.*;
import java.util.regex.*;
import java.util.stream.*;

public class SearchFileExample {
    public static void main(String[] args) {
        Path path = Paths.get("largefile.txt");
        Pattern pattern = Pattern.compile("error", Pattern.CASE_INSENSITIVE);
        try (Stream<String> lines = Files.lines(path)) {
            lines.filter(pattern.asPredicate())
                 .forEach(System.out::println); // Print lines containing "error"
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **4. Processing Large Files Using Streams**

```java
import java.nio.file.*;
import java.util.stream.Stream;

public class ProcessLargeFileExample {
    public static void main(String[] args) {
        Path path = Paths.get("largefile.txt");
        try (Stream<String> lines = Files.lines(path)) {
            long count = lines.filter(line -> line.contains("INFO")).count();
            System.out.println("Number of INFO logs: " + count);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **5. Using Java NIO for Faster File Handling**

### 🛠 **Using `FileChannel` for Large Files**

```java
import java.io.*;
import java.nio.channels.FileChannel;

public class FileChannelExample {
    public static void main(String[] args) {
        try (FileChannel srcChannel = new FileInputStream("source.txt").getChannel();
             FileChannel destChannel = new FileOutputStream("destination.txt").getChannel()) {
            destChannel.transferFrom(srcChannel, 0, srcChannel.size());
            System.out.println("File copied successfully!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **6. Real-Time File Monitoring Using `WatchService` (Java NIO)**

```java
import java.nio.file.*;

public class FileWatcherExample {
    public static void main(String[] args) {
        try {
            WatchService watchService = FileSystems.getDefault().newWatchService();
            Path path = Paths.get("C:/files");
            path.register(watchService, StandardWatchEventKinds.ENTRY_CREATE, StandardWatchEventKinds.ENTRY_DELETE);

            System.out.println("Watching directory: " + path);

            WatchKey key;
            while ((key = watchService.take()) != null) {
                for (WatchEvent<?> event : key.pollEvents()) {
                    System.out.println("Event kind: " + event.kind() + " - File: " + event.context());
                }
                key.reset();
            }
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **7. Handling Files with Java Streams (Functional Style)**

```java
import java.nio.file.*;
import java.io.IOException;
import java.util.stream.Stream;

public class StreamFileExample {
    public static void main(String[] args) {
        try (Stream<String> lines = Files.lines(Paths.get("largefile.txt"))) {
            lines.filter(line -> line.contains("SUCCESS"))
                 .map(String::toUpperCase)
                 .forEach(System.out::println);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## ✅ **8. File Operations Using Java Lambda Expressions**

### 🛠 **List All Files in a Directory Using Lambda:**

```java
import java.io.File;

public class ListFilesLambda {
    public static void main(String[] args) {
        File folder = new File("C:/files");
        File[] files = folder.listFiles(file -> file.isFile() && file.getName().endsWith(".txt"));

        if (files != null) {
            for (File file : files) {
                System.out.println(file.getName());
            }
        }
    }
}
```

---

## ✅ **9. Advanced File Content Operations Using Apache Commons IO**

If you need **advanced file handling utilities**, consider using **Apache Commons IO**.

**Dependency for Maven:**

```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.11.0</version>
</dependency>
```

### Example: Using `FileUtils`

```java
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;

public class ApacheFileUtilsExample {
    public static void main(String[] args) {
        try {
            File file = new File("output.txt");
            FileUtils.writeStringToFile(file, "Hello, World!", "UTF-8");
            String content = FileUtils.readFileToString(file, "UTF-8");
            System.out.println(content);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

These examples should help you handle advanced file content operations in Java efficiently.
