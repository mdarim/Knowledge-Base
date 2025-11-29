# 📦 **Java 16 Packaging Tool (`jpackage`)**

Java 16 introduced the **Packaging Tool (`jpackage`)** as a **new tool for creating native installable packages** for Java applications. It is part of the **JEP 392** and aims to **simplify application distribution** by allowing developers to bundle their Java applications with a **runtime environment** into **platform-specific installers**.

The tool is **cross-platform** and supports creating **.exe** for Windows, **.dmg** for macOS, and **.deb/.rpm** packages for Linux.

---

## 📋 **Key Features of `jpackage`**

1. ✅ **Creates native installers** for Java applications (e.g., `.exe`, `.dmg`, `.deb`, `.rpm`).
2. ✅ **Bundles the JRE** to ensure your application runs without requiring a separate JDK installation.
3. ✅ **Supports cross-platform packaging** (Windows, macOS, Linux).
4. ✅ **Allows for custom icons, metadata, and shortcuts**.
5. ✅ **Supports both modular and non-modular applications**.

---

## 🛠 **How to Use `jpackage`**

### **Basic Command Syntax**

```bash
jpackage --input <path-to-jar-files> --name <app-name> --main-jar <main-jar-file> --type <package-type>
```

| **Option**   | **Description**                                               |
|--------------|---------------------------------------------------------------|
| `--input`    | Directory containing the JAR files.                           |
| `--name`     | Name of the application.                                      |
| `--main-jar` | The main JAR file to be executed.                             |
| `--type`     | Type of the package to generate (`exe`, `dmg`, `deb`, `rpm`). |

---

### 🖥️ **Example: Creating a Windows `.exe` Installer**

Assume you have a JAR file named **`MyApp.jar`**.

```bash
jpackage --input target/ --name MyApp --main-jar MyApp.jar --type exe --icon app-icon.ico
```

---

### 📦 **Example: Creating a macOS `.dmg` Installer**

```bash
jpackage --input target/ --name MyApp --main-jar MyApp.jar --type dmg --icon app-icon.icns
```

---

### 🐧 **Example: Creating a Linux `.deb` Installer**

```bash
jpackage --input target/ --name MyApp --main-jar MyApp.jar --type deb
```

---

## ⚙️ **Packaging Options**

| **Option**        | **Description**                                           |
|-------------------|-----------------------------------------------------------|
| `--app-version`   | Version of the application (e.g., `1.0.0`).               |
| `--vendor`        | The name of the application’s vendor.                     |
| `--icon`          | Path to the application icon (`.ico`, `.icns`, `.png`).   |
| `--runtime-image` | Bundles a custom runtime image with the application.      |
| `--main-class`    | Specifies the main class if not included in the JAR file. |
| `--install-dir`   | Directory where the application will be installed.        |

---

## 🔧 **Advanced Example: Creating a Custom Runtime Image with `jlink` and `jpackage`**

1️⃣ First, create a **custom runtime image** using `jlink`.

```bash
jlink --module-path $JAVA_HOME/jmods --add-modules java.base,java.sql --output custom-runtime
```

2️⃣ Then, use `jpackage` to create a native installer with the custom runtime.

```bash
jpackage --input target/ --name MyApp --main-jar MyApp.jar --type exe --runtime-image custom-runtime
```

---

## 🖼 **Adding a Custom Icon**

- **Windows:** Use `.ico` files.
- **macOS:** Use `.icns` files.
- **Linux:** Use `.png` files.

Example command with an icon:

```bash
jpackage --input target/ --name MyApp --main-jar MyApp.jar --type exe --icon app-icon.ico
```

---

## 🧩 **Supported Package Types by Platform**

| **Platform**      | **Package Type** |
|-------------------|------------------|
| Windows           | `.exe`, `.msi`   |
| macOS             | `.dmg`, `.pkg`   |
| Linux             | `.deb`, `.rpm`   |

---

## 🔎 **Use Cases for `jpackage`**

- **Distributing desktop applications**.
- **Bundling the JRE with your app** to avoid version conflicts.
- **Creating self-contained installers** for end-users.
- **Packaging applications for multiple platforms** from a single build system.

---

## 🧪 **Integration with Build Tools (Gradle/Maven)**

To integrate `jpackage` into your **build pipeline**, you can use Gradle or Maven plugins.

### 🔧 **Gradle Example**

```groovy
plugins {
    id 'org.beryx.jlink' version '2.24.0'
}

jlink {
    jpackage {
        imageName = 'MyApp'
        installerType = 'exe'
        appVersion = '1.0.0'
        icon = file('src/main/resources/app-icon.ico')
    }
}
```

### 🧰 **Maven Example**

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jlink-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <jpackage>
            <name>MyApp</name>
            <type>exe</type>
            <icon>${project.basedir}/src/main/resources/app-icon.ico</icon>
        </jpackage>
    </configuration>
</plugin>
```

---

## 🚀 **Benefits of Using `jpackage`**

| **Benefit**                | **Description**                                                  |
|----------------------------|------------------------------------------------------------------|
| No need for a separate JDK | Bundles the JRE with your app.                                   |
| Cross-platform packaging   | Supports Windows, macOS, and Linux installers.                   |
| Custom runtime images      | Allows for minimal custom JRE using `jlink`.                     |
| Simplifies distribution    | Creates native, self-contained installers for easy distribution. |

---

## 🎯 **Summary**

The **Java 16 Packaging Tool (`jpackage`)** is a powerful tool for **packaging Java applications** into native, installable packages. It simplifies the **distribution of desktop applications** by bundling your **Java app and JRE** into a single package that works **out-of-the-box** for end-users, regardless of their platform.
