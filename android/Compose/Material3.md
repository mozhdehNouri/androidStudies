## android material

in this article we focus of material 3 build we have a summary of material 2

----

### for remembering material 2 Color Palette :

 you select a primary and a secondary color to represent your brand. 

**The baseline Material color theme** :

- Primary and secondary colors
- Variants of primary and secondary colors
- Additional UI colors, such as colors for backgrounds, surfaces, errors, typography, and iconography.

### Primary color:

A primary color is the color displayed most frequently across your app’s screens and components.   like  topAppBar

### Secondary color

A secondary color provides more ways to accent and distinguish your product. Having a secondary color is optional, and should be applied sparingly to accent...

A **secondary color** provides more ways to accent and distinguish your product. Having a secondary color is optional, and should be applied sparingly to accent select parts of your UI.

If you don’t have a secondary color, your primary color can also be used to accent elements.

Secondary colors are best for:

- Floating action buttons
- Selection controls, like sliders and switches
- Highlighting selected text
- Progress bars
- Links and headlines

### Surface, background, and error colors

Surface, background, and error colors typically don’t represent brand:

Surface, background, and error colors typically don’t represent brand:

- **Surface colors** affect surfaces of components, such as cards, sheets, and menus.
- The **background color** appears behind scrollable content. The baseline background and surface color is #FFFFFF.
- **Error color** indicates errors in components, such as invalid text in a text field. The baseline error color is #B00020.

#### "On" colors :

App surfaces use colors from specific categories in your color palette, such as a primary color. Whenever elements, such as text or icons, appear in front of those surfaces, those elements should use colors designed to be clear and legible against the colors behind them.

This category of colors is called “on” colors

#### Typography :

type scale :

| Scale Category | Weight  | Size | Usage |
| -------------- | ------- | ---- | ----- |
| H1             | Light   | 96   |       |
| H2             | Light   | 60   |       |
| H3             | Regular | 48   |       |
| H4             | Regular | 34   |       |
| H5             | Regular | 24   |       |
| H6             | Medium  | 20   |       |
| Subtitle1      | Regular | 16   |       |
| Subtitle2      | Medium  | 14   |       |
| Body1          | Regular | 16   |       |
| Body2          | Regular | 14   |       |
| Button         | Medium  | 14   |       |
| Caption        | Regular | 12   |       |
| OverLine       | Regular | 10   |       |

----

# Material 3 :

### Core colors

Input one or more brand color to define your color scheme.

- Primary

- Secondary

- Tertiary

- Neutral

- - Used for background and surfaces



MD3 provides over 25 color slots for use when customizing an app theme. The color names are also different in that they describe the purpose of the color rather than the color itself. In Material Design 2, for example, we had a color named *purple_500*:

```xml
<color name="purple_500">#FF6200EE</color>
```

This, in turn, was used for the *colorPrimary* theme slot in the light mode *themes.xml* file:

```xml
<item name="colorPrimary">@color/purple_500</item>
```

Suppose that we need to change *colorPrimary* from purple to red. We could, of course, just assign the RGB value for a shade of red to the *purple_500* color declaration. Unfortunately, we now have a red color declaration with a name that suggests it is purple. The only option is to add a new entry to the *colors.xml* file for the red color and then assign it to the *colorPrimary* slot in the *themes.xml* file. This works, but to achieve this we had to edit both the color and theme files. In MD3, color names do not reference the actual color they represent. The *colorPrimary* slot in an MD3 theme file is instead declared as follows:



```theme.xml
<item name="colorPrimary">@color/md_theme_light_primary</item>
```

```color.xml
<color name="md_theme_light_primary">#B82000</color>
```



example of theme in Material3 :

```xml
<resources>
    <style name="Theme.MyAppDemo" parent="Base.Theme.Material3.Dark.NoActionBar">
        <item name="colorPrimary">@color/md_theme_dark_primary</item>
        <item name="colorOnPrimary">@color/md_theme_dark_onPrimary</item>
        <item name="colorPrimaryContainer">@color/md_theme_dark_primaryContainer</item>
        <item name="colorOnPrimaryContainer">@color/md_theme_dark_onPrimaryContainer</item>
        <item name="colorSecondary">@color/md_theme_dark_secondary</item>
.
.
   </style>
   <style name="Theme.ThemeDemo" parent="Base.Theme.ThemeDemo" />
</resources>
```

