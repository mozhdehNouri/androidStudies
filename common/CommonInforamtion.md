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
