#### Domain Layer in android architecture

The domain layer is an *optional* layer that sits between the UI layer and the data layer.

## Dependencies

In a typical app architecture, use case classes fit between ViewModels from the UI layer and repositories from the data layer.

For example, in your app, you might have a use case class that fetches data from a news repository and an author repository, and combines them:

```kt
class GetLatestNewsWithAuthorsUseCase(
  private val newsRepository: NewsRepository,
  private val authorsRepository: AuthorsRepository
) { /* ... */ }
```

Because use cases contain reusable logic, they can also be used by other use cases

It's normal to have multiple levels of use cases in the domain layer

 For example, the use case defined in the example below can make use of the `FormatDateUseCase` use case if multiple classes from the UI layer rely on time zones to display the proper message on the screen:

```kt
class GetLatestNewsWithAuthorsUseCase(
  private val newsRepository: NewsRepository,
  private val authorsRepository: AuthorsRepository,
  private val formatDateUseCase: FormatDateUseCase
) { /* ... */ }
```

## Call use cases in Kotlin

In Kotlin, you can make use case class instances callable as functions by defining the `invoke()` function with the `operator` modifier.

```kt
class FormatDateUseCase(userRepository: UserRepository) {

    private val formatter = SimpleDateFormat(
        userRepository.getPreferredDateFormat(),
        userRepository.getPreferredLocale()
    )

    operator fun invoke(date: Date): String {
        return formatter.format(date)
    }
}
```

the `invoke()` method in `FormatDateUseCase` allows you to call instances of the class as if they were functions.

The `invoke()` method is not restricted to any specific signature—it can take any number of parameters and return any type

You can also overload `invoke()` with different signatures in your class.

##### you can call like this :

```kt
class MyViewModel(formatDateUseCase: FormatDateUseCase) : ViewModel() {
    init {
        val today = Calendar.getInstance()
        val todaysDate = formatDateUseCase(today)
        /* ... */
    }
}
```

## Lifecycle

Use cases don't have their own lifecycle. Instead, they're scoped to the class that uses them. This means that you can call use cases from classes in the UI layer, from services, or from the `Application` class itself. Because use cases shouldn't contain mutable data, you should create a new instance of a use case class every time you pass it as a dependency.

## Threading

Use cases from the domain layer must be *main-safe*; in other words, they must be safe to call from the main thread. If use case classes perform long-running blocking operations, they are responsible for moving that logic to the appropriate thread. However, before doing that, check if those blocking operations would be better placed in other layers of the hierarchy. Typically, complex computations happen in the data layer to encourage reusability or caching. For example, a resource-intensive operation on a big list is better placed in the data layer than in the domain layer if the result needs to be cached to reuse it on multiple screens of the app.

##### using coroutine :

```kt
class MyUseCase(
    private val defaultDispatcher: CoroutineDispatcher = Dispatchers.Default
) {

    suspend operator fun invoke(...) = withContext(defaultDispatcher) {
        // Long-running blocking operations happen on a background thread.
    }
}
```

### Combine repositories

In a news app, you might have `NewsRepository` and `AuthorsRepository` classes that handle news and author data operations respectively. The `Article` class that `NewsRepository` exposes only contains the name of the author, but you want to display more information about the author on the screen. Author information can be obtained from the `AuthorsRepository`.

Because the logic involves multiple repositories and can become complex, you create a `GetLatestNewsWithAuthorsUseCase` class to abstract the logic out of the ViewModel and make it more readable. This also makes the logic easier to test in isolation, and reusable in different parts of the app.

```kt
/**
 * This use case fetches the latest news and the associated author.
 */
class GetLatestNewsWithAuthorsUseCase(
  private val newsRepository: NewsRepository,
  private val authorsRepository: AuthorsRepository,
  private val defaultDispatcher: CoroutineDispatcher = Dispatchers.Default
) {
    suspend operator fun invoke(): List<ArticleWithAuthor> =
        withContext(defaultDispatcher) {
            val news = newsRepository.fetchLatestNews()
            val result: MutableList<ArticleWithAuthor> = mutableListOf()
            // This is not parallelized, the use case is linearly slow.
            for (article in news) {
                // The repository exposes suspend functions
                val author = authorsRepository.getAuthor(article.authorId)
                result.add(ArticleWithAuthor(article, author))
            }
            result
        }
}
```



## Data layer access restriction

One other consideration when implementing the domain layer is whether you should still allow direct access to the data layer from the UI layer, or force everything through the domain layer.

## Testing



source :

https://developer.android.com/topic/architecture/domain-layer



// domain main sample 
https://www.droidcon.com/2023/08/14/transforming-usecase-embracing-fluent-and-functional-approach-2023/

https://betterprogramming.pub/how-to-avoid-use-cases-boilerplate-in-android-d0c9aa27ef27


https://itnext.io/domain-driven-android-building-a-model-which-makes-sense-badb774c606d

https://developer.android.com/topic/architecture/domain-layer
