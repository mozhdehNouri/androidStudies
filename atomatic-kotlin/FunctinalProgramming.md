## Functional Programming

1. Lambda :

it has no name, requires a minimal amount of code to create, and you can insert it directly into other code.

As a starting point, consider map(), which works with collections like List. The
parameter for map() is a transformation function which is applied to each element
in a collection. map() returns a new List containing all the transformed elements.

```kt
fun main() {
val list = listOf(1, 2, 3, 4)
val result = list.map({ n: Int -> "[$n]" })
result eq listOf("[1]", "[2]", "[3]", "[4]")
}
```

The function body can be one or more expressions. The final expression becomes the
return value of the lambda.



---



```kt
fun main() {
val list = listOf(1, 2, 3, 4)
val even = list.filter { it % 2 == 0 }
val greaterThan2 = list.filter { it > 2 }
even eq listOf(2, 4)
greaterThan2 eq listOf(3, 4)
}
```

```kt
fun main() {
val list = listOf(1, 2, 3, 4)
val isEven = { e: Int -> e % 2 == 0 }
list.filter(isEven) eq listOf(2, 4)
list.any(isEven) eq true
}
```

```kt
fun main() {
val list = listOf(1, 5, 7, 10)
var sum = 0
val divider = 5
list.filter { it % divider == 0 }
.forEach { sum += it }
sum eq 15
}
```

---

#### Sum()

its work only for list<Int>

**Iterable<Int>.sum()**

Returns the sum of all elements in the collection.

sum() works on a list of numbers, adding all the elements in the list.

---

#### any()

The Kotlin List.any() function can be used to check if the list has at least one element, or if the list has at least one element that matches the given predicate.


























