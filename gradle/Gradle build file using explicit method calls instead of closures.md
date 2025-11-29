Here’s a guide on **rewriting Gradle build file closures to traditional method calls**. This approach can help you understand how closures work internally by breaking them down into **explicit method calls**.

---

## 📋 **Gradle Build File with Closures (Typical)**

Let's start with a sample **Gradle build file** using closures:

```groovy
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
}

tasks.register('hello') {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

We’ll now **rewrite each closure-based block** into **explicit method calls**.

---

## 🛠 **1. Plugins Block: Using Method Calls**

### ✅ **Closure-based:**

```groovy
plugins {
    id 'java'
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
plugins.apply('java')
```

---

## 🛠 **2. Repositories Block: Using Method Calls**

### ✅ **Closure-based:**

```groovy
repositories {
    mavenCentral()
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
repositories.add(repositories.mavenCentral())
```

Alternatively, if using a specific `RepositoryHandler` object:

```groovy
RepositoryHandler handler = repositories
handler.mavenCentral()
```

---

## 🛠 **3. Dependencies Block: Using Method Calls**

### ✅ **Closure-based:**

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
dependencies.add('implementation', 'org.springframework.boot:spring-boot-starter')
```

Alternatively:

```groovy
DependencyHandler handler = dependencies
handler.add('implementation', 'org.springframework.boot:spring-boot-starter')
```

---

## 🛠 **4. Task Registration: Using Method Calls**

### ✅ **Closure-based:**

```groovy
tasks.register('hello') {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
Task helloTask = tasks.register('hello').get()
helloTask.doLast(new Action<Task>() {
    @Override
    void execute(Task task) {
        System.out.println('Hello, Gradle!');
    }
})
```

Alternatively, using a lambda (if using Groovy 3+ or Java 8+):

```groovy
tasks.register('hello').get().doLast(task -> System.out.println('Hello, Gradle!'))
```

---

## 🛠 **5. Applying Plugins: Using Method Calls**

### ✅ **Closure-based:**

```groovy
plugins {
    id 'application'
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
plugins.apply('application')
```

---

## 🛠 **6. Multi-Task Configuration: Using Method Calls**

### ✅ **Closure-based:**

```groovy
tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
tasks.withType(JavaCompile.class).configureEach(new Action<JavaCompile>() {
    @Override
    void execute(JavaCompile javaCompile) {
        javaCompile.getOptions().setEncoding('UTF-8');
    }
});
```

Alternatively, using a lambda:

```groovy
tasks.withType(JavaCompile.class).configureEach(javaCompile -> {
    javaCompile.getOptions().setEncoding('UTF-8');
});
```

---

## 🛠 **7. Conditional Logic in Tasks: Using Method Calls**

### ✅ **Closure-based:**

```groovy
tasks.register('conditionalTask') {
    doLast {
        if (project.hasProperty('env')) {
            println "Running in ${project.env} environment"
        } else {
            println "No environment specified"
        }
    }
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
Task conditionalTask = tasks.register('conditionalTask').get()
conditionalTask.doLast(new Action<Task>() {
    @Override
    void execute(Task task) {
        if (project.hasProperty('env')) {
            System.out.println("Running in " + project.property('env') + " environment");
        } else {
            System.out.println("No environment specified");
        }
    }
});
```

Alternatively, using a lambda:

```groovy
tasks.register('conditionalTask').get().doLast(task -> {
    if (project.hasProperty('env')) {
        System.out.println("Running in " + project.property('env') + " environment");
    } else {
        System.out.println("No environment specified");
    }
});
```

---

## 🛠 **8. Custom Task Actions: Using Method Calls**

### ✅ **Closure-based:**

```groovy
tasks.register('buildProject') {
    doFirst {
        println 'Setting up the project...'
    }
    doLast {
        println 'Build completed successfully!'
    }
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
Task buildProjectTask = tasks.register('buildProject').get()
buildProjectTask.doFirst(new Action<Task>() {
    @Override
    void execute(Task task) {
        System.out.println("Setting up the project...");
    }
})
buildProjectTask.doLast(new Action<Task>() {
    @Override
    void execute(Task task) {
        System.out.println("Build completed successfully!");
    }
})
```

Alternatively, using lambdas:

```groovy
tasks.register('buildProject').get()
    .doFirst(task -> System.out.println("Setting up the project..."))
    .doLast(task -> System.out.println("Build completed successfully!"));
```

---

## 🛠 **9. Configuring All Tasks: Using Method Calls**

### ✅ **Closure-based:**

```groovy
tasks.all {
    doLast {
        println "Task ${name} completed"
    }
}
```

### 🔄 **Rewritten to Traditional Method Calls:**

```groovy
tasks.all(new Action<Task>() {
    @Override
    void execute(Task task) {
        task.doLast(new Action<Task>() {
            @Override
            void execute(Task t) {
                System.out.println("Task " + t.getName() + " completed");
            }
        });
    }
});
```

Alternatively, using lambdas:

```groovy
tasks.all(task -> {
    task.doLast(t -> System.out.println("Task " + t.getName() + " completed"));
});
```

---

## 📝 **Summary of Traditional Method Calls**

| **Closure-Based Syntax**                                     | **Traditional Method Calls**                                                                                    |
|--------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| `plugins { id 'java' }`                                      | `plugins.apply('java')`                                                                                         |
| `repositories { mavenCentral() }`                            | `repositories.add(repositories.mavenCentral())`                                                                 |
| `dependencies { implementation 'lib' }`                      | `dependencies.add('implementation', 'lib')`                                                                     |
| `tasks.register('task') { doLast { println 'Hello' } }`      | `tasks.register('task').get().doLast(task -> System.out.println('Hello'))`                                      |
| `tasks.withType(JavaCompile) { options.encoding = 'UTF-8' }` | `tasks.withType(JavaCompile.class).configureEach(javaCompile -> javaCompile.getOptions().setEncoding('UTF-8'))` |

---

## Complete Example 

```groovy
// Traditional Gradle build file using explicit method calls instead of closures

import org.gradle.api.Action
import org.gradle.api.tasks.compile.JavaCompile
import org.gradle.api.tasks.Task

// Apply Plugins
plugins.apply("java")

// Configure Repositories
repositories.add(repositories.mavenCentral())

// Closure-based:
// repositories {
//     mavenCentral()
// }

// Corresponding Traditional Method Call
repositories.add(repositories.mavenCentral())

// Configure Dependencies
dependencies.add("implementation", "org.springframework.boot:spring-boot-starter")

dependencies.add("testImplementation", "org.junit.jupiter:junit-jupiter-api:5.9.3")
dependencies.add("testRuntimeOnly", "org.junit.jupiter:junit-jupiter-engine:5.9.3")

// Closure-based:
// dependencies {
//     implementation "org.springframework.boot:spring-boot-starter"
//     testImplementation "org.junit.jupiter:junit-jupiter-api:5.9.3"
//     testRuntimeOnly "org.junit.jupiter:junit-jupiter-engine:5.9.3"
// }

// Corresponding Traditional Method Call
dependencies.add("implementation", "org.springframework.boot:spring-boot-starter")
dependencies.add("testImplementation", "org.junit.jupiter:junit-jupiter-api:5.9.3")
dependencies.add("testRuntimeOnly", "org.junit.jupiter:junit-jupiter-engine:5.9.3")

// Register a custom task
Task helloTask = tasks.register("hello").get()
helloTask.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Hello, Gradle!")
}
})

// Closure-based:
// tasks.register("hello") {
//     doLast {
//         println "Hello, Gradle!"
//     }
// }

// Corresponding Traditional Method Call
Task helloTaskTraditional = tasks.register("hello").get()
helloTaskTraditional.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Hello, Gradle!")
}
})

// Register a build task with multiple actions
Task buildTask = tasks.register("buildProject").get()
buildTask.doFirst(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Setting up the project...")
}
})
buildTask.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Build completed successfully!")
}
})

// Closure-based:
// tasks.register("buildProject") {
//     doFirst {
//         println "Setting up the project..."
//     }
//     doLast {
//         println "Build completed successfully!"
//     }
// }

// Corresponding Traditional Method Call
Task buildProjectTaskTraditional = tasks.register("buildProject").get()
buildProjectTaskTraditional.doFirst(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Setting up the project...")
}
})
buildProjectTaskTraditional.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Build completed successfully!")
}
})

// Dynamic task creation based on environment properties
if (project.hasProperty("env")) {
Task deployTask = tasks.register("deploy").get()
deployTask.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Deploying to " + project.property("env") + " environment")
}
})
}

// Closure-based:
// if (project.hasProperty("env")) {
//     tasks.register("deploy") {
//         doLast {
//             println "Deploying to ${project.env} environment"
//         }
//     }
// }

// Corresponding Traditional Method Call
if (project.hasProperty("env")) {
Task deployTaskTraditional = tasks.register("deploy").get()
deployTaskTraditional.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Deploying to " + project.property("env") + " environment")
}
})
}

// Configure all JavaCompile tasks
tasks.withType(JavaCompile.class).configureEach(new Action<JavaCompile>() {
@Override
void execute(JavaCompile javaCompile) {
javaCompile.getOptions().setEncoding("UTF-8")
}
})

// Closure-based:
// tasks.withType(JavaCompile) {
//     options.encoding = "UTF-8"
// }

// Corresponding Traditional Method Call
tasks.withType(JavaCompile.class).configureEach(new Action<JavaCompile>() {
@Override
void execute(JavaCompile javaCompile) {
javaCompile.getOptions().setEncoding("UTF-8")
}
})

// Apply a task configuration to all tasks
tasks.all(new Action<Task>() {
@Override
void execute(Task task) {
task.doLast(new Action<Task>() {
@Override
void execute(Task t) {
System.out.println("Task " + t.getName() + " completed")
}
})
}
})

// Closure-based:
// tasks.all {
//     doLast {
//         println "Task ${name} completed"
//     }
// }

// Corresponding Traditional Method Call
tasks.all(new Action<Task>() {
@Override
void execute(Task task) {
task.doLast(new Action<Task>() {
@Override
void execute(Task t) {
System.out.println("Task " + t.getName() + " completed")
}
})
}
})

// Custom validation task
Task validateTask = tasks.register("validateInput").get()
validateTask.doLast(new Action<Task>() {
@Override
void execute(Task task) {
if (!project.hasProperty("username")) {
throw new GradleException("Username property is required.")
} else {
System.out.println("Hello, " + project.property("username") + "!")
}
}
})

// Closure-based:
// tasks.register("validateInput") {
//     doLast {
//         if (!project.hasProperty("username")) {
//             throw new GradleException("Username property is required.")
//         } else {
//             println "Hello, ${project.username}!"
//         }
//     }
// }

// Corresponding Traditional Method Call
Task validateInputTaskTraditional = tasks.register("validateInput").get()
validateInputTaskTraditional.doLast(new Action<Task>() {
@Override
void execute(Task task) {
if (!project.hasProperty("username")) {
throw new GradleException("Username property is required.")
} else {
System.out.println("Hello, " + project.property("username") + "!")
}
}
})

// Run tests with JUnit
Task testTask = tasks.register("test").get()
testTask.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Running tests...")
}
})

// Closure-based:
// tasks.register("test") {
//     doLast {
//         println "Running tests..."
//     }
// }

// Corresponding Traditional Method Call
Task testTaskTraditional = tasks.register("test").get()
testTaskTraditional.doLast(new Action<Task>() {
@Override
void execute(Task task) {
System.out.println("Running tests...")
}
})
```

