[//]: # (title: Create a Spring Boot project with Kotlin)

<microformat>
    <p>This is the first part of the <strong>Getting started with Spring Boot and Kotlin</strong> tutorial:</p><br/>
    <p><img src="icon-1.svg" width="20" alt="First step"/> <strong>Create a Spring Boot project with Kotlin</strong><br/><img src="icon-2-todo.svg" width="20" alt="Second step"/> Add a data class to the Spring Boot project<br/><img src="icon-3-todo.svg" width="20" alt="Third step"/> Add database support for Spring Boot project<br/><img src="icon-4-todo.svg" width="20" alt="Fourth step"/> Use Spring Data CrudRepository for database access<br/></p>
</microformat>


This tutorial walks you through the process of creating a simple application with Spring Boot and adding a database
to store the information.

## Before you start

Download and install the latest version of [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html).

## Create the Spring Boot project

Create a new Spring Boot project with Kotlin by using the project wizard in IntelliJ IDEA Ultimate Edition:

> You can also create a new project using [IntelliJ IDEA with the Spring Boot plugin](https://www.jetbrains.com/help/idea/spring-boot.html)
>
{type="note"}

1. In IntelliJ IDEA, select **File** | **New** | **Project**. 
2. In the panel on the left, select **New Project** | **Spring Initializr**.
3. Specify the following fields and options in the project wizard window:
   * **Name**: demo
   * **Language**: Kotlin
   * **Build system**: Gradle
   * **JDK**: Java 17 JDK
     
     > Use **Amazon Corretto version 17** for this tutorial.
     >
     {type="note"}
   
   * **Java**: 17

   ![Create Spring Boot project](create-spring-boot-project.png){width=800}

4. Ensure that all the fields are specified and click **Next**.

5. Select the following dependencies that will be required for the tutorial:
   * **Spring Web**
   * **Spring Data JDBC**
   * **H2 Database**

   ![Set up Spring Boot project](set-up-spring-boot-project.png){width=800}

6. Click **Create** to generate and set up the project.

The IDE will generate and open the new project. It may take some time to download and import the project dependencies.
After this, you can observe the following structure in the **Project view**:

![Set up Spring Boot project](spring-boot-project-view.png){width=400}

The generated Gradle project corresponds to Maven’s standard directory layout.
There are packages and classes under the `main/kotlin` folder that belong to the application.
The entry point to the application is the `main()` method of the `DemoApplication.kt` file.

## Explore the project Gradle build file {initial-collapse-state="collapsed"}

Open the `build.gradle.kts` file. `build.gradle.kts` is the Gradle Kotlin build script, which contains a list of the dependencies required for the application.

The Gradle file is standard for Spring Boot, but it also contains necessary Kotlin dependencies, including the kotlin-spring Gradle plugin – `kotlin("plugin.spring")`.

Here is the full script with the explanation of all parts and dependencies:

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile // for `KotlinCompile` task below

plugins { 
    id("org.springframework.boot") version "2.7.1"
    id("io.spring.dependency-management") version "1.0.11.RELEASE"
    kotlin("jvm") version "%kotlinVersion%" // The version of Kotlin to use
    kotlin("plugin.spring") version "1.6.21" // The Kotlin Spring plugin
}

group = "com.example"
version = "0.0.1-SNAPSHOT" 
java.sourceCompatibility = JavaVersion.VERSION_17 

repositories {
    mavenCentral()
}

dependencies { 
    implementation("org.springframework.boot:spring-boot-starter-data-jdbc") 
    implementation("org.springframework.boot:spring-boot-starter-web") 
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin") // Jackson extensions for Kotlin for working with JSON
    implementation("org.jetbrains.kotlin:kotlin-reflect") // Kotlin reflection library, required for working with Spring
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8") // Kotlin standard library
    runtimeOnly("com.h2database:h2") 
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}

tasks.withType<KotlinCompile> { // Settings for `KotlinCompile` tasks
    kotlinOptions { // Kotlin compiler options
        freeCompilerArgs = listOf("-Xjsr305=strict") // `-Xjsr305=strict` enables the strict mode for [JSR-305 annotations](java-interop.md#jsr-305-support)
        jvmTarget = "17" // This option specifies the target version of the generated JVM bytecode
    }
}

tasks.withType<Test> { 
    useJUnitPlatform()
}
```

As you can see, there are a few Kotlin-related artifacts added to the Gradle build file.

In the `plugins` block, there are two Kotlin artifacts:

* `kotlin("jvm")` – the plugin defines the version of Kotlin to be used in the project
* `kotlin("plugin.spring")` – Kotlin Spring compiler plugin for adding the open modifier to Kotlin classes in order to make them compatible with Spring Framework features.

In the `dependencies` block, you can see a few Kotlin-related modules listed:

* `com.fasterxml.jackson.module:jackson-module-kotlin` - the module adds support for serialization and deserialization of Kotlin classes and data classes.
* `org.jetbrains.kotlin:kotlin-reflect` - Kotlin reflection library
* `org.jetbrains.kotlin:kotlin-stdlib-jdk8` - Kotlin’s standard library

After the dependencies section, you can see the `KotlinComiple` task configuration block. This is where you can add extra arguments to the compiler to enable or disable various language features.

## Explore the generated Spring Boot application

Open the `DemoApplication.kt` file:

```kotlin
package demo

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
    runApplication<DemoApplication>(*args)
}
```

<deflist collapsible="true">
   <def title="Declaring classes – class DemoApplication">
      <p>Right after package declaration and import statements you can see the first class declaration, <code>class DemoApplication</code>.</p>
      <p>In Kotlin, if a class doesn’t include any members (properties of functions), you can omit the class body (<code>{}</code>) for good.</p>
   </def>
   <def title="@SpringBootApplication annotation">
      <p><a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.using-the-springbootapplication-annotation"><code>@SpringBootApplication annotation</code></a> is a convenience annotation in a Spring Boot application.
      It enables Spring Boot’s <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.auto-configuration">auto-configuration</a>, <a href="https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html">component scan</a>, and be able to define extra configuration on their "application class".
      </p>
   </def>
   <def title="Program entry point – main()">
      <p>The <a href="basic-syntax.md#program-entry-point"><code>main()</code></a> function is the entry point to the application.</p>
      <p>It is declared as a <a href="functions.md#function-scope">top-level function</a> outside the <code>DemoApplication</code> class. The main function invokes Spring’s <code>runApplication(&amp;args)</code> function to start the application with Spring Framework.</p>
   </def>
   <def title="Variable arguments – args: Array&lt;String&gt;">
      <p>If you check the declaration of <code>runApplication()</code> function, you will see that the parameter of the function is marked with <a href="functions.md#variable-number-of-arguments-varargs"><code>vararg</code> modifier</a>: <code>vararg args: String</code>.
        This means that you can pass a variable number of String arguments to the function.
      </p>
   </def>
   <def title="The spread operator – (*args)">
      <p>The <code>args</code> is a parameter to the main function declared as an array of Strings.
        Since there is an array of strings, and you want to pass its contents to the function, use the spread operator (prefix the array with <code>*</code>).
      </p>
   </def>
</deflist>


## Create a controller

In the Spring application, a controller is used to handle the web requests.

In the `DemoApplication.kt` file, create the `MessageResource` class as follows:

```kotlin
@RestController
class MessageResource {
    @GetMapping
    fun index(@RequestParam("name") name: String) = "Hello, $name!"
}
```

<deflist collapsible="true">
   <def title="@RestController annotation">
      <p>You need to tell Spring that <code>MessageResource</code> is a REST Controller, so you should mark it with the <code>@RestController</code> annotation.</p>
      <p>This annotation means this class will be picked up by the component scan because it's in the same package as our <code>DemoApplication</code> class.</p>
   </def>
   <def title="@GetMapping annotation">
      <p><code>@GetMapping</code> marks the functions of the REST controller that implement the endpoints corresponding to HTTP GET calls:</p>
      <code style="block" lang="kotlin">
      @GetMapping
      fun index(@RequestParam("name") name: String) = "Hello, $name!"
      </code>
   </def>
   <def title="@RequestParam annotation">
      <p>The function parameter <code>name</code> is marked with <code>@RequestParam</code> annotation.This annotation indicates that a method parameter should be bound to a web request parameter.</p>
      <p>Hence, if you access the application at the root and supply a request parameter called "name", like <code>/?name=&lt;your-value&gt;</code>, the parameter value will be used as an argument for invoking the <code>index()</code> function.</p>
   </def>
   <def title="Single-expression functions – index()">
      <p>Since the <code>index()</code> function contains only one statement you can declare it as a <a href="functions.md#single-expression-functions">single-expression function</a>.</p>
      <p>This means the curly braces can be omitted and the body is specified after the equals <code>=</code> sign.</p>
   </def>
   <def title="Type inference for function return types">
      <p>The <code>index()</code> function does not declare the return type explicitly. Instead, the compiler infers the return type by looking at the result of the statement on the right-hand side from the equals sign <code>=</code>.</p>
      <p>The type of <code>Hello, $name!</code> expression is <code>String</code>, hence the return type of the function is also <code>String</code>.</p>
   </def>
   <def title="String templates – $name">
      <p><code>Hello, $name!</code> expression is called a <a href="strings.md#string-templates"><i>String template</i></a> in Kotlin.</p>
      <p>String templates are String literals that contain embedded expressions.</p>
      <p>This is a convenient replacement for String concatenation operations.</p>
   </def>
</deflist>

## Run the application

The application is now ready to run.

Click the green Run icon in the gutter beside the `main()` method:

![Run Spring Boot application](run-spring-boot-application.png){width=706}

> You can also run the `./gradlew bootRun` command in the terminal.
>
{type="tip"}

Once the application starts, open the following URL: [http://localhost:8080?name=John](http://localhost:8080?name=John).   
You should see "Hello, John!" printed as a response.

## What's next

You ready to upgrade the project: [proceed to the next chapter](jvm-spring-boot-add-data-class.md)