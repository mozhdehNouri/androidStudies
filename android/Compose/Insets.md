# inset in compose

important concept of inset is edge-to-edge 

##### what is edge-to-edge :

Today, mobile applications are increasingly displayed across the entire visible surface of the display, beyond the system UI. These applications use an *edge-to-edge* approach, where the application is displayed below the system UI, i.e., the status bar and the navigation bar.

##### why we use edge-to-edge ?

To create a more attractive and modern user interface. Will you agree that everyone is happier when they use a nicer application?

edge-to-edge: draw behind the system bar where the status and navigation bars have opaque backgrounds. 

draw behind the status bar means your content  drawing behind of status bar 
and draw behind the navigation bar mean the content draw behind the navigation bar.   

Actually, edge-to-edge is more than just drawing behind the system bars. its apps taking responsibility for handling those pieces of system ui which might overlap with the app.

 we have two abvious examples are the status bar and navigation bar but we also have the on-screen keyboard(ime).

steps for implementing edge-to-edge :

As of Android 5 (API 21), it’s now possible to set the color for the Status Bar and Navigation Bar. Use the following theme attributes for this purpose:

```kt
<item name="android:statusBarColor">@color/colorAccent</item>
<item name="android:navigationBarColor">@color/colorAccent</item>
```

We can also make the color of the system UI transparent or semi-transparent. To achieve semi-transparency, just set

```kt
<item name="android:windowTranslucentStatus">true</item>
<item name="android:windowTranslucentNavigation">true</item>In Jetpack Compose, you can use the System UI Controller library to change the color, which provides simple tools for changing the color of the UI system
```

