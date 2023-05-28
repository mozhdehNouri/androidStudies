### Manager Gradle in Multi Modular Application

#### Using the "Build-logic" convention Plugin and Version Catalog instead of buildSrc

the common way to organize gradle plugin and dependecie in modular project is using buildSrc Approch but the BuildSrc has some pitfalls and weak points that i want to mention :

1. Build time: building the "buildSrc" folder can take a significant amount of time, even if none of the plugins defined in "buildSrc" are used in the current build. This can slow down the development process, especially if you are frequently making small changes to the project.
   
   - This is because the "buildSrc" folder is built before any other module in the project, even if none of the plugins defined in "buildSrc" are used in a given build. This can lead to unnecessary build time when working on smaller parts of the project.

2. Code duplication: When using the "buildSrc" folder, it's possible for different modules to define similar logic in their own build scripts. This can lead to duplicated code and potential inconsistencies across the project. By contrast, the "build-logic" approach allows you to define common configurations in a single location, reducing the likelihood of code duplication.

3. IDE integration: The "buildSrc" folder can sometimes cause issues with IDE integration and debugging, especially when using mixed-language projects with Kotlin and Java. This is because "buildSrc" is a separate module from the main project, which can lead to some inconsistencies between the two. With the "build-logic" approach, convention plugins are included as an "included build", which makes it easier to integrate with IDEs and debug the build process.

4. Limited scope: The "buildSrc" folder is limited in scope to custom Gradle plugins, whereas convention plugins can be used to define a wide range of configurations for different modules in the project. This allows for more flexibility in how you define and use plugins in your project.

Comparison between buildSrc and build-logic :

1. Scope: The "buildSrc" folder is intended to be used for defining custom Gradle plugins, while the "build-logic" approach is a more general approach to organizing Gradle projects. Convention plugins defined in "build-logic" can be used to configure all aspects of a project, not just custom plugins.

2. Dependencies: Plugins defined in "buildSrc" are automatically available to all subprojects in a multi-project build, but they are not available to the root project or any other builds outside the current project. Convention plugins defined in "build-logic", on the other hand, are available to any project that includes the "build-logic" project as an included build.

3. Build time: Because the "buildSrc" folder is compiled separately from the main project, it can add significant time to the build process. Convention plugins defined in "build-logic" are included as part of the build process and can be compiled along with the rest of the project, potentially reducing build time.

4. Flexibility: Convention plugins defined in "build-logic" can be designed to be composable and additive, allowing for greater flexibility in configuring different aspects of a project. By contrast, plugins defined in "buildSrc" are typically more focused on specific tasks and may be less flexible in their application.

5. Code duplication: Convention plugins defined in "build-logic" can help to reduce code duplication by providing a single source of truth for common configurations. With "buildSrc", it's possible for multiple subprojects to define similar logic in their own build scripts, leading to duplicated code and potential inconsistencies.

Overall, both approaches have their strengths and weaknesses, and the choice between them will depend on the specific needs of your project. In general, if you only need to define custom Gradle plugins, the "buildSrc" folder may be the better choice. If you need more flexibility and want to avoid code duplication, the "build-logic" approach may be a better fit.

##### what is Convention plugin :

Convention plugins in Android Gradle are a type of plugin that provides a set of conventions and default behaviors for building Android projects. Convention plugins are designed to simplify the build process by providing a predefined structure and set of configurations that developers can use as a starting point for their projects.

Convention plugins are typically included in the Gradle build file using the `apply plugin` syntax, like any other plugin. Once applied, the plugin adds a set of default configurations to the project that follow the conventions set by the plugin. For example, the `com.android.application` plugin is a convention plugin that provides a default configuration for building Android applications.

Some examples of convention plugins for Android Gradle include:

- `com.android.library`: This convention plugin provides a default configuration for building Android libraries.

- `kotlin-android`: This convention plugin provides a default configuration for building Android projects with Kotlin.

- `android-apt`: This convention plugin provides support for using the Android Annotation Processing Tool (APT).

Convention plugins can be very useful for simplifying the build process and reducing the amount of boilerplate code required to set up a new Android project. However, they may not always be suitable for complex projects that require more customization and flexibility in the build process.

what benefits convention plugin provide for our project :

1. Simplifying the build process: Convention plugins provide a set of conventions and default behaviors that simplify the build process for Android projects. By following the conventions set by the plugin, developers can avoid the need to write custom configurations for their projects.

2. Reducing boilerplate code: Convention plugins can help reduce the amount of boilerplate code required to set up a new Android project. This makes it easier and faster to get started with a new project.

3. Encouraging best practices: Convention plugins are designed to encourage best practices for building Android projects. By using a convention plugin, developers can be confident that their project follows established best practices and is set up in a consistent manner.

4. Supporting common tasks: Convention plugins often provide support for common tasks in Android projects, such as building libraries, working with Kotlin, or using annotation processing tools. This can save developers time and effort by providing built-in support for these tasks.