Color slots in MD3 are grouped as Primary, Secondary, Tertiary, Error, Background, and Surface. These slots are further divided into pairs consisting of a *base color* and an *“on” base color*. This generally translates to the background and foreground colors of a Material component.

The particular group used for coloring will differ between widgets. A Material Button widget, for example, will use the *colorPrimary* base color for the background color and *colorOnPrimary* for its content (i.e. the text or icon it displays). The FloatingActionButton component, on the other hand, uses *colorPrimaryContainer* as the background color and *colorOnPrimaryContainer* for the foreground. The correct group for a specific widget type can usually be identified quickly by changing color settings in the theme files and reviewing the rendering in the layout editor.























##### diffrent between MD2 and MD3 :

The biggest difference between MD2 and MD3 is support for dynamic colors and Material You, both of which will be covered in the next chapter entitled *“A Material Design 3 Theming and Dynamic Color Tutorial”*. Note that dynamic colors only take effect when enabled on the device by the user within the wallpaper and styles section of the Android Settings app.

- M2: Buttons have a height of 36dp and slightly rounded corner radius. Button labels use ALL CAPS. 
- M3: Buttons are taller at 40dp and have fully rounded corners. Button labels use sentence case.



**actually choosing material 3 colors may be difficult 
for generating automatically you can use this link** :

https://m3.material.io/theme-builder#/custom

**for example, you choose only primary and secondary and tertiary color** 



##### Key colors :

- Accent colors 

- - Primary is the base color, used for main components like prominent buttons, active states, and the tint of elevated surfaces.
- - The secondary key color is used for less prominent components in the UI, such as filter chips, and expands the opportunity for color expression.
- - The tertiary key color is used to derive the roles of contrasting accents that can be used to balance primary and secondary colors or bring enhanced attention to an element.



- Neutral colors 

- - Neutral key color
  
  - Neutral variant key Color 





#### Accent colors:

The primary key color is used to derive roles for key components across the UI, such as the FAB, prominent buttons, active states, as well as the tint of elevated surfaces.

The secondary key color is used for less prominent components in the UI such as filter chips, while expanding the opportunity for color expression.

The tertiary key color is used to derive the roles of contrasting accents that can be used to balance primary and secondary colors or bring heightened attention to an element. The tertiary color role is left for teams to use at their discretion and is intended to support broader color expression in products.



Secondary            On Secondary              Secondary Container            OnSecondary Container



|           |              |                     |                       |
|:---------:| ------------ | ------------------- | --------------------- |
| Primary   | On Primary   | Primary Container   | OnPrimary Container   |
| Secondary | On Secondary | Secondary Container | OnSecondary Container |
| Tertiary  | On Tertiary  | Tertiary Container  | OnTertiary Container  |



#### Neutral colors

The neutral key color is used to derive surface color roles for backgrounds, as well as colors used for high emphasis text and icons.

The neutral variant key color is used to derive color roles for medium emphasis elements like text, icons, and component outlines.

like surface  -Outline



#### Error colors

In addition to the accent and neutral key color, the color system includes a semantic color role for error, again in the form of the error role itself, plus an **on-error**, **error container**, and **on-error container** role.

Error - onError -Error Container - onError Contariner



setting theme 

```kt
// Dynamic color is available on Android 12+
val dynamicColor = Build.VERSION.SDK_INT >= Build.VERSION_CODES.S
val colors = when {
    dynamicColor && darkTheme - > dynamicDarkColorScheme(LocalContext.current)
    dynamicColor && !darkTheme - > dynamicLightColorScheme(LocalContext.current)
    darkTheme - > darkColorScheme
    else - > lightColorScheme
}
```



color usage 

```kt
Text(
   text = "Hello theming",
   color = MaterialTheme.colorScheme.primary
)
```



