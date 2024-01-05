Recomposition is triggered by changes to a `State<T>` object, and when a state changes,
recomposition occurs.

When something in your app's data changes, Compose needs to determine which parts of the UI to
update. It accomplishes this using a clever technique known as Positional Memoization.

Now, let's break down the concept of Positional Memoization:

**Memoization:** Memoization is a technique in which a function's results are cached or remembered
to avoid recomputation when the same inputs are encountered in the future.

**Positional Memoization:** In the context of Positional Memoization, the results of a function are
stored based on the arguments passed to the function. The "position" or "position-based" aspect
refers to the idea that the results are stored in a specific location based on the input arguments,
making it easy to retrieve the cached result if the same arguments are encountered again.

**Cache Lookup:** When the function is called with a set of arguments, it first checks whether it
has already computed and stored the result for those specific arguments. If the result is found in
the cache, the function returns the cached result instead of recomputing it, which can save time and
resources.

**So, what is Positional Memoization in Jetpack Compose?**

Compose is based on the concept of Positional Memoization, which is a technique for optimizing
program function calls and returning cached results. When we draw the UI for the first time in
Compose, it caches all the composables in the UI tree. When something in your app's data changes,
Compose compares the new data with the old data. It then determines which composables depend on this
changed data. These are the composables that need to be updated because they might look different
now. So, it doesn't update everything from scratch. Instead, it updates only the specific
composables that are affected by the data change. This makes your app faster and more efficient
because it doesn't redo unnecessary work.

The `remember` function provides us with what's called positional memoization – we're remembering
this value at this specific position in the tree. When this function is recomposed (i.e., executed
again), we have access to the value we defined previously at this position, so even when the name
changes, the color does not.


