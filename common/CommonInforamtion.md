common android inforamtion :

### coreLibraryDesugaringEnabled in Gradle

```kts
android {
    //...
    compileOptions {
        coreLibraryDesugaringEnabled true
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    //...
}

dependencies {
    coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:1.1.5'
    // Add any other dependencies required by your app
}
```

`isCoreLibraryDesugaringEnabled` is a flag that is used to enable or disable core library desugaring in your Gradle build. You should set this flag to `true` if you want to use newer Java APIs that are not available on older Android versions.

You should add this flag to your Gradle build when you want to use newer Java APIs in your app and ensure that they work on older Android versions. For example, if you're using Java 8 features such as lambdas or streams in your app and you want to make sure they work on devices running Android 4.4 or lower, you can enable core library desugaring in your Gradle build.

The `sourceCompatibility` and `targetCompatibility` options are also set to `JavaVersion.VERSION_1_8` to ensure that the Java version used by the Kotlin compiler is the same as the Java version used by the Java compiler.

`com.android.tools:desugar_jdk_libs:1.1.5` dependency to the `coreLibraryDesugaring` configuration. This dependency provides the necessary classes and methods to replace calls to newer Java APIs with equivalent code that works on older Android versions.

-----

##### implementation Vs api in gradle :

To understand the `implementation` keyword consider the following example.

**example**

Suppose you have a library called `MyLibrary` that internally uses another library called `InternalLibrary`. Something like this:

```typescript
// 'InternalLibrary' module
public class InternalLibrary {
    public static String giveMeAString(){
        return "hello";
    }
}
```

```typescript
// 'MyLibrary' module
public class MyLibrary {
    public String myString(){
        return InternalLibrary.giveMeAString();
    }
}
```

Suppose the `MyLibrary` `build.gradle` uses `api` configuration in `dependencies{}` like this:

```scss
dependencies {
    api(project(":InternalLibrary"))
}
```

You want to use `MyLibrary` in your code so in your app's `build.gradle` you add this dependency:

```scss
dependencies {
    implementation(project(":MyLibrary"))
}
```

Using the `api` configuration (or deprecated `compile`) you can access `InternalLibrary` in your application code:

```vhdl
// Access 'MyLibrary' (granted)
MyLibrary myLib = new MyLibrary();
System.out.println(myLib.myString());

// Can ALSO access the internal library too (but you shouldn't)
System.out.println(InternalLibrary.giveMeAString());
```

In this way the module `MyLibrary` is potentially "leaking" the internal implementation of something. You shouldn't (be able to) use that because it's not directly imported by you.

The `implementation` configuration was introduced to prevent this. So now if you use `implementation` instead of `api` in `MyLibrary`:

```scss
dependencies {
    implementation(project(":InternalLibrary"))
}
```

you won't be able to call `InternalLibrary.giveMeAString()` in your app code anymore.

When you have a lot of nested dependencies this mechanism can speed up the build a lot. *(Watch the video linked at the end for a full understanding of this)*

- If you are a library mantainer you should use `api` for every dependency which is needed for the public API of your library, while use `implementation` for test dependencies or dependencies which must not be used by the final users.