example of PrimaryContainer 

```kt
Card(
    colors = CardDefaults.cardColors(
        containerColor =
        if (isSelected) MaterialTheme.colorScheme.primaryContainer
        else
            MaterialTheme.colorScheme.surfaceVariant)
) {
    Text(
        text = “Dinner club”,
        style = MaterialTheme.typography.bodyLarge,
        color =
        if (isSelected) MaterialTheme.colorScheme.onPrimaryContainer
        else MaterialTheme.colorScheme.onSurface, ),
        ….
        ….
}

```

##### Typography :



| **M3**           | **Default Font Size/Line Height** |
| ---------------- | --------------------------------- |
| `displayLarge`   | `Roboto 57/64`                    |
| `displayMedium`  | `Roboto 45/52`                    |
| `displaySmall`   | `Roboto 36/44`                    |
| `headlineLarge`  | `Roboto 32/40`                    |
| `headlineMedium` | `Roboto 28/36`                    |
| `headlineSmall`  | `Roboto 24/32`                    |
| `titleLarge`     | `New- Roboto Medium 22/28`        |
| `titleMedium`    | `Roboto Medium 16/24`             |
| `titleSmall`     | `Roboto Medium 14/20`             |
| `bodyLarge`      | `Roboto 16/24`                    |
| `bodyMedium`     | `Roboto 14/20`                    |
| `bodySmall`      | `Roboto 12/16`                    |
| `labelLarge`     | `Roboto Medium 14/20`             |
| `labelMedium`    | `Roboto Medium 12/16`             |
| `labelSmall`     | `New Roboto Medium, 11/16`        |



**Note:** Unlike the M2 `Typography` class, the M3 `Typography` class doesn’t currently include a `defaultFontFamily` parameter. You’ll need to use the `fontFamily` parameter in each of the individual `TextStyles` instead.



example :

```kt
val replyTypography = Typography(
        titleLarge = TextStyle(
            fontWeight = FontWeight.SemiBold,
            fontSize = 22. sp,
            lineHeight = 28. sp,
            letterSpacing = 0. sp
        ),
        titleMedium = TextStyle(
            fontWeight = FontWeight.SemiBold,
            fontSize = 16. sp,
            lineHeight = 24. sp,
            letterSpacing = 0.15.sp
        ),
    )
    ...
```



usage :

```kt
Text(
    text = "Hello M3 theming",
    style = MaterialTheme.typography.titleLarge
)
Text(
    text = "you are learning typography",
    style = MaterialTheme.typography.bodyMedium
)
```



MD2 Vs   MD3  Typography

| M2          | M3               |
| ----------- | ---------------- |
| `h1`        | `displayLarge`   |
| `h2`        | `displayMedium`  |
| `h3`        | `displaySmall`   |
| N/A         | `headlineLarge`  |
| `h4`        | `headlineMedium` |
| `h5`        | `headlineSmall`  |
| `h6`        | `titleLarge`     |
| `subtitle1` | `titleMedium`    |
| `subtitle2` | `titleSmall`     |
| `body1`     | `bodyLarge`      |
| `body2`     | `bodyMedium`     |
| `caption`   | `bodySmall`      |
| `button`    | `labelLarge`     |
| N/A         | `labelMedium`    |
| `overline`  | `labelSmall`     |



Resource :

https://www.youtube.com/watch?v=41VZhwrXAKI

https://m2.material.io/design/color/the-color-system.html#color-theme-creation

https://m3.material.io/theme-builder#/custom

https://www.answertopia.com/kotlin-android/working-with-material-design-3-theming-in-android/#google_vignette

https://pranaypatel.medium.com/getting-started-with-material3-in-your-app-47aac160e068

https://github.com/material-components/material-components-android/blob/master/docs/theming/Color.md

https://material.io/blog/migrating-material-3

https://m3.material.io/styles/color/the-color-system/key-colors-tones

https://developer.android.com/jetpack/compose/designsystems/material3#color-scheme

https://m3.material.io/styles/typography/type-scale-tokens
