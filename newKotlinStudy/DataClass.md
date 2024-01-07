##### What is dataClass

The class mechanism performs a fair amount of work for you. However,
creating
classes that primarily hold data still requires a significant amount of
repetitive code.
When you need a class that’s essentially a data holder, data classes
simplify your
code and perform common tasks.
You define a data class using the data keyword, which tells Kotlin to
generate
additional functionality.

1. The String produced by s1 is different than what we usually see; it
   includes the
   parameter names and values of the data held by the object. data classes
   display
   objects in a nice, readable format without requiring any additional
   code

2. If you create two instances of the same data class containing identical
   data
   (equal values for properties), you probably also want those two
   instances to
   be equal. To achieve that behavior for a regular class, you must define
   a
   special function equals() to compare instances. In data classes, this
   function
   is automatically generated; it compares the values of all properties
   specified as
   constructor parameters

```kt
class Person(val name: String)
data class Contact(
   val name: String,
   val number: String
)

fun main() {
// These seem the same, but they're not:
   Person("Cleo") neq Person("Cleo")
// A data class defines equality sensibly:
   Contact("Miffy", "1-234-567890") eq
           Contact("Miffy", "1-234-567890")
}
```

Because the Person class is defined without the data keyword, two
instances
containing the same name are not equal. Fortunately, creating Contact as a
data
class produces a reasonable result.
Notice the difference between the display format of the data class, and
Person,
which just shows default object information.

Another useful function generated for every data class is copy(), which
creates a
new object containing the data from the current object. However, it also
allows you
to change selected values in the process.

```kt
data class DetailedContact(
   val name: String,
   val surname: String,
   val number: String,
   val address: String
)

fun main() {
   val contact = DetailedContact(
      "Miffy",
      "Miller",
      "1-234-567890",
      "1600 Amphitheater Parkway"
   )
   val newContact = contact.copy(
      number = "098-765-4321",
      address = "Brandschenkestrasse 110"
   )
   newContact eq DetailedContact(
      "Miffy",
      "Miller",
      "098-765-4321",
      "Brandschenkestrasse 110"
   )
}
```

HashMap and HashSet
Creating a data class also generates an appropriate hash function so that
objects can
be used as keys in HashMaps and HashSets:

```kt
data class Key(val name: String, val id: Int)

fun main() {
   val korvo: Key = Key("Korvo", 19)
   korvo.hashCode() eq -2041757108
   val map = HashMap()
   map[korvo] = "Alien"
   map[korvo] eq "Alien"
   val set = HashSet()
   set.add(korvo)
   set.contains(korvo) eq true
}
```

hashCode() is used in conjunction with equals() to rapidly look up a Key
in
a HashMap or a HashSet. Creating a correct hashCode() by hand is tricky
and
error-prone, so it is quite beneficial to have the data class do it for
you. Operator
Overloading covers equals() and hashCode() in more detail








