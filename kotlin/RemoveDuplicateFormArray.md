Remove duplicate item in array :

    

we can use following ways for remove duplicate item in array:

- `distinct()`
- `toSet()`
- `toMutableSet()`
- `toHashSet()`



Consider following dataClass :

```kt
data class Mentor(val id: Int, val name: String)
```

and an array of Mentor class :

```kt
val mentors = arrayOf(
    Mentor(1, "Amit Shekhar"),
    Mentor(2, "Anand Gaurav"),
    Mentor(1, "Amit Shekhar"),
    Mentor(3, "Lionel Messi"))
```

### 1- remove duplicates item using  `distinct()`:

in Kotlin, we can use `distinct()` function available in Collection functions to remove duplicates.

```kt
val distinct = mentors.distinct()
println(distinct)
```

output :

```kt
[Mentor(id=1, name=Amit Shekhar),
Mentor(id=2, name=Anand Gaurav),
Mentor(id=3, name=Lionel Messi)]
```

important distinct note  :

- returning `list`

- Maintain the original order of items

- Among equal elements of the given array, only the first one will be present in the output.

- returns a list containing only distinct elements from the given list.

- elements in the resultant list have the same order as in the original array.

### Syntax

```kt
List.distinct()
```

##### note : distinctBy {}

distinctBy extension function, which we can use to specify criteria** for removing duplicate values

lets  take an example :

```kt
data class Employee(val id: String, val name: String)

val emp1 = Employee("Jimmy", "1")
val emp2 = Employee("James", "2")
val emp3 = Employee("Jimmy", "3")
val employees = arrayOf(emp1, emp2, emp1, emp3)

val uniqueEmployees = employees.distinctBy { it.id }
println(uniqueEmployees)
```

output :

```kt
[Employee(id=Jimmy, name=1), Employee(id=James, name=2)]
```

point :

if using  distinct() check hashCode and you output was:

```kt
[Employee(id=Jimmy, name=1), 
Employee(id=James, name=2), 
Employee(id=Jimmy, name=3)]
```

`distinct()`  is useful when using  list with primitive type

`distinctBy{}` is useful when we have list of dataClass.



practice : guess the answer to practice :

```kt
val emp1 = Employee("John", "1")
val emp2 = Employee("John", "2")
val employees = arrayOf(emp1, emp2, emp1)
```

example 3:

```kt
val a = arrayOf("a", "a", "b", "c", "c")
val b = a.distinct() // ["a", "b", "c"]
```

```kt
val a = listOf("a", "b", "ab", "ba", "abc")
val b = a.distinctBy { it.length } // ["a", "ab", "abc"]
```

### 2 - remove duplicate item with toSet() :

```kt
val toSet = mentors.toSet()
println(toSet)
```

output :

```kt
[Mentor(id=1, name=Amit Shekhar),
Mentor(id=2, name=Anand Gaurav),
Mentor(id=3, name=Lionel Messi)]
```

set note :

- Maintain the original order of items.
- Returns `Set` which is a `read-only` set. It means that we can't perform operations like `add` on the set



### 3 - remove item with toMutableSet() :

```kt
val toMutableSet = mentors.toMutableSet()
println(toMutableSet)
```

output :

```kt
[Mentor(id=1, name=Amit Shekhar),
Mentor(id=2, name=Anand Gaurav),
Mentor(id=3, name=Lionel Messi)]
```

- Maintain the original order of items.
- Returns `MutableSet` which is a `read/write` set. It means that we can perform operations like `add` on the mutable set.



### 4 - remove duplicate item with using `toHashSet`()  :

```kt
val toHashSet = mentors.toHashSet()
println(toHashSet)
```

output :

```kts
[Mentor(id=3, name=Lionel Messi),
Mentor(id=1, name=Amit Shekhar),
Mentor(id=2, name=Anand Gaurav)]
```

- Similar to `MutableSet` but do NOT maintain the original order of items.
- Returns `HashSet`













Resource :

https://kotlinandroid.org/kotlin/kotlin-list-distinct/

[distinctBy - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/distinct-by.html)

[Remove Duplicate Values From an Array in Kotlin | Baeldung on Kotlin](https://www.baeldung.com/kotlin/array-remove-duplicates)
