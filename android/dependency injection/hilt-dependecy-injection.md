### Hilt

**Dependency Injection (DI)** is a design pattern to decouple the conventional dependency relationship between objects. When it comes to DI in android [**Dagger**](https://www.geeksforgeeks.org/dependency-injection-with-dagger-2-in-android/) always takes a lead. But it is very complex and requires a lot of boilerplate codes in order to set up the Dagger. So, to overcome this problem **Hilt** was introduced. Dagger Hilt simplifies the whole process and reduces the unnecessary steps without losing any of the features of the original Dagger.

Hilt is a dependency injection library for Android that reduces the boilerplate of doing manual dependency injection in your project. Doing manual dependency injection requires you to construct every class and its dependencies by hand, and to use containers to reuse and manage dependencies.

Hilt provides a standard way to use DI in your application by providing containers for every Android class in your project and managing their lifecycles automatically. Hilt is built on top of the popular DI library [Dagger](https://developer.android.com/training/dependency-injection/dagger-basics) to benefit from the compile-time correctness, runtime performance, scalability, and [Android Studio support](https://medium.com/androiddevelopers/dagger-navigation-support-in-android-studio-49aa5d149ec9) that Dagger provides. 

Hilt is built on top of the [Dagger](https://dagger.dev/) dependency injection library, providing a standard way to incorporate Dagger into an Android application.

With respect to Dagger, the goals of Hilt are as follows:

- To simplify Dagger-related infrastructure for Android apps.
- To create a standard set of components and scopes to ease setup, readability, and code sharing between apps.
- To provide an easy way to provision different bindings to various build types, such as testing, debug, or release.

### Defining and injecting dependencies

When you write code that uses dependency injection, there are two major components to think about:

1. Classes that have dependencies that you want to inject.
2. Classes that can be injected as dependencies.

These are not mutually exclusive and, in many cases, your class is both injectable and has dependencies.

## Make a dependency injectable

To make something injectable in Hilt, you must tell Hilt how to create an instance of that thing. These instructions are called *bindings*.

There are three ways to define a binding in Hilt.

1. Annotate the constructor with `@Inject`
2. Use `@Binds` in a module
3. Use `@Provides` in a module

 **Annotate the constructor with** `**@Inject**`

Any class can have a constructor annotated with `@Inject` which makes it available as a dependency anywhere in your project.

```kt
class OatMilk @Inject constructor() {
  ...
}
```

#### 1- Hilt application class

All apps that use Hilt must contain an [`Application`](https://developer.android.com/reference/android/app/Application) class that is annotated with `@HiltAndroidApp`.

`@HiltAndroidApp` triggers Hilt's code generation, including a base class for your application that serves as the application-level dependency container. what is that mean ?

- When you apply the `@HiltAndroidApp` annotation to your application class, it triggers Hilt's code generation process. Hilt then generates a base class for your application that acts as the application-level dependency container.

- In simpler terms, the `@HiltAndroidApp` annotation is used to enable Hilt in your Android application. It sets up the necessary infrastructure for dependency injection by generating code behind the scenes. This generated code includes a base class that handles the creation and management of dependencies throughout your application.

- By using Hilt's generated base class, you can easily access and use dependencies within your application components, such as activities, fragments, and services, without explicitly instantiating them yourself. Hilt takes care of providing the required dependencies wherever they are needed, making it easier to manage and test your application's dependencies.

```kt
@HiltAndroidApp
class ExampleApplication : Application() { ... }
```

This generated Hilt component is attached to the `Application` object's lifecycle and provides dependencies to it. Additionally, it is the parent component of the app, which means that other components can access the dependencies that it provides.

## Inject dependencies into Android classes

Once Hilt is set up in your `Application` class and an application-level component is available, Hilt can provide dependencies to other Android classes that have the `@AndroidEntryPoint` annotation: 

```kt
@AndroidEntryPoint
class ExampleActivity : AppCompatActivity() { ... }
```

Hilt currently supports the following Android classes:

- `Application` (by using `@HiltAndroidApp`)
- `ViewModel` (by using `@HiltViewModel`)
- `Activity`
- `Fragment`
- `View`
- `Service`
- `BroadcastReceiver`

If you annotate an Android class with `@AndroidEntryPoint`, then you also must annotate Android classes that depend on it. For example, if you annotate a fragment, then you must also annotate any activities where you use that fragment.

**Note:** The following exceptions apply to Hilt support for Android classes:

- Hilt only supports activities that extend [`ComponentActivity`](https://developer.android.com/reference/kotlin/androidx/activity/ComponentActivity), such as [`AppCompatActivity`](https://developer.android.com/reference/kotlin/androidx/appcompat/app/AppCompatActivity).
- Hilt only supports fragments that extend `androidx.Fragment`.
- Hilt does not support retained fragments.

`@AndroidEntryPoint` generates an individual Hilt component for each Android class in your project. These components can receive dependencies from their respective parent classes as described in [Component hierarchy](https://developer.android.com/training/dependency-injection/hilt-android#component-hierarchy).

To obtain dependencies from a component, use the `@Inject` annotation to perform field injection:

```kt
@AndroidEntryPoint
class ExampleActivity : AppCompatActivity() {

  @Inject lateinit var analytics: AnalyticsAdapter
  ...
}
```

note :Fields injected by Hilt cannot be private. Attempting to inject a private field with Hilt results in a compilation error.

## Define Hilt bindings

To perform field injection, Hilt needs to know how to provide instances of the necessary dependencies from the corresponding component. A *binding* contains the information necessary to provide instances of a type as a dependency.

One way to provide binding information to Hilt is *constructor injection*. Use the `@Inject` annotation on the constructor of a class to tell Hilt how to provide instances of that class:

```kt
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }
```

The parameters of an annotated constructor of a class are the dependencies of that class. In the example, `AnalyticsAdapter` has `AnalyticsService` as a dependency. Therefore, Hilt must also know how to provide instances of `AnalyticsService`.

## Hilt modules

Sometimes a type cannot be constructor-injected. This can happen for multiple reasons. For example, you cannot constructor-inject an interface. You also cannot constructor-inject a type that you do not own, such as a class from an external library. In these cases, you can provide Hilt with binding information by using *Hilt modules*.

A Hilt module is a class that is annotated with `@Module`. Like a Dagger module , it informs Hilt how to provide instances of certain types. Unlike Dagger modules, you must annotate Hilt modules with `@InstallIn` to tell Hilt which Android class each module will be used or installed in.

Dependencies that you provide in Hilt modules are available in all generated components that are associated with the Android class where you install the Hilt module.

A [Hilt module](https://dagger.dev/hilt/modules) can be thought of as a collection of “recipes” that tell Hilt how to create an instance of something that doesn’t have a constructor — such as an interface or a system service.

Modules are installed in a [Hilt component](https://dagger.dev/hilt/components.html) specified using the `@InstallIn` annotation. I’ll explain this in more detail later.

### Inject interface instances with @Binds

Consider the `AnalyticsService` example. If `AnalyticsService` is an interface, then you cannot constructor-inject it. Instead, provide Hilt with the binding information by creating an abstract function annotated with `@Binds` inside a Hilt module.

The `@Binds` annotation tells Hilt which implementation to use when it needs to provide an instance of an interface.

The annotated function provides the following information to Hilt:

- The function return type tells Hilt what interface the function provides instances of.
- The function parameter tells Hilt which implementation to provide.

```kt
interface AnalyticsService {
  fun analyticsMethods()
}

class AnalyticsServiceImpl @Inject constructor(
  ...
) : AnalyticsService { ... }

@Module
@InstallIn(ActivityComponent::class)
abstract class AnalyticsModule {

  @Binds
  abstract fun bindAnalyticsService(
    analyticsServiceImpl: AnalyticsServiceImpl
  ): AnalyticsService
}
```

The Hilt module `AnalyticsModule` is annotated with `@InstallIn(ActivityComponent.class)` because you want Hilt to inject that dependency into `ExampleActivity`. This annotation means that all of the dependencies in `AnalyticsModule` are available in all of the app's activities

### Inject instances with @Provides

Interfaces are not the only case where you cannot constructor-inject a type. Constructor injection is also not possible if you don't own the class because it comes from an external library (classes like [Retrofit](https://square.github.io/retrofit/), [`OkHttpClient`](https://square.github.io/okhttp/), or [Room databases](https://developer.android.com/topic/libraries/architecture/room)), or if instances must be created with the [builder pattern](https://en.wikipedia.org/wiki/Builder_pattern).

Consider the previous example. If you don't directly own the `AnalyticsService` class, you can tell Hilt how to provide instances of this type by creating a function inside a Hilt module and annotating that function with `@Provides`.

The annotated function supplies the following information to Hilt:

- The function return type tells Hilt what type the function provides instances of.
- The function parameters tell Hilt the dependencies of the corresponding type.
- The function body tells Hilt how to provide an instance of the corresponding type. Hilt executes the function body every time it needs to provide an instance of that type.

```kt
@Module
@InstallIn(ActivityComponent::class)
object AnalyticsModule {

  @Provides
  fun provideAnalyticsService(
    // Potential dependencies of this type
  ): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .build()
               .create(AnalyticsService::class.java)
  }
}
```

```kt
@Module
@InstallIn(ApplicationComponent::class)
object ConnectivityManagerModule {
  @Provides
  fun provideConnectivityManager(
    @ApplicationContext context: Context
  ) = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
}
```

### Provide multiple bindings for the same type

In cases where you need Hilt to provide different implementations of the same type as dependencies, you must provide Hilt with multiple bindings. You can define multiple bindings for the same type with *qualifiers*.

A qualifier is an annotation that you use to identify a specific binding for a type when that type has multiple bindings defined.

Consider the example. If you need to intercept calls to `AnalyticsService`, you could use an `OkHttpClient` object with an [interceptor](https://square.github.io/okhttp/interceptors/). For other services, you might need to intercept calls in a different way. In that case, you need to tell Hilt how to provide two different implementations of `OkHttpClient`.

First, define the qualifiers that you will use to annotate the `@Binds` or `@Provides` methods:

```kt
@Qualifier
@Retention(AnnotationRetention.BINARY)
annotation class AuthInterceptorOkHttpClient

@Qualifier
@Retention(AnnotationRetention.BINARY)
annotation class OtherInterceptorOkHttpClient
```

Then, Hilt needs to know how to provide an instance of the type that corresponds with each qualifier. In this case, you could use a Hilt module with `@Provides`. Both methods have the same return type, but the qualifiers label them as two different bindings:

```kt
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {

  @AuthInterceptorOkHttpClient
  @Provides
  fun provideAuthInterceptorOkHttpClient(
    authInterceptor: AuthInterceptor
  ): OkHttpClient {
      return OkHttpClient.Builder()
               .addInterceptor(authInterceptor)
               .build()
  }

  @OtherInterceptorOkHttpClient
  @Provides
  fun provideOtherInterceptorOkHttpClient(
    otherInterceptor: OtherInterceptor
  ): OkHttpClient {
      return OkHttpClient.Builder()
               .addInterceptor(otherInterceptor)
               .build()
  }
}
```

You can inject the specific type that you need by annotating the field or parameter with the corresponding qualifier:

```kt
// As a dependency of another class.
@Module
@InstallIn(ActivityComponent::class)
object AnalyticsModule {

  @Provides
  fun provideAnalyticsService(
    @AuthInterceptorOkHttpClient okHttpClient: OkHttpClient
  ): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .client(okHttpClient)
               .build()
               .create(AnalyticsService::class.java)
  }
}

// As a dependency of a constructor-injected class.
class ExampleServiceImpl @Inject constructor(
  @AuthInterceptorOkHttpClient private val okHttpClient: OkHttpClient
) : ...

// At field injection.
@AndroidEntryPoint
class ExampleActivity: AppCompatActivity() {

  @AuthInterceptorOkHttpClient
  @Inject lateinit var okHttpClient: OkHttpClient
}
```

As a best practice, if you add a qualifier to a type, add qualifiers to all the possible ways to provide that dependency. Leaving the base or common implementation without a qualifier is error-prone and could result in Hilt injecting the wrong dependency.

### Predefined qualifiers in Hilt

Hilt provides some predefined qualifiers. For example, as you might need the `Context` class from either the application or the activity, Hilt provides the `@ApplicationContext` and `@ActivityContext` qualifiers.

Suppose that the `AnalyticsAdapter` class from the example needs the context of the activity. The following code demonstrates how to provide the activity context to `AnalyticsAdapter`:

```kt
class AnalyticsAdapter @Inject constructor(
    @ActivityContext private val context: Context,
    private val service: AnalyticsService
) { ... }
```

## Generated components for Android classes

For each Android class in which you can perform field injection, there's an associated Hilt component that you can refer to in the `@InstallIn` annotation. Each Hilt component is responsible for injecting its bindings into the corresponding Android class.

The previous examples demonstrated the use of `ActivityComponent` in Hilt modules.

Hilt provides the following components:

| Hilt component              | Injector for                                  |
| --------------------------- | --------------------------------------------- |
| `SingletonComponent`        | `Application`                                 |
| `ActivityRetainedComponent` | N/A                                           |
| `ViewModelComponent`        | `ViewModel`                                   |
| `ActivityComponent`         | `Activity`                                    |
| `FragmentComponent`         | `Fragment`                                    |
| `ViewComponent`             | `View`                                        |
| `ViewWithFragmentComponent` | `View` annotated with `@WithFragmentBindings` |
| `ServiceComponent`          | `Service`                                     |

**Note:** Hilt doesn't generate a component for broadcast receivers because Hilt injects broadcast receivers directly from `SingletonComponent`.

##### difference between  SingletonComponent and ActivityRetainedComponent :

1 - SingletonComponent:

- The SingletonComponent is used to define dependencies that have a global singleton scope.

- Dependencies declared in a SingletonComponent will have a single instance throughout the entire lifecycle of the application.

- It is typically used for dependencies that need to be shared across multiple activities, fragments, and other components.

- The SingletonComponent is created when the application starts and remains alive until the application is terminated.

2 - ActivityRetainedComponent:

- The ActivityRetainedComponent is used to define dependencies that have a scope tied to the lifecycle of an activity.
- Dependencies declared in an ActivityRetainedComponent will have a single instance that survives configuration changes (such as device rotation) of the associated activity.
- It is typically used for dependencies that are specific to an activity and should be retained across configuration changes.
- The ActivityRetainedComponent is created when the associated activity is created and remains alive until the activity is destroyed

3 - diffrent between ViewModelScoped and ActivityRetainedComponent :

- `ActivityRetainedScoped`: This scope is used to retain dependencies across configuration changes (such as screen rotations) in an Android activity. It ensures that the dependencies are retained as long as the activity itself is alive. This scope is typically used when you have a dependency that should survive configuration changes but does not need to be shared across multiple activities or fragments.

- `ViewModelScoped`: This scope is specifically designed for dependencies that are tied to an Android ViewModel. It ensures that the dependencies are kept as long as the associated ViewModel is active and in memory. When the ViewModel is destroyed, the dependencies are also cleared. This scope is useful when you have dependencies that are specific to a ViewModel and should not be shared across multiple instances of that ViewModel.

### Component lifetimes

Hilt automatically creates and destroys instances of generated component classes following the lifecycle of the corresponding Android classes.

| Generated component         | Created at               | Destroyed at            |
| --------------------------- | ------------------------ | ----------------------- |
| `SingletonComponent`        | `Application#onCreate()` | `Application` destroyed |
| `ActivityRetainedComponent` | `Activity#onCreate()`    | `Activity#onDestroy()`  |
| `ViewModelComponent`        | `ViewModel` created      | `ViewModel` destroyed   |
| `ActivityComponent`         | `Activity#onCreate()`    | `Activity#onDestroy()`  |
| `FragmentComponent`         | `Fragment#onAttach()`    | `Fragment#onDestroy()`  |
| `ViewComponent`             | `View#super()`           | `View` destroyed        |
| `ViewWithFragmentComponent` | `View#super()`           | `View` destroyed        |
| `ServiceComponent`          | `Service#onCreate()`     | `Service#onDestroy()`   |

**Note:** `ActivityRetainedComponent` lives across configuration changes, so it is created at the first `Activity#onCreate()` and destroyed at the last `Activity#onDestroy()`

## Components

Each module is installed inside a [Hilt component](https://dagger.dev/hilt/components.html), specified by using `@InstallIn(*<component>*)`. The module’s component is primarily used to prevent accidentally injecting a dependency in the wrong place. For example, `@InstallIn(ServiceComponent.class)` would prevent bindings and providers in the annotated module from being used in an activity.

### Component scopes :

By default, all bindings in Hilt are *unscoped*. This means that each time your app requests the binding, Hilt creates a new instance of the needed type.

In the example, every time Hilt provides `AnalyticsAdapter` as a dependency to another type or through field injection (as in `ExampleActivity`), Hilt provides a new instance of `AnalyticsAdapter`.

However, Hilt also allows a binding to be scoped to a particular component. Hilt only creates a scoped binding once per instance of the component that the binding is scoped to, and all requests for that binding share the same instance.

The table below lists scope annotations for each generated component:

| Android class                                 | Generated component         | Scope                     |
| --------------------------------------------- | --------------------------- | ------------------------- |
| `Application`                                 | `SingletonComponent`        | `@Singleton`              |
| `Activity`                                    | `ActivityRetainedComponent` | `@ActivityRetainedScoped` |
| `ViewModel`                                   | `ViewModelComponent`        | `@ViewModelScoped`        |
| `Activity`                                    | `ActivityComponent`         | `@ActivityScoped`         |
| `Fragment`                                    | `FragmentComponent`         | `@FragmentScoped`         |
| `View`                                        | `ViewComponent`             | `@ViewScoped`             |
| `View` annotated with `@WithFragmentBindings` | `ViewWithFragmentComponent` | `@ViewScoped`             |
| `Service`                                     | `ServiceComponent`          | `@ServiceScoped`          |

In the example, if you scope `AnalyticsAdapter` to the `ActivityComponent` using `@ActivityScoped`, Hilt provides the same instance of `AnalyticsAdapter` throughout the life of the corresponding activity:

```kt
@ActivityScoped
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }
```

Suppose that `AnalyticsService` has an internal state that requires the same instance to be used every time—not only in `ExampleActivity`, but anywhere in the app. In this case, it is appropriate to scope `AnalyticsService` to the `SingletonComponent`. The result is that whenever the component needs to provide an instance of `AnalyticsService`, it provides the same instance every time.

```kt
// If AnalyticsService is an interface.
@Module
@InstallIn(SingletonComponent::class)
abstract class AnalyticsModule {

  @Singleton
  @Binds
  abstract fun bindAnalyticsService(
    analyticsServiceImpl: AnalyticsServiceImpl
  ): AnalyticsService
}

// If you don't own AnalyticsService.
@Module
@InstallIn(SingletonComponent::class)
object AnalyticsModule {

  @Singleton
  @Provides
  fun provideAnalyticsService(): AnalyticsService {
      return Retrofit.Builder()
               .baseUrl("https://example.com")
               .build()
               .create(AnalyticsService::class.java)
  }
}
```

example :

```kt
@Module
@InstallIn(ActivityComponent::class)
abstract class MilkModule {
  @ActivityScoped
  @Binds
  abstract fun bindMilk(oatMilk: OatMilk): Milk
}
```

#### use activity and application  field injection context :

```kt
class AnalyticsServiceImpl @Inject constructor(
  @ApplicationContext context: Context
) : AnalyticsService { ... }

// The Application binding is available without qualifiers.
class AnalyticsServiceImpl @Inject constructor(
  application: Application
) : AnalyticsService { ... }
```

```kt
class AnalyticsAdapter @Inject constructor(
  @ActivityContext context: Context
) { ... }

// The Activity binding is available without qualifiers.
class AnalyticsAdapter @Inject constructor(
  activity: FragmentActivity
) { ... }
```

## Entry point :

In some cases you’ll have a class that’s not *created* via dependency injection, but still has dependencies injected into it. A good example of this is activities, which are normally created by the Android framework rather than Hilt.

These classes are [*entry points*](https://dagger.dev/hilt/entry-points.html) into Hilt’s dependency graph and Hilt needs to know they have dependencies that need injecting.

**Android Entry Point**

Most of your entry points will be one of these so-called [Android Entry Points](https://dagger.dev/hilt/android-entry-point.html):

- Activity
- Fragment
- View
- Service
- BroadcastReceiver

If that’s the case, annotate it with `@AndroidEntryPoint`.

```kt
@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  ...
}
```

## Inject a dependency

Once your dependencies are injectable, you can inject them using Hilt in two ways.

1. As constructor parameters
2. As fields

##### As constructor parameters

```kt
interface Milk { ... }
interface Coffee { ... }

class Latte @Inject constructor(
  private val Milk milk,
  private val Coffee coffee
) {
  ...
}
```

If the constructor is marked with `@Inject`, Hilt injects all of the parameters according to the bindings you defined for those types.

 **As fields**

```kt
interface Milk { ... }
interface Coffee { ... }

@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  @Inject lateinit var milk: Milk
  @Inject lateinit var coffee: Coffee

  ...
}
```

If the class is an entry point, here specified using the `@AndroidEntryPoint` annotation (more about that in the next section), all fields annotated with `@Inject` are injected.

Fields annotated with `@Inject` must be public. It’s also convenient to make them `lateinit` to avoid making them nullable, as their initial value prior to injection is `null`.

If the class is an entry point, here specified using the `@AndroidEntryPoint` annotation (more about that in the next section), all fields annotated with `@Inject` are injected.

Fields annotated with `@Inject` must be public. It’s also convenient to make them `lateinit` to avoid making them nullable, as their initial value prior to injection is `null`.

## ViewModel

A `ViewModel` is a special case: it’s not instantiated directly, as the framework needs to create them, but is also not an Android Entry Point. Instead, `ViewModel`s use the special `@ViewModelInject` annotation which allows Hilt to inject dependencies into them when they’re created using `by viewModels()`, similar to how `@Inject` works for other classes.

```kt
interface Milk { ... }
interface Coffee { ... }

@
class LatteViewModel @Inject  constructor(
  private val milk: Milk,
  private val coffee: Coffee
) : ViewModel() {
  ...
}

@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  private val viewModel: LatteViewModel by viewModels()

  ...
}
```

If you need access to saved state in your `ViewModel`, inject a [SavedStateHandle](https://developer.android.com/reference/androidx/lifecycle/SavedStateHandle) as a constructor parameter by adding the `@Assisted` annotation.

```kt
class LatteViewModel @ViewModelInject constructor(
  @Assisted private val savedState: SavedStateHandle,
  private val milk: Milk,
  private val coffee: Coffee
) : ViewModel() {
  ...
}
```

Resource :

[Hilt Dependency Injection (Kotlin Beginner Example) - YouTube](https://www.youtube.com/watch?v=ldeIEXu2-2w)

https://developer.android.com/training/dependency-injection/hilt-android#kotlin

[The Ultimate Dagger-Hilt Guide (Dependency Injection) - Android Studio Tutorial - YouTube](https://www.youtube.com/watch?v=bbMsuI2p1DQ)

[Dagger Hilt in Android with Example - GeeksforGeeks](https://www.geeksforgeeks.org/dagger-hilt-in-android-with-example/)

https://medium.com/androiddevelopers/a-pragmatic-guide-to-hilt-with-kotlin-a76859c324a1

[Entry Points](https://dagger.dev/hilt/entry-points.html)

[Using Hilt in your Android app &nbsp;|&nbsp; Android Developers](https://developer.android.com/codelabs/android-hilt#0)
