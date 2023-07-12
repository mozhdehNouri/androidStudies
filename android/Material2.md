### Material 2 in android



first, we want to start with colors in material 2 
The Material Design color system can help you create a color theme that reflects your brand or style.

the main color you have to set :

`primary
primaryVariant
secondary
secondaryVariant
background
surface
error
onPrimary
onSecondary
onBackground
onSurface
onError
isLight`



#### Primary :

The primary color is the color displayed most frequently across your app’s screens and components.



### primaryVariant :

The primary variant color is used to distinguish two elements of the app using the primary color, such as the top app bar and the system bar.



### secondary :

The secondary color provides more ways to accent and distinguish your product. 

Having a secondary color is optional, and should be applied sparingly to accent select parts of your UI.Secondary colors are best for:

- Floating action buttons

- Selection controls, like checkboxes and radio buttons and switches

- Highlighting selected text

- Links and headlines 

- Highlighting selected text

### secondaryVariant :

The secondary variant color is used to distinguish two elements of the app using the secondary color.



### surface :

The surface color is used on surfaces of components, such as cards, sheets and menus.



### contentColorFor :

The Material color system contains pairs of colors that are typically used for the background and content color inside a component. For example, a  Button typically uses `primary` for its background, and `onPrimary` for the color of its content (usually text or iconography).



### background :

The background color appears behind scrollable content.



### onBackground :

Color used for text and icons displayed on top of the background color.



### error :

The error color is used to indicate error within components, such as text fields.



### onError :

Color used for text and icons displayed on top of the error color.





example :

if your primary color is  #6200EE(500)  you primary variant have to Two degrees darker from primary Color for example primary variant is #3700B3(700)





#### [Typography](, headlines span from a range of 1 through 6. Headlines are the largest text on the screen, reserved for short, important text or numerals.) :

##### Headlines :

headlines span from a range of 1 through 6. Headlines are the largest text on the screen, reserved for short, important text or numerals.



### Subtitles

Subtitles are smaller than headlines. They are typically reserved for medium-emphasis text that is shorter in length. Serif or sans serif typefaces work well for subtitles.



### Body

Body text comes in ranges 1-2, and it’s typically used for long-form writing as it works well for small text sizes. For longer sections of...



### Caption and overline

Caption and overline text (text with a line above it) are the smallest font sizes. They are used sparingly to annotate imagery or to introduce a headline.



### Button

Button text is a call to action used by different types of buttons (such as text, outlined and contained buttons) and in tabs, dialogs, and cards.













Resource :

https://developer.android.com/reference/kotlin/androidx/compose/material/Colors#primary()

https://m2.material.io/design/color/the-color-system.html#color-usage-and-palettes

https://m2.material.io/design/color/the-color-system.html#color-theme-creation
































