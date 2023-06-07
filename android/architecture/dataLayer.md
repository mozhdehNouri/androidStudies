### Data Layer in android architecture

 data layer contains *application data* and *business logic*. The business logic is what gives value to your app

This separation of concerns allows the data layer to be used on multiple screens, share information between different parts of the app

 the role of the data layer is to hold, manage, and provide access to the app data,

## Data layer architecture

The data layer is made of *repositories* that each can contain zero to many *data sources*. You should create a repository class for each different type of data you handle in your app. For example, you might create a `MoviesRepository` class for data related to movies, or a `PaymentsRepository` class for data related to payments.

Repository classes are responsible for the following tasks:

- Exposing data to the rest of the app.
- Centralizing changes to the data.
- Resolving conflicts between multiple data sources.
- Abstracting sources of data from the rest of the app.
- Containing business logic.

Each data source class should have the responsibility of working with only one source of data, which can be a file, a network source, or a local database. Data source classes are the bridge between the application and the system for data operations.

Other layers in the hierarchy should never access data sources directly; the entry points to the data layer are always the repository classes. State holder classes `UI layer `or use case classes  `domain layer `guideshould never have a data source as a direct dependency. Using repository classes as entry points allows the different layers of the architecture to scale independently.

**The data exposed by this layer should be immutable** so that it cannot be tampered with by other classes. Immutable data can also be safely handled by multiple threads.

example :

```kt
class ExampleRepository(
    private val exampleRemoteDataSource: ExampleRemoteDataSource, // network
    private val exampleLocalDataSource: ExampleLocalDataSource // database
) { /* ... */ }
```

## Expose APIs :

Classes in the data layer generally expose functions to perform one-shot Create, Read, Update and Delete (CRUD) calls or to be notified of data changes over time. The data layer should expose the following for each of these cases:

**One-shot operations:** The data layer should expose suspend functions in Kotlin;

**To be notified of data changes over time:** The data layer should expose [flows](https://developer.android.com/kotlin/flow) in Kotlin;

```kt
class ExampleRepository(
    private val exampleRemoteDataSource: ExampleRemoteDataSource, // network
    private val exampleLocalDataSource: ExampleLocalDataSource // database
) {

    val data: Flow<Example> = ...

    suspend fun modifyData(example: Example) { ... }
}
```

## Naming

*type of data* + *Repository*.

For example: `NewsRepository`, `MoviesRepository`, or `PaymentsRepository`

*type of data* + *type of source* + *DataSource*  

for example :  NewsRemoteDataSource -NewsLocalDataSource

To be more specific in case the source is important, use the type of the source. For example: `NewsNetworkDataSource` or `NewsDiskDataSource`.

Don't name the data source based on an implementation detail—for example, `UserSharedPreferencesDataSource`—because repositories that use that data source shouldn't know how the data is saved. If you follow this rule, you can change the implementation of the data source (for example, migrating from SharedPreferences  to DataStore without affecting the layer that calls that source.

## Multiple levels of repositories

In some cases involving more complex business requirements, a repository might need to depend on other repositories.

For example, a repository that handles user authentication data, `UserRepository`, could depend on other repositories such as `LoginRepository` and `RegistrationRepository` to fulfill its requirements.

As for best practices, it's generally recommended to keep repositories focused on a single responsibility or entity. Each repository should handle a specific type of data or encapsulate a particular set of operations. This promotes a modular and maintainable design by separating concerns and ensuring that each repository has a clear and well-defined purpose.

When repositories depend on each other, it's essential to carefully manage the interactions and ensure proper abstraction and encapsulation. This can be achieved by defining clear interfaces and contracts between repositories, avoiding tight coupling, and adhering to the principles of dependency inversion and single responsibility.

## Source of truth

It's important that each repository defines a single source of truth. The source of truth always contains data that is consistent, correct, and up-to-date. In fact, the data exposed from the repository should always be the data coming directly from the source of truth.

The source of truth can be a data source—for example, the database—or even an in-memory cache that the repository might contain

Repositories combine different data sources and solve any potential conflicts between the data sources to update the single source of truth regularly or due to a user input event.

Different repositories in your app might have different sources of truth. For example, the `LoginRepository` class might use its cache as the source of truth and the `PaymentsRepository` class might use the network data source.

In order to provide offline-first support, **a local data source—such as a database—is the recommended source of truth**.

###### what is the meaning of above explain ?

it say

## Threading

Calling data sources and repositories should be *main-safe*—safe to call from the main thread. These classes are responsible for moving the execution of their logic to the appropriate thread when performing long-running blocking operations. For example, it should be main-safe for a data source to read from a file, or for a repository to perform expensive filtering on a big list.

Note that most data sources already provide main-safe APIs like the suspend method calls provided by Room , Retrofit or [Ktor](https://ktor.io/). Your repository can take advantage of these APIs when they are available.

## Represent business models

The data models that you want to expose from the data layer might be a subset of the information that you get from the different data sources. Ideally, the different data sources—both network and local—should return only the information your application needs; but that's not often the case.

For example, imagine a News API server that returns not only the article information, but also edit history, user comments, and some metadata:

```kt
data class ArticleApiModel(
    val id: Long,
    val title: String,
    val content: String,
    val publicationDate: Date,
    val modifications: Array<ArticleApiModel>,
    val comments: Array<CommentApiModel>,
    val lastModificationDate: Date,
    val authorId: Long,
    val authorName: String,
    val authorDateOfBirth: Date,
    val readTimeMin: Int
)
```

The app doesn't need that much information about the article because it only displays the content of the article on the screen, along with basic information about its author. It's a good practice to separate model classes and have your repositories expose only the data that the other layers of the hierarchy require. For example, here is how you might trim down the `ArticleApiModel` from the network in order to expose an `Article` model class to the domain and UI layers:

```kt
data class Article(
    val id: Long,
    val title: String,
    val content: String,
    val publicationDate: Date,
    val authorName: String,
    val readTimeMin: Int
)
```

Separating model classes is beneficial in the following ways:

- It saves app memory by reducing the data to only what's needed.
- It adapts external data types to data types used by your app—for example, your app might use a different data type to represent dates.
- It provides better separation of concerns—for example, members of a large team could work individually on the network and UI layers of a feature if the model class is defined beforehand.

## Types of data operations

the data layer refers to the component responsible for managing data and interacting with data sources such as databases, APIs, and local storage. Within the data layer, different types of data operations can be categorized based on their importance and purpose. These categories include UI-oriented, app-oriented, and business-oriented operations.

### UI-oriented operations

UI-oriented operations are only relevant when the user is on a specific screen, and they're canceled when the user moves away from that screen. An example is displaying some data obtained from the database.

UI-oriented operations are typically triggered by the UI layer and follow the caller's lifecycle—for example, the lifecycle of the ViewModel. See the Make a network request section for an example of a UI-oriented operation.

### App-oriented operations

App-oriented operations are relevant as long as the app is open. If the app is closed or the process is killed, these operations are canceled. An example is caching the result of a network request so that it can be used later if needed. See the Implement in-memory data caching section to learn more.

These operations typically follow the lifecycle of the `Application` class or the data layer. For an example, see the Make an operation live longer than the screen section.

### Business-oriented operations

Business-oriented operations cannot be canceled. They should survive process death. An example is finishing the upload of a photo that the user wants to post to their profile.

The recommendation for business-oriented operations is to use WorkManager. 

resource :

https://developer.android.com/topic/architecture/data-layer