In *Jetpack Compose*, you can use the [System UI Controller](https://github.com/google/accompanist/tree/main/systemuicontroller) library to change the color, which provides simple tools for changing the color of the UI system

```kt
private val BlackScrim = Color(0f, 0f, 0f, 0.3f) // 30% opaque black

@Composable
fun TransparentSystemBars() {
   val systemUiController = rememberSystemUiController()
   val useDarkIcons = MaterialTheme.colors.isLight
   SideEffect {
       systemUiController.setSystemBarsColor(
           color = Color.Transparent,
           darkIcons = useDarkIcons,
           isNavigationBarContrastEnforced = false,
           transformColorForLightContent = { original ->
               BlackScrim.compositeOver(original)
           }
       )
   }
}
```

### Request a rendering of the application under the system UI :

In addition to changing the color of the system interface, you need to tell the system to draw the application in full-screen mode.

 Android has special *View SYSTEM_UI_FLAGS* for this purpose (hereafter *UI _FLAGS*). They’ve been deprecated since API 30, and you should now use the new WindowCompat class, which contains the required flags on earlier versions of the API

```kt
WindowCompat.setDecorFitsSystemWindows(window, false)
```

If you apply this in the activity, the framework won’t replace inserts for the content of your application and we’ll have to do it manually.

It makes sense to request rendering mode for the entire application (in onCreate() of your Activity, if you use the single-activity approach).

# Eliminate visual conflicts

If you follow the previous steps and run the application, you can see that the system no longer accounts for the space for the system UI.

Android applications use [*WindowInsets*](https://developer.android.com/reference/android/view/WindowInsets) to manage the system UI. *Insets* is an object that represents an area of a window that conflicts with the application. Conflicts can be different, and there’re different types of *insets* for that (gesture handling areas, system panels, bangs, etc.).

```kt
TopAppBar( 
  contentPadding = WindowInsets.systemBars
        .only(WindowInsetsSides.Horizontal + WindowInsetsSides.Top) 
        .asPaddingValues(), 
  backgroundColor = MaterialTheme.colors.primary 
) { 
  // content… 
}
```

We recommend that all apps use an `edge-to-edge` layout, meaning that it draws behind the system bars to use the entire width and height of the display. This means your app should do all of the following.

- Signal to the system that the app wants to draw behind the system bars.  In your Activity’s onCreate:  
  `WindowCompat.setDecorFitsSystemWindows(window, false)`

- Set the system bar colors to be either transparent or translucent.  In theme XML: `<item name=”android:statusBarColor” tools:targetApi=”21">@android:color/transparent</item>`   In code: `window.statusBarColor = transparent.`

- Offset composables or views by the sizes of the system bars.  Compose: `Modifier.safeDrawingPadding()`, etc   View: Use `ViewCompat.setOnApplyWindowInsetsListener` to obtain the insets.

## Display content edge-to-edge in your app

You can configure your app to draw its content behind the system bars. Together, the status bar and the navigation bar are called the *system bars*.

Your app achieves an edge-to-edge layout by drawing behind these system bars. When implementing edge-to-edge,

1. Lay out your app full-screen.
2. Change the system bar colors and transparency.
3. Handle any visual overlaps.

## Step 1: Lay out your app in full screen

This is the primary step for ensuring that your app goes edge-to-edge, that is, laid out using the entire width and height of the display. Use [`WindowCompat.setDecorFitsSystemWindows(window, false)`](https://developer.android.com/reference/androidx/core/view/WindowCompat#setDecorFitsSystemWindows(android.view.Window,%20boolean)) to lay out your app behind the system bars, as shown in the following code example:

```kt
override fun onCreate(savedInstanceState: Bundle?) {
  super.onCreate(savedInstanceState)
  WindowCompat.setDecorFitsSystemWindows(window, false)
}
```

## Step 2: Change the color of the system bars

When operating in an edge-to-edge layout, your app needs to change the colors of the navigation and status bars to allow for the content underneath to be visible.After your app performs this step, the system handles all visual protection of the user interface in gesture navigation mode or in the button modes.

```kt
<!-- values-v29/themes.xml -->
<style name="Theme.MyApp">
  <item name="android:navigationBarColor">
     @android:color/transparent
  </item>

  <!-- Optional: set to transparent if your app is drawing behind the status bar. -->
  <item name="android:statusBarColor">
     @android:color/transparent
  </item>

  <!-- Optional: set the status bar light and content dark. -->
  <item name="android:windowLightStatusBar">
    true
  </item>
</style>
```

## Step 3: Handle overlaps using insets

After you’ve implemented an edge-to-edge layout with color transparency, some of your app's views may be drawn behind the system bars

The types of insets that apply to displaying your app edge-to-edge are:

- **System bars insets**. These insets are best used for views that are tappable and that shouldn’t be visually obscured by the system bars.

- **System gesture insets**. These insets apply to gesture-navigational areas used by the system that take priority over your app



##### edge to edge in jetpack compose :

## Insets overview

You can draw edge to edge in three steps:

1. tell Android that you want to take advantage of the full screen
2. make the status and navigation bars transparent
3. update your UI so that it is not covered by system bars or notches.



```kt
class BoringActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyAppTheme {
                Column {
                    Text("👋")
                    Text("💯")
                }
            }
        }
    }
}

```

## Tell Android that you want to draw behind system bars

By default, the contents of an Android app is placed within safe bounds. This is the area below any system bars (status bar, navigation bar).

To do this call `WindowCompat.setDecorFitsSystemWindows(window, false)` before your `setContent {}` call.



## Make your status and navigation bars transparent

There is no dedicated Jetpack Compose API to update the colors of your status bar (yet). Handling this ourselves using native Android APIs can get complicated. Because of this we will use the [Accompanist System UI Controller](https://google.github.io/accompanist/systemuicontroller/) library instead

### Install Accompanist System UI Controller

Include the following dependency in your `app/build.gradle`:

```kt
// app/build.gradle

repositories {
    mavenCentral()
}

dependencies {
    implementation "com.google.accompanist:accompanist-systemuicontroller:0.31.0-alpha"
}

```

### Make system bars transparent using Accompanist System UI Controller

Create a `TransparentSystemBars()` composable that will make the system bars transparent as soon as it enters the composition. Include it in your layout to enable transparent bars:

```kt
@Composable
fun TransparentSystemBars() {
    val systemUiController = rememberSystemUiController()
    val useDarkIcons = !isSystemInDarkTheme()

    DisposableEffect(systemUiController, useDarkIcons) {
        systemUiController.setSystemBarsColor(
            color = Color.Transparent,
            darkIcons = useDarkIcons
        )

        onDispose {}
    }
}

```

## Insets in plain English

When you draw edge to edge, by default your UI will be covered by the system bars.

The Insets APIs makes it simple to add paddings to your layouts according to different parts of the screen such as the status bar or the navigation bar.

There are two types of APIs you need to be aware of: `Modifier`s and `WindowInsets`.

For example if you are interested in navigation bars you might want to use `Modifier.navigationBarsPadding()` or `WindowInsets.navigationBars`.

Depending on the composable you want to use it with, you will have to use the one or the other. Let's have a look.

### Apply insets via Modifiers

Say that you have a `Box()` composable that needs to take the full size of the screen. At the same time you need its contents to be within the system bars. The most straight forward way to do this is by using the respective modifier:

```kt
Box(Modifier
        .systemBarsPadding()
        .border(2.dp, Color.Red, RectangleShape)
        .fillMaxSize()
)

```

This will cause the contents of the `Box()` to be placed between the system bars (not behind it).

Think about `Modifier.systemBarsPadding()` like `Modifier.padding()` where the padding value for each side (left, bottom, top, right) is updated according to any system bar in the respective location of the screen.

The cool thing about it is that the padding values will be automatically updated according to configuration changes.

### Apply insets via `WindowInsets`

You will quickly realize that you cannot use inset `Modifier`s with every composable.

Using a `Modifier` with a `LazyColumn` will not produce the result you want. It will cause the list's contents to be 'cut off' when it reaches the end of the list.

Instead, use the respective `WindowInset` to apply content padding to the list.

```kt
val emojis = (1..100)
    .map { String(Character.toChars(127744 + it)) }
    .toList()

LazyColumn(
    contentPadding = WindowInsets.systemBars.asPaddingValues()
) {
    items(emojis) {
        Text(
            it, modifier = Modifier
                .padding(
                    vertical = 10.dp,
                    horizontal = 16.dp
                )
                .fillMaxWidth()
        )
    }
}

```



finally using like this :

```kt

```







































resource :

https://www.youtube.com/watch?v=acC7SR1EXsI

https://medium.com/lonto-digital-services-integrator/cooking-window-inset-with-jetpack-compose-sauce-and-a-pinch-of-view-4fea49407314

https://medium.com/androiddevelopers/is-your-app-providing-a-backward-compatible-edge-to-edge-experience-2479267073a0

https://developer.android.com/develop/ui/views/layout/edge-to-edge

[🚀 Drawing edge to edge in Jetpack Compose](https://www.composables.com/tutorials/insets)
