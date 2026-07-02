# CST338 Notes: Java Primer & Encapsulation

*Source: SP26WK01_Quick_JavaPrimer.pptx, P1_Encapsulation.pptx*

## 1. Java Language Basics

- Java is a **strongly typed** language — every variable has a fixed type, and type errors are caught rather than silently coerced.
- Java is also **statically typed** — types are checked at compile time. Example of a type mismatch caught by the compiler/linter:

  ```java
  int foo = 42;
  foo = "Nope."; // error: Required type: int, Provided: String
  ```

- Java runs on the **Java Virtual Machine (JVM)** — compiled Java doesn't run directly on hardware; the JVM interprets/executes it, which is why Java is "write once, run anywhere."
- Java **compiles into byte code** (`.class` files) rather than directly into native machine code. The `.java` source file is compiled into a `.class` byte code file that the JVM runs.

## 2. Primitive vs. Reference Types

**Primitive types** hold raw values directly (not objects), have no methods, and represent basic data:

| Type | Kind of Value | Memory Used | Range |
|---|---|---|---|
| `boolean` | true or false | 1 bit (not applicable) | — |
| `char` | single character (Unicode) | 2 bytes | all Unicode characters |
| `byte` | integer | 1 byte | -128 to 127 |
| `short` | integer | 2 bytes | -32,768 to 32,767 |
| `int` | integer | 4 bytes | ~-2.1 billion to 2.1 billion |
| `long` | integer | 8 bytes | very large range |
| `float` | floating-point | 4 bytes | ~±3.4 × 10³⁸ |
| `double` | floating-point | 8 bytes | ~±1.7 × 10³⁰⁸ |

**Reference types** (a.k.a. objects/composite types):
- Are first-class citizens — treated as objects.
- Have both methods **and** data.
- Hold the **state** of the object.
- Everything that isn't a primitive is an object in Java.

## 3. Classes and Files

- Each class goes in its **own file**.
- The **file name must match the public class name** exactly (e.g., `Wk01_Demo.java` must contain `public class Wk01_Demo`). Mismatches cause a compile error ("we get yelled at").
- Class names are capitalized (UpperCamelCase); this is why the file name capitalization matters.
- Classes can be grouped into a **package** (a folder/namespace, declared with `package PackageName;` at the top of the file).

## 4. Members, Methods, and Access Modifiers

- **Members**: variables that belong to a class (a.k.a. fields/attributes).
- **Methods**: functions that belong to a class.
  - A **function** is a named, callable block of code.
  - A **method** is a function that belongs to an object.
  - Methods are accessed with **dot notation**: `foo.addition();`

### Access Modifiers ("Trust and Access")

Access modifiers control who/what can access or modify a class member. The core principle: objects must control their own state, and external code should not be able to directly change an object's internal state.

| Modifier | Class | Package | Subclass | World |
|---|---|---|---|---|
| `public` | Y | Y | Y | Y |
| `protected` | Y | Y | Y | N |
| *(no modifier)* — package-private | Y | Y | N | N |
| `private` | Y | N | N | N |

- **private**: only the class itself can access it.
- **package-private** (no modifier): accessible within the same package.
- **protected**: accessible within the same package or by subclasses.
- **public**: accessible from anywhere.

## 5. Encapsulation

- **Encapsulation** (Wikipedia definitions given in class):
  1. A language mechanism for restricting direct access to some of an object's components.
  2. A language construct that facilitates bundling data together with the methods that operate on that data.
- Objects have data and are responsible for the **state** of that data.
- We must be able to **trust** that data — encapsulation is how we enforce that trust, by controlling *who* has access.
- The mechanism for controlling access to state: **setters and getters** (accessor/mutator methods), combined with `private` fields.

## 6. What Is an Object?

- An **object** is a collection of data and the methods that operate on that data.
- Specifically, an object is an **instance of a class**.
- An object is responsible for maintaining its own **state**.

## 7. Constructors

- A **constructor** is called when an object is **instantiated** (created) with the `new` keyword:

  ```java
  Wk02_demo demo = new Wk02_demo();
  ```

- Java automatically provides a **default constructor** (no-argument) if you don't write one:

  ```java
  Demo2 demo = new Demo2();
  ```

- **Parameterized constructors** let you pass arguments in when creating the object:

  ```java
  Demo2 demo = new Demo2(21);
  ```

  This requires defining a constructor in the class that accepts matching parameters, e.g.:

  ```java
  public class Demo2 {
      private String mName;
      protected int mCount;

      Demo2() {
          mName = "neat";
          mCount = 42;
      }

      Demo2(int count) {
          mCount = count;
          mName = "neat";
      }
  }
  ```

- A constructor **returns no type** — not even `void` — and **shares its name with the class**.
- **Constructors can be overloaded** (multiple constructors with different parameter lists). Regular methods can be overloaded too.
- **Java has no destructors** (no `~ClassName()` like C++). Java relies on **garbage collection** to reclaim memory instead of manual/explicit destruction.

## 8. The `main` Method

```java
public class Wk01_Demo {
    public static void main(String[] args) {
        Demo2 demo = new Demo2(21);
        System.out.println(demo.getCount());
    }
}
```

- A `main` method can be included in **any** class.
- `public` — must be accessible from anywhere (JVM needs to call it).
- `static` — doesn't rely on an instance of the class existing (belongs to the class, not an object).
- `void` — returns nothing.
- `main` — the required method name; this is the JVM's entry point.
- Must take a `String[]` (String array) parameter — used for command-line arguments.

## 9. UML Diagrams (Unified Modeling Language)

- UML standardizes notation for software design. Originally developed by Grady Booch, Ivar Jacobson, and James Rumbaugh at Rational Software (1994–1996).
- Class diagram box has three sections: class name, attributes, methods.
- Attribute/method visibility symbols in UML map to access modifiers:

  | Symbol | Modifier |
  |---|---|
  | `+` | public |
  | `-` | private |
  | `#` | protected |
  | `~` | package-private |

- Format example: `- attribute2 : String` (private String attribute2), `+ method1() : void` (public method returning void).
- Tools for creating UML: **draw.io** (easy, accessible), **IntelliJ IDEA Ultimate** (can auto-generate UML from code), or hand-drawn (pencil/paper, whiteboard, tablet) and photographed.

## 10. Code Style & Documentation

- Class follows Google's Java Style Guide (https://google.github.io/styleguide/javaguide.html):
  - Class names must be capitalized.
  - Every statement gets braces `{ }` — even empty or one-line statements.
  - Opening braces go on the **same line** as the statement (not on their own line).
- **Javadoc**: comments of the form `/** ... */`.
  - Every method you write yourself (not auto-generated, e.g. by an IDE) **must** have a Javadoc comment.
  - Example:

    ```java
    /**
     * Returns a Random action.
     * @return A String representing a generic action
     */
    private static String getRandomAction() {
        List<String> actions = new ArrayList<>();
        actions.add("Drink coffee");
        actions.add("Sleep");
        actions.add("Grade");
        Random random = new Random();
        return actions.get(random.nextInt(actions.size()));
    }
    ```

## 11. Course Context (background, low quiz-relevance)

- Course assumes ~2 prior programming classes; assumes familiarity with variables, loops, if-statements, basic debugging — but no prior Java or OOP experience assumed.
- Java is designed to be object-oriented; OOP supports extensibility, robustness, and good design.
- Course goals: build Java experience, learn OOP design patterns/anti-patterns, work on larger projects, learn some software development practice, and write some JavaFX.
- File-naming matters for submitted assignments (Canvas may append numbers to filenames — this is auto-corrected by the instructor's script, but the submitted file itself must be named correctly).
