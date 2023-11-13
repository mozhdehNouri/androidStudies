### Navigarion in jetpack compose

The [`NavController`](https://developer.android.com/reference/androidx/navigation/NavController) is the central API for the Navigation component. It is stateful and keeps track of the back stack of composables that make up the screens in your app and the state of each screen.

You can create a `NavController` by using the [`rememberNavController()`](https://developer.android.com/reference/kotlin/androidx/navigation/compose/package-summary#rememberNavController(kotlin.Array)) method in your composable:

```kt
val navController = rememberNavController()
```

You should create the `NavController` in the place in your composable hierarchy where all composables that need to reference it have access to it and allows you to use the `NavController` and the state it provides via [`currentBackStackEntryAsState()`](https://developer.android.com/reference/kotlin/androidx/navigation/compose/package-summary#(androidx.navigation.NavController).currentBackStackEntryAsState()) to be used as the source of truth for updating composables outside of your screens

Each [`NavController`](https://developer.android.com/reference/androidx/navigation/NavController) must be associated with a single [`NavHost`](https://developer.android.com/reference/kotlin/androidx/navigation/compose/package-summary#NavHost(androidx.navigation.NavHostController,kotlin.String,androidx.compose.ui.Modifier,kotlin.String,kotlin.Function1)) composable. The `NavHost` links the `NavController` with a navigation graph that specifies the composable destinations that you should be able to navigate between. As you navigate between composables, the content of the `NavHost` is automatically [recomposed](https://developer.android.com/jetpack/compose/mental-model#recomposition). Each composable destination in your navigation graph is associated with a *route*.

Creating the `NavHost` requires the `NavController` previously created via `rememberNavController()` and the route of the starting destination of your graph. `NavHost` creation uses the lambda syntax from the [Navigation Kotlin DSL](https://developer.android.com/guide/navigation/navigation-kotlin-dsl#navgraphbuilder) to construct your navigation graph. You can add to your navigation structure by using the [`composable()`](https://developer.android.com/reference/kotlin/androidx/navigation/compose/package-summary#(androidx.navigation.NavGraphBuilder).composable(kotlin.String,kotlin.collections.List,kotlin.collections.List,kotlin.Function1)) method.

```kt
NavHost(navController = navController, startDestination = "profile") {
    composable("profile") { Profile(/*...*/) }
    composable("friendslist") { FriendsList(/*...*/) }
    /*...*/
}
```

##### navigate with an argument :

Navigation Compose also supports passing arguments between composable destinations. In order to do this, you need to add argument placeholders to your route

```kt
NavHost(startDestination = "profile/{userId}") {
    ...
    composable("profile/{userId}") {...}
}
```

By default, all arguments are parsed as strings. The `arguments` parameter of `composable()` accepts a list of [`NamedNavArgument`](https://developer.android.com/reference/androidx/navigation/NamedNavArgument)s. You can quickly create a `NamedNavArgument` using the [`navArgument`](https://developer.android.com/reference/kotlin/androidx/navigation/package-summary#navArgument(kotlin.String,kotlin.Function1)) method and then specify its exact `type`:

```kt
NavHost(startDestination = "profile/{userId}") {
    ...
    composable(
        "profile/{userId}",
        arguments = listOf(navArgument("userId") { type = NavType.StringType })
    ) {...}
}
```

You should extract the arguments from the [`NavBackStackEntry`](https://developer.android.com/reference/kotlin/androidx/navigation/NavBackStackEntry) that is available in the lambda of the `composable()` function.

```kt
composable("profile/{userId}") { backStackEntry ->
    Profile(navController, backStackEntry.arguments?.getString("userId"))
}
```

To pass the argument to the destination, you need to add append it to the route when you make the `navigate` call:

```kt
navController.navigate("profile/user1234")
```

It is strongly advised not to pass around complex data objects when navigating, but instead pass the minimum necessary information, such as a unique identifier or other form of ID, as arguments when performing navigation actions:

Complex objects should be stored as data in a single source of truth, such as the data layer. Once you land on your destination after navigating, you can then load the required information from the single source of truth by using the passed ID. To retrieve the arguments in your ViewModel that's responsible for accessing the data layer, you can use the `ViewModel’s` [`SavedStateHandle`](https://developer.android.com/topic/libraries/architecture/viewmodel-savedstate#savedstatehandle):

```kt
class UserViewModel(
    savedStateHandle: SavedStateHandle,
    private val userInfoRepository: UserInfoRepository
) : ViewModel() {

    private val userId: String = checkNotNull(savedStateHandle["userId"])

    // Fetch the relevant user information from the data layer,
    // ie. userInfoRepository, based on the passed userId argument
    private val userInfo: Flow<UserInfo> = userInfoRepository.getUserInfo(userId)

// …

}
```

### Adding optional arguments

Navigation Compose also supports optional navigation arguments. Optional arguments differ from required arguments in two ways:

- They must be included using query parameter syntax (`"?argName={argName}"`)
- They must have a `defaultValue` set, or have `nullable = true` (which implicitly sets the default value to `null`)

This means that all optional arguments must be explicitly added to the `composable()` function as a list:

```kt
composable(
    "profile?userId={userId}",
    arguments = listOf(navArgument("userId") { defaultValue = "user1234" })
) { backStackEntry ->
    Profile(navController, backStackEntry.arguments?.getString("userId"))
}
```

 Essentially part of navigation you have to know about it :

1. **Navigation Graph:**
   
   - In the Jetpack Navigation library, you define a navigation graph that represents the different screens and the possible navigation paths between them.
   - The navigation graph is usually defined in XML for traditional Android Views, but in Compose, you define it using Kotlin DSL.

2. **NavHost:**
   
   - The `NavHost` is where your navigation graph is hosted. It acts as a container for displaying different composables corresponding to different destinations in your app.
   - The `NavHost` component manages the navigation state and is responsible for showing the appropriate content based on the current destination.

3. **NavController:**
   
   - The `NavController` is an object that manages navigation within the `NavHost`. It keeps track of the current navigation state, allowing you to navigate between different destinations in your app.

4. **NavGraphBuilder:**
   
   - When working with Compose, you use the `NavHost` and `NavController` in combination with the `NavGraphBuilder` to define your navigation graph in code.
   - The `NavGraphBuilder` is a DSL (Domain-Specific Language) that you use to build the navigation graph within the `NavHost`.











Resource:

https://developer.android.com/jetpack/compose/navigation