### Using Version catalog :

As projects start to be more complex and modularized, keeping all the dependencies in sync and updating them becomes a hard and annoying task. To solve this, we have a few alternatives and a new one was recently launched by Gradle to make our lives easier.

[Gradle version catalogs](https://docs.gradle.org/current/userguide/platforms.html) enable you to add and maintain dependencies and plugins in a scalable way. Using Gradle version catalogs makes managing dependencies and plugins easier when you have [multiple modules](https://developer.android.com/topic/modularization). Instead of hardcoding dependency names and versions in individual build files and updating each entry whenever you need to upgrade a dependency, you can create a central *version catalog* of dependencies that various modules can reference in a type-safe way with Android Studio assistance.

Version Catalog files are shareable, so it’s even easier to have a standard configuration not only inside it but across multiple projects. The flexibility also supports third-party plugins to automatically update the versions for the latest ones, if we want.

Also, in addition to [composite builds](https://docs.gradle.org/current/userguide/composite_builds.html), it performs better in comparison with the `buildSrc` solution. For instance with `buildSrc`, when we increment a version number the build is cleaned and needs to be rebuilt. This is not the case for Version Catalog.

The Version Catalog is a great new way to handle dependency management on our projects. It makes the maintenance of both libraries and versions easier and also allows all the flexibility that Gradle has to offer. Some Android projects already started migrating and we will start to see more and more code, examples and improvements on this amazing feature.

##### 1. Create the libs.versions.toml file

fist Create libs.versions.toml file is the file that contains all the dependency definitions, such as `versions`, `libraries`, `bundles` and `plugins` definitions.

```toml
[versions]
# Define the dependency versions
kotlin = "1.7.10"
compose = "1.2.1"

[libraries]
# Define the libraries
compose_ui = { module = "androidx.compose.ui:ui", version.ref = "compose" }
compose_material = { module = "androidx.compose.material:material", version.ref = "compose" }
compose_tooling = { module = "androidx.compose.ui:ui-tooling", version.ref = "compose" }
compose_icons = { module = "androidx.compose.material:material-icons-extended", version.ref = "compose" }
```

note :It’s worth mentioning that all the dependency alias are normalized by Gradle. It means that every alias that has `-`, `_` or`.` will be updated to use `.`instead. For instance the alias `compose_ui`, `compose-ui` or `compose.ui` will be normalized as `compose.ui`.

##### 2 - ## Create a dedicated module for plugins

we need a dedicated module. The module does not need a specific name, but just make sure that it won’t conflict with the existing ones in your project.

This module needs to contain at least two files: a `build.gradle(.kts)` and `settings.gradle(.kts)`. The first one is used for regular Gradle configuration and the other is used to link up our `libs.versions.toml` file with the Version Catalog feature. here our module name is "build-logic"

```kt
// build.gradle.kts file "build-logic"

 plugins {
    `kotlin-dsl`
}

repositories {
    mavenCentral()
    google()
}
```

```kt
//setttings.gradle.kts "build-logic"

dependencyResolutionManagement {
    repositories {
        mavenCentral()
    }
    versionCatalogs {
        create("libs") {
            from(files("../gradle/libs.versions.toml"))
        }
    }
}
```

## 3. Setup Composite Build

add includeBuild ("your module name ") in setttings.gradle.kts root directory

```kt
///setttings.gradle.kts   root directory

pluginManagement {
    includeBuild("plugins")
    repositories {
        gradlePluginPortal()
        google()
        mavenCentral()
    }
}
```

The code above sets the plugin management to not only look for dependencies in the remote repositories but also search for them in our `plugins` module.

## 4. Use the Version Catalog

```kt
 dependencies {
    // Adds a single dependency
    implementation libs.kotlin

    // Add a group of dependencies
    implementation libs.bundles.compose
}
```

## Automatically update versions

One of the advantages of the Version Catalog is the ability to use tools to automatically update them. For projects on GitHub, [RenovateBot](https://github.com/renovatebot/renovate) is a great tool to integrate into your pipeline. This bot reads your `libs.versions.toml` and automatically creates Pull Requests to update your dependencies.

 if your project does not support that plugin, a good alternative is the [Version Catalog Update Plugin](https://github.com/littlerobots/version-catalog-update-plugin) to directly apply to the project’s Gradle file. Both plugins are widely configurable and help us keep the project dependencies fresh.

Resource :

https://developer.android.com/build/migrate-to-catalogs#kts

https://proandroiddev.com/using-version-catalog-on-android-projects-82d88d2f79e5

[Leveraging Gradle convention plugins to enhance your build configuration - Satyan Jacquens - YouTube](https://www.youtube.com/watch?v=Fx46ttEfZk8)

https://developer.android.com/build/migrate-to-catalogs#kts

https://github.com/jjohannes/idiomatic-gradle