[Useful article](https://medium.com/mindorks/implementation-vs-api-in-gradle-3-0-494c817a6fa) Showcasing the difference between **implementation** and **api**

**REFERENCES** (This is the same video splitted up for time saving)

[Google I/O 2017 - How speed up Gradle builds (FULL VIDEO)](https://www.youtube.com/watch?v=7ll-rkLCtyk)

[Google I/O 2017 - How speed up Gradle builds (NEW GRADLE PLUGIN 3.0.0 PART ONLY)](https://youtu.be/7ll-rkLCtyk?t=22m21s)

[Google I/O 2017 - How speed up Gradle builds (reference to **1***)](https://youtu.be/7ll-rkLCtyk?t=30m37s)

[Android documentation](https://developer.android.com/studio/preview/features/new-android-plugin-migration.html#new_configurations)

##### another difinition :

In Android development, an API is like a set of rules that allow different parts of a program to work together. It defines how you can use a library in your code.

The implementation of a library is the actual code and resources that make the library work. It's the behind-the-scenes stuff.

When you use the "implementation" keyword in Gradle, it means you only need the library's code to run your app. Other parts of your code can't access the library directly.

But when you use the "api" keyword, it means your code needs the library's code, and you want other parts of your code to be able to use the library too.

So, "implementation" keeps the library private to your code, while "api" makes it accessible to other parts of your code.

----

## Downloadable fonts in compose

----

##### kapt.use.worker.api=false  in Gradle.properties

"kapt.use.worker.api=false" is a setting in the Android Gradle build system that disables a feature called the Gradle Worker API for a tool called KAPT.

KAPT is used for processing annotations in Kotlin projects. By default, KAPT uses a feature called the Gradle Worker API that helps process annotations faster by doing some work in parallel (at the same time).

"kapt.use.worker.api=false" is a setting in the Android Gradle build system that disables a feature called the Gradle Worker API for a tool called KAPT.

KAPT is used for processing annotations in Kotlin projects. By default, KAPT uses a feature called the Gradle Worker API that helps process annotations faster by doing some work in parallel (at the same time).

But keep in mind that disabling the parallel processing feature may slow down the build process, especially for larger projects. So, it's usually best to leave it enabled unless you're experiencing specific problems.

-----

##### Moshi - Kotshi - GSON

###### if you confused to use which one in your project read this :

Moshi, Kotshi, and Gson are all popular Java libraries used for converting Java objects to JSON (serialization) and JSON to Java objects (deserialization). Each library has its own features and advantages. Let's discuss the differences between them:

1. Moshi:
   
   - Developed by Square, Moshi is a lightweight JSON library that focuses on simplicity and speed.
   - It provides automatic JSON serialization and deserialization for Java objects.
   - Moshi supports features like custom type adapters, Kotlin nullability, and lenient parsing.
   - It is known for its excellent performance and low memory footprint.
   - Moshi integrates well with other Square libraries like Retrofit and OkHttp.

2. Kotshi:
   
   - Kotshi is an annotation processor built on top of Moshi, providing additional functionality and convenience.
   - It simplifies the process of writing custom type adapters by generating them automatically based on annotations.
   - Kotshi is primarily used with Kotlin, taking advantage of Kotlin's language features.
   - It offers features like sealed class support, default values for properties, and the ability to customize serialization/deserialization behavior.

3. Gson:
   
   - Gson is a popular JSON library developed by Google.
   - It provides a simple API for JSON serialization and deserialization in Java.
   - Gson supports customization through type adapters and type hierarchy handling.
   - It has a wide range of features, including JSON tree model, streaming API, and support for circular references.
   - Gson has been around for a longer time and has a large user base.

Choosing the "better" library depends on your specific requirements and preferences. Here are some considerations:

- If simplicity, speed, and low memory footprint are crucial factors, Moshi might be a good choice.
- If you're working with Kotlin and prefer a convenient annotation-based approach, Kotshi can be beneficial.
- If you need a more mature library with a wider range of features and a large community, Gson might be preferable.

----

##### different between  Okhttp and Retrofit and what is the relationship between them?

Retrofit and OkHttp are two libraries used for network communication in Android.

OkHttp is a powerful HTTP client library that handles low-level network operations like making HTTP requests and managing connections.

Retrofit is built on top of OkHttp and provides a higher-level API for making network requests. It simplifies the process by allowing you to define API endpoints and handle responses using annotations. Retrofit uses OkHttp under the hood to perform the actual network communication.

In simple terms, OkHttp does the heavy lifting of network operations, while Retrofit adds a layer of abstraction to make it easier to work with APIs in Android applications.

----

##### hilt providing note :

1- To provide DAO we don't need to put @Singleton annotaion

2- To provide Dispatcher we don't need to put @Singleton annotaion but we provide @Qualifier like annotation class IoDispatcher beacuse In the context of using `@Qualifier` with `CoroutineDispatcher` in Android development, it allows you to provide different dispatchers for different use cases, such as I/O operations, computation-heavy tasks, or main thread interactions. For example, you can define a custom qualifier annotation called `@IoDispatcher` using `@Qualifier` as follows:

```kt
@Qualifier
@Retention(AnnotationRetention.RUNTIME)
annotation class IoDispatcher
```

By using this annotation, you can annotate a `CoroutineDispatcher` parameter to indicate that it should be injected with a dispatcher specifically suited for I/O operations. This provides a way to differentiate between dispatchers meant for different purposes.

3 - if our component have @InstallIn(SingletonComponent::class) but the function don't have @Singleton the component is provide in whole application but every time we call a function create a new instance of it

---

##### when using SaveStateHandle in viewmodel class

imaging you have an id or a search parameter and send it as fragment or activity to ViewModel if the app proccess will kill for any reason and you return app from background you app will crash beause its don't have parameter 
for this reason you have to use SaveStateHandle and save your id in there 
