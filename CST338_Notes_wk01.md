# CST338 Notes: Java, Encapsulation, UML, Git, JUnit

## 1. Java Language Basics

- **Strongly typed** — fixed variable types, errors caught not coerced
- **Statically typed** — checked at compile time

  ```java
  int foo = 42;
  foo = "Nope."; // error: Required type: int, Provided: String
  ```

- **JVM** — runs compiled Java; "write once, run anywhere"
- **Byte code** — `.java` → compiled → `.class` (byte code) → run by JVM

## 2. Primitive vs. Reference Types

**Primitive types** — raw values, no methods, basic data:

| Type | Kind of Value | Memory Used | Range |
|---|---|---|---|
| `boolean` | true or false | 1 bit (n/a) | — |
| `char` | single char (Unicode) | 2 bytes | all Unicode chars |
| `byte` | integer | 1 byte | -128 to 127 |
| `short` | integer | 2 bytes | -32,768 to 32,767 |
| `int` | integer | 4 bytes | ~-2.1B to 2.1B |
| `long` | integer | 8 bytes | very large |
| `float` | floating-point | 4 bytes | ~±3.4 × 10³⁸ |
| `double` | floating-point | 8 bytes | ~±1.7 × 10³⁰⁸ |

**Reference types** (objects/composite):
- First-class citizens, treated as objects
- Methods **and** data
- Hold object's **state**
- Non-primitive = object

## 3. Classes and Files

- One class per file
- **File name = public class name** exactly (mismatch → compile error)
- Class names capitalized (UpperCamelCase)
- **Package** = folder/namespace, `package PackageName;` at top of file

## 4. Members, Methods, and Access Modifiers

- **Members** — variables belonging to a class (fields/attributes)
- **Methods** — functions belonging to a class
  - Function = named, callable code block
  - Method = function belonging to an object
  - Accessed via **dot notation**: `foo.addition();`

### Access Modifiers ("Trust and Access")

Control who/what can access a member. Principle: objects control own state.

| Modifier | Class | Package | Subclass | World |
|---|---|---|---|---|
| `public` | Y | Y | Y | Y |
| `protected` | Y | Y | Y | N |
| *(no modifier)* — package-private | Y | Y | N | N |
| `private` | Y | N | N | N |

- **private** — class only
- **package-private** (no modifier) — same package
- **protected** — same package + subclasses
- **public** — anywhere

## 5. Encapsulation

- Restricts direct access to object's components
- Bundles data with methods that operate on it
- Objects own their **state**
- Trust enforced by controlling access
- Mechanism: **setters/getters** + `private` fields

## 6. What Is an Object?

- Data + methods that operate on it
- **Instance of a class**
- Maintains own **state**

## 7. Constructors

- Called on **instantiation** (`new` keyword):

  ```java
  Wk02_demo demo = new Wk02_demo();
  ```

- **Default constructor** — auto-provided, no-arg:

  ```java
  Demo2 demo = new Demo2();
  ```

- **Parameterized constructor** — pass args in:

  ```java
  Demo2 demo = new Demo2(21);
  ```

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

- No return type (not even `void`); name = class name
- **Overloadable** (constructors and methods)
- **No destructors** — garbage collection instead

## 8. The `main` Method

```java
public class Wk01_Demo {
    public static void main(String[] args) {
        Demo2 demo = new Demo2(21);
        System.out.println(demo.getCount());
    }
}
```

- Can live in **any** class
- `public` — accessible anywhere (JVM calls it)
- `static` — no instance needed, belongs to class
- `void` — no return
- `main` — required name, JVM entry point
- `String[] args` — required param, command-line args

## 9. UML Diagrams (Unified Modeling Language)

- Standard notation for software design
- Class box: name / attributes / methods
- Visibility symbols = access modifiers:

  | Symbol | Modifier |
  |---|---|
  | `+` | public |
  | `-` | private |
  | `#` | protected |
  | `~` | package-private |

- Format: `- attribute2 : String`, `+ method1() : void`
- Example (`ClassA`):

  ```
  ------------------------
  |        ClassA        |
  ------------------------
  | + attribute1 : int   |
  | - attribute2 : String|
  | # attribute3 : double|
  | ~ attribute4 : boolean|
  ------------------------
  | + method1() : void   |
  | - method2(int) : String|
  | # method3() : double |
  | ~ method4() : boolean|
  ------------------------
  ```

- IntelliJ IDEA Ultimate — auto-generates UML from code

### Static, Abstract, Interfaces & Stereotypes

- **Static** members — underlined
- **Abstract** classes/methods — italicized
- **Interfaces** — marked with the `«interface»` stereotype (stereotypes = metadata, shown in guillemets)
- Constructors likewise get a `«constructor»` stereotype

### Relationships (increasing specificity)

| Relationship | Meaning | In Java |
|---|---|---|
| Dependency | A depends on B (loose) | e.g. B used as a local var/param |
| Association | A sends messages to B | A holds/calls a B |
| Aggregation | A is made up of B (part-whole) | A has a field of type B |
| Composition | A made up of B, **and** B's lifetime depends on A | same, but B destroyed when A is |
| Generalization | A generalizes B → B is a subclass of A | `extends` |
| Realization | B realizes interface A | `implements` |

- **Multiplicity** — how many objects on each end: `1` (default), `*` (zero or more), or a range (`2..*`)
- **Directionality** — arrow = one-way; no arrowhead = bidirectional
- Generalization/realization arrows conventionally drawn pointing **up** to the more general class/interface

## 10. Code Style & Documentation

- Google Java Style Guide (google.github.io/styleguide/javaguide.html):
  - Class names capitalized
  - Braces `{ }` always, even empty/one-line
  - Opening brace on same line as statement
- **Javadoc** — `/** ... */`
  - Required for every hand-written method
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

## 11. Git Workflow: Feature Branch → Main

**Concepts:**
- **Branch** — pointer to a specific commit
- **Commit** — snapshot of tracked files at a point in time

**Commands:**
- `git branch {branchName}` — create branch, stay on current
- `git checkout -b {branchName}` — create + switch to branch
- `git merge {branch}` — combine, preserves/interleaves history
- `git rebase {branch}` — combine, linear history

**Feature branch → main flow:**

```
git checkout main
git pull origin main                # bring local main up to date

git checkout <feature-branch>
git merge main                      # merge latest main into feature branch

git push origin -u <feature-branch> # -u idempotent: no-op if already tracking

# GitHub: open PR, confirm tests pass, squash and merge

git checkout main
git pull origin main                # sync local main with merged PR

git checkout -b <next-feature-branch>  # start next feature branch
```

## 12. JUnit

- Framework for automated unit tests
- Test class mirrors tested class (e.g. `ShapeTest` ↔ `Shape`)
- `@Test` annotation + assertions:

  ```java
  @Test
  void testGetArea() {
      Shape s = new Shape(4, 5);
      assertEquals(20, s.getArea());
  }
  ```

- Common assertions: `assertEquals`, `assertTrue`/`assertFalse`, `assertThrows`
- Run tests before merging PR (tests must pass before squash and merge)
