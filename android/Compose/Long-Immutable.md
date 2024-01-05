use **@Immutable** to improve your performance in jetpack compose

compose is designed to efficiently update the UI when the data Changes. When you use a `LazyColumn`
or `LazyRow` to display a list of items, Compose is smart enough to recompose only the items that
need to be updated when the data changes.

now look at the example:

```kt
data class Book(
    val name:String ,
    val createdAt :String,
    val author :String 
)
```

and composable function :

```kt
@Composable 
fun BookItem(book:Book){
Column(){
    Text(name)
    Text(author)
}}
```

in the above example we have a dataClass called Book and a Composable function that takes a "Book"
object as parameter. now if any part of Book object changes like name or createAt compose will need
to recompose the "BookItem" to reflect the updated data.

now assume BookItem() wanna use in LazyColumn for display a list of users
While implementing it, sometimes you may notice that there's something that is lagging on UI actully
The function`BookItem`will be recomposed whenever the parameter`Book`is changed.

**Now how can fix it ?**

@Immutable annotation :

with @Immutable annotation In Compose, when you sticker on a class, it's like telling Compose that
the things inside that class won't change. This is really helpful for Compose because it can do some
cool tricks to make your app run smoother and faster.

like :

```kt
@Immutable
data class Book(
    val name:String ,
    val createdAt :String,
    val author :String 
)
```

now maybe you mixed up with these question in your mind

question : 1- What happens if there's an update to a property like 'name'? Will we not receive
updates?

answer : it's essential to understand that using `@Immutable` is not a guarantee that the data won't
change. It's a tool for developers to convey their intent about data immutability. If your data
changes often, it's better not to use `@Immutable` because it might lead to incorrect assumptions
and less efficient updates by Compose.

2 - Isn't Compose smart enough to understand the changed items without @Immutable?

answer :

Compose is indeed smart in understanding changes, using `@Immutable` can provide an extra level of
guidance to Compose to further optimize performance, especially in cases where data is genuinely
meant to be mostly static or infrequently changing.

let me compare a class without **@Immutable** and with **With @Immutable**

**Without @Immutable**:
When you don't use the `@Immutable` annotation on your data class, Compose will assume that the data
within the class can change at any time. Therefore, whenever you provide a new instance of the data
class, even if the data properties have the same values, Compose may recompose the Composables using
that data. This can lead to more frequent recomposition, potentially impacting performance.

**With @Immutable**:
When you use the `@Immutable` annotation on a data class, you're indicating to Compose that the data
within the class is expected to be immutable or change infrequently. Compose can then make certain
optimizations based on this hint. It may not recompose Composables using that data class unless a
genuinely new instance (with different property values) is provided. This can reduce unnecessary
recomposition and improve performance.
