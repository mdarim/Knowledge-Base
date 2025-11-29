In Gradle's Groovy DSL, many configurations and dependencies are defined using a
domain-specific language (DSL) that allows for concise expressions. However, these DSL
constructs are essentially shorthand for underlying method calls. Understanding the
traditional method call equivalents can provide clarity, especially when debugging or
transitioning to more explicit scripting.

**1. Applying Plugins:**

In the DSL, applying a plugin is often done using:

```groovy
apply plugin: 'java'
```

This is a shorthand for the `apply` method, which accepts a map as an argument:

```groovy
apply([plugin: 'java'])
```

Here, `apply` is a method that takes a map with the key `plugin` and the value being the
plugin's ID.

**2. Repositories Configuration:**

Defining repositories using the DSL might look like:

```groovy
repositories {
    mavenCentral()
}
```

This utilizes the `repositories` method, which accepts a closure. Within this closure,
`mavenCentral()` is a method call that adds the Maven Central repository to the project:

```groovy
repositories(new Action<RepositoryHandler>() {
    @Override
    void execute(RepositoryHandler repositories) {
        repositories.mavenCentral()
    }
})
```

In this example, `repositories` is called with an anonymous inner class implementing the
`Action` interface, and `mavenCentral()` is invoked on the `RepositoryHandler`.

**3. Dependencies Configuration:**

Adding dependencies in the DSL is often done as:

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:2.5.4'
}
```

This is a shorthand for invoking the `dependencies` method with a closure:

```groovy
dependencies(new Action<DependencyHandler>() {
    @Override
    void execute(DependencyHandler dependencies) {
        dependencies.add('implementation', 'org.springframework.boot:spring-boot-starter-web:2.5.4')
    }
})
```

Here, `add` is a method on `DependencyHandler` that takes the configuration name and the
dependency notation as arguments.

**4. Defining Tasks:**

Creating a task using the DSL might be written as:

```groovy
task hello {
    doLast {
        println 'Hello, World!'
    }
}
```

This is a shorthand for:

```groovy
tasks.create('hello', new Action<Task>() {
    @Override
    void execute(Task task) {
        task.doLast(new Action<Task>() {
            @Override
            void execute(Task t) {
                println 'Hello, World!'
            }
        })
    }
})
```

In this example, `tasks.create` is used to define a new task named 'hello', and `doLast`
adds an action to be executed at the end of the task.

**5. Setting Properties:**

Assigning properties in the DSL can be done directly:

```groovy
version = '1.0.0'
```

This is equivalent to calling the `setVersion` method:

```groovy
setVersion('1.0.0')
```

Or using the explicit setter syntax:

```groovy
project.setVersion('1.0.0')
```

Here, `setVersion` is a method on the `Project` object that sets the project's version.

**6. java toolchain:**

In Gradle's Groovy DSL, configurations are often expressed using a concise syntax that
omits explicit method calls. To convert such configurations into traditional method calls,
you can expand the shorthand notations into their full method equivalents.

**Original Groovy DSL Configuration:**

```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(23)
    }
}
```

**Expanded Version with Explicit Method Calls:**

```groovy
java(new Action<JavaPluginExtension>() {
    @Override
    void execute(JavaPluginExtension javaPluginExtension) {
        javaPluginExtension.toolchain(new Action<JavaToolchainSpec>() {
            @Override
            void execute(JavaToolchainSpec toolchainSpec) {
                toolchainSpec.setLanguageVersion(JavaLanguageVersion.of(23))
            }
        })
    }
})
```

**Explanation:**

1. **`java` Method:**
    - In the original DSL, `java { ... }` configures the `JavaPluginExtension`.
    - The expanded version calls the `java` method with an `Action<JavaPluginExtension>`
      implementation.
    - This `Action`'s `execute` method receives the `JavaPluginExtension` instance to
      configure.

2. **`toolchain` Method:**
    - Within the `execute` method of the `java` configuration, the `toolchain` method is
      called with an `Action<JavaToolchainSpec>`.
    - This `Action`'s `execute` method receives the `JavaToolchainSpec` instance.

3. **Setting `languageVersion`:**
    - Inside the `execute` method of the `toolchain` configuration, the
      `setLanguageVersion` method is called with `JavaLanguageVersion.of(23)`.
    - This explicitly sets the desired Java language version for the toolchain.

By expanding the shorthand DSL into explicit method calls, you gain a clearer
understanding of the underlying method invocations and the structure of the configuration.
This approach can be particularly helpful for debugging or when transitioning to a more
explicit scripting style.

For more detailed information on configuring Java toolchains in Gradle, refer to
the [Gradle User Manual on Toolchains for JVM Projects](https://docs.gradle.org/current/userguide/toolchains.html).

Understanding these traditional method calls can demystify the Groovy DSL and provide
deeper insights into the mechanics of Gradle build scripts. For more detailed information,
refer to
the [Gradle User Manual on Writing Build Scripts](https://docs.gradle.org/current/userguide/writing_build_scripts.html). 