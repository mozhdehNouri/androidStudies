## Solid principle

Solide principle is not only define for android programming but in this article we define it android development

SOLID principles, which were introduced by Robert C. Martin in the early 2000s. These principles are a set of guidelines for writing clean, maintainable, and scalable code, and they apply to any programming language, including Kotlin.

By following SOLID principles, you’ll be able to write code that is easier to understand, maintain, and extend over time.

### **Single Responsibility Principle (SRP)**

The Single Responsibility Principle (SRP) states that a class should have only one reason to change, meaning that it should have only one responsibility. This principle is fundamental to writing maintainable and scalable software, as it helps to reduce the complexity of a codebase.

Consider a class `Student` which has multiple responsibilities such as storing student information, calculating student grades, and printing student information to the console. The code for this class might look like this :

```kt
class Student {
    var name: String
    var age: Int
    var marks: Int

    fun calculateGrades() : String {
        // Code to calculate grades
    }

    fun printStudentInformation() {
        // Code to print student information to the console
    }
}
```

In this implementation, the `Student` class has multiple responsibilities, and if we want to change anything related to student information, we need to modify the `Student` class. This creates tight coupling between the responsibilities, which can make it difficult to maintain and scale the codebase over time.

To follow the Single Responsibility Principle, we can refactor the code and create two separate classes, one for storing student information, and another for printing information to the console. The code might look like this:

```kt
class StudentInfo {
    var name: String
    var age: Int
    var marks: Int

    fun calculateGrades() : String {
        // Code to calculate grades
    }
}

class StudentPrinter {
    fun printStudentInformation(studentInfo: StudentInfo) {
        // Code to print student information to the console
    }
}
```

#### **Open/Closed Principle (OCP)**

The Open/Closed Principle (OCP) states that software entities, such as classes, modules, or functions, should be open for extension but closed for modification. This means that we should design our code in a way that it can be easily extended to accommodate new requirements, but its existing behavior should not be changed.
