# CST338 Notes: Design Patterns

## 1. What Is a Design Pattern?

- A reusable **solution shape** for a recurring problem — not something blindly copy-pasted, more like a tool/data structure you adapt to the situation
- Nystrom (*Game Programming Patterns*): "a simple structure used cleverly is better than a clever structure used simply"
- Three problem categories: **creational**, **structural**, **behavioral**
- ~23 patterns from the "Gang of Four" (Gamma, Helm, Johnson, Vlissides) book *Design Patterns: Elements of Reusable Object-Oriented Software*
- Other references: *Game Programming Patterns* (Robert Nystrom), sourcemaking.com (GoF content + code examples + anti-patterns)
- Many pattern behaviors are now built into modern languages directly (e.g. annotations ≈ Decorator, built-in state machine tools)
- Most rely on **inheritance** (class inheritance or interface implementation)
- Useful when prompting an LLM for help — naming the pattern you want gives it strong direction, and knowing the pattern lets you verify its output is structured correctly

## 2. Commonly Referenced Patterns (brief)

| Pattern | Category | Idea |
|---|---|---|
| Singleton | Creational | Ensure only one instance of a class ever exists (e.g. one Bluetooth antenna, one in-memory database connection as source of truth) |
| Factory Method | Creational | Delegate object creation to another object/method instead of instantiating directly |
| Flyweight | Structural | Share common/repeated data across many instances instead of duplicating it (e.g. game sprites shared by many enemy instances) |
| Decorator | Structural | Add behavior to an object without changing its underlying code (Java annotations are an example) |
| Command | Behavioral | Encapsulate a method call/request as an object (this week's focus) |
| Memento | Behavioral | Capture/store the minimal state needed to restore an object later (e.g. save files) |
| Observer | Behavioral | Objects subscribe to be notified of another object's changes — common in event-driven/UI programming |
| State | Behavioral | Use a state machine to track and control an object's behavior |
| Strategy | Behavioral | Make an algorithm/behavior swappable at runtime |

## 3. The Command Pattern

- Turns a **method call into an object** — decouples the method from the object it's tightly bound to
- Definition (paraphrased): encapsulate a request as an object, so you can parameterize objects with different requests, queue/log requests, and support undo
- Lets you assign new behavior to an object **at runtime**, without breaking encapsulation
- Caution: seeing verbs like `execute`/`do`/`activate` doesn't automatically mean "this is the Command pattern" — read the actual structure, not just the naming

### Roles

- **Command interface** — one generic verb method (`execute()`, `do()`, `activate()`)
- **Concrete command** — implements the interface; holds a reference to the receiver, the method to invoke, and any arguments
- **Receiver** — the object that actually does the work
- **Creator / Client** — creates the command object
- **Invoker** — receives the command object and decides when to call it (creator and invoker are often, but don't have to be, the same code)

### Implementation Checklist

1. Define a command interface with a generic verb method signature
2. Create one or more concrete command classes encapsulating: a receiver, the method to invoke, and arguments to pass
3. Instantiate a command object for each deferred execution request
4. Pass the command object from the creator to the invoker
5. The invoker decides when to execute it

### Example

```java
public interface ICommand {
    void execute();
}

public class JumpCommand implements ICommand {
    private Player player;
    public JumpCommand(Player player) { this.player = player; }
    public void execute() { player.jump(); }
}
```

- The `JumpCommand` object holds all context needed (the `player`) to perform the action later, from anywhere
- A controller can bind commands to inputs (`A = jumpCommand`, `B = duckCommand`) and remap them at runtime by reassigning which command object an input points to

### Real-World Uses

- Remappable game controls (swap what a button does at runtime)
- Undo/redo (pair verbs like `activate`/`deactivate`, `do`/`undo`)
- Recording and replaying a queue of actions
- Sending commands over a network (remote desktop/screen-share control, multiplayer games sending "I jumped" to a server)
- Context-sensitive controls (same button does different things depending on state, e.g. walking vs. driving vs. flying)

## 4. Interfaces (Refresher + Rules)

- Declared like a class: `public interface IStone { ... }`
- A class uses it via `implements`: `public class PowerStone implements IStone`
- Rules:
  - Methods must be abstract or static
  - File extension `.java`; file name matches interface name; compiles to a `.class` file
  - Cannot be instantiated (`new` fails); no constructors
  - All fields are implicitly `public static final` (symbolic constants)
  - A class **implements** an interface; an **interface** can `extend` other interfaces

## 5. Abstract Classes (Recap)

- **Abstract method** — no body/definition; must be declared inside an interface or abstract class; can still have parameters and a return type; default (unspecified) access is package-private
- **Abstract class** — cannot be instantiated (`new` fails); can only be extended; may mix regular fields/methods with abstract methods

## 6. Abstract Class vs. Interface — When to Use Which

- **Abstract class** — when code should be shared among closely related classes, you need access modifiers other than `public`, or you need non-static/non-final fields
- **Interface** — when the classes are otherwise unrelated, you just want to specify *behavior* (not implementation), or you need multiple inheritance of type (e.g. a class implementing both `Comparable` and `Cloneable`)

## 7. Class vs. Interface Inheritance Rules

- A class can `extend` only **one** parent class
  - Why: if two fully-defined parent classes both implemented the same method differently, the compiler wouldn't know which version to use (ambiguity)
- A class can `implement` **multiple** interfaces
- An interface can `extend` multiple other interfaces
- `final` class → cannot be extended
- `abstract` + `final` on the same class is invalid — `abstract` requires it be extended, `final` forbids extension

## 8. Software Testing: Planning Basics

- In Agile, test planning happens iteratively (plan → figure out pre/post-conditions → design → repeat), not as a rigid one-time waterfall step
- Testing = detective work — testers must be suspicious of everything and try not to leave anything uncovered
- Two major categories: **black box** (functional) and **white box** (structural, a.k.a. "glass box") testing

## 9. Black Box Testing

- Tester treats the software as a black box: known inputs → unknown internal process → observed outputs
- Test cases derived **solely from design specs**, not from source code
- Tester cannot see source code, internal data, or internal design docs
- Pairs naturally with **TDD** — you can write black-box-style tests before any code exists, based only on the spec (what goes in, what should come out)

## 10. Equivalence Class Partitioning

- Divide possible inputs into groups ("equivalence classes") — typically **valid** vs. **invalid**
- Run one representative test case per equivalence class
- Requires understanding both the required input *and* how the software was likely designed (e.g. a login field accepting either "username" or "email" are two different valid equivalence classes, not one)
- Example (Celsius→Fahrenheit converter): valid class = numeric input (ints/floats); invalid class = non-numeric, empty/null, whitespace, repeated/duplicated characters, emoji

## 11. Boundary Value Analysis (Edge Cases)

- Programs often fail at **boundary values** — values that sit right at the edge of an equivalence class
- A boundary value test case = data at (or just outside) the edge of a valid/invalid range
- Edges are a disproportionately rich source of bugs (classic example: integer/counter rollover, like an old game's score counter wrapping back to 0 after hitting its max)
- For a range like `0 <= x <= 1.0`, good test values: just below the low edge (`-1`), the low edge itself (`0`), the middle (`0.5`), just below the high edge (`0.9999`), the high edge itself (`1.0`), and just above it (`1.1`)
- Also test unusual/special situations: empty input, `null`, blank/whitespace, repeated values, negative numbers, non-numeric in numeric fields (or vice versa), values too big/too small

## 12. White Box Testing (Structural / "Glass Box")

- Tester **can** see source code, design docs, and observe runtime behavior/internal data — this is what developers do most often since they wrote the code
- Three approaches, in order of thoroughness vs. practicality:
  - **Control flow** — exhaustive: test every possible combination of conditions/paths; accurate but becomes impractical fast as code grows
  - **Statement coverage** — every line/statement executed at least once; the most practical, commonly-used target
  - **Edge/branch coverage** — convert code to a flow graph, ensure every edge/branch is traversed at least once; a hybrid, but tedious to construct by hand for anything non-trivial
- 100% statement coverage is always achievable in principle — any code that can never execute should just be deleted

## 13. Test Coverage in Practice

- IDEs (IntelliJ), build tools (Gradle), and test frameworks (JUnit) can run tests "with coverage" and report % of classes/methods/lines/branches exercised
- Coverage reports highlight untested lines/branches directly in the source (e.g. a `catch` block that never fired)
- Many teams/orgs (mentioned: Google-style practices) require a minimum coverage threshold before code can be merged
- Takeaways: **boundary value analysis** is the most broadly useful testing technique to internalize; **statement coverage** is the metric used most often day-to-day

## 14. JavaFX Overview

- A modern GUI toolkit for Java, successor to Swing (split out of the JDK around Java 11 as a separate library)
- Supports UI controls (buttons, labels, text fields, etc.), CSS styling, FXML (JavaFX's XML layout format, usable with drag-and-drop Scene Builder), and 2D/3D graphics
- Building a UI is just normal object-oriented Java — constructing objects and calling setters, nothing exotic

## 15. Application Lifecycle & Class Hierarchy

- Entry class `extends Application` (an **abstract class** that requires overriding `start(...)`)
- `main` only needs to call `launch(args)`, which hands control to JavaFX and eventually calls `start(Stage stage)`
- UI class hierarchy: `Object` → `Node` → `Parent` → `Region` → `Control` (buttons, labels, text fields extend `Control`) and separately → `Pane` (`StackPane`, `VBox`, `HBox` extend `Pane`)

## 16. Stage, Scene, and Scene Graph

- **Stage** — the OS window itself (title bar, etc.)
- **Scene** — the content displayed inside the window; holds the root node and defines width/height
- **Scene graph** — the tree of all visual nodes; the scene's root is a layout container (e.g. a `StackPane`), and its children are controls or nested containers
- Typical flow: build controls → add them to a root pane's children → wrap the root in a `Scene` → `stage.setScene(scene)` → `stage.show()`

## 17. Layout Containers

- **StackPane** — stacks children on top of each other
- **VBox / HBox** — arrange children vertically / horizontally
- **BorderPane** — top/bottom/left/right/center regions
- **GridPane** — grid-based layout (conceptually similar to CSS grid in web development)
- All extend `Pane`

## 18. Event Handling: Lambdas vs. Anonymous Inner Classes

- Two equivalent ways to handle a UI event, e.g. a button click:
  - **Lambda**: `button.setOnAction(e -> toggle(button));`
  - **Anonymous inner class**: `button.setOnAction(new EventHandler<ActionEvent>() { public void handle(ActionEvent e) { toggle(button); } });`
- `setOnAction(...)` expects an `EventHandler` — a **functional interface** (single abstract method), which is exactly what makes the lambda shorthand valid
- The anonymous inner class explicitly implements the interface inline and overrides its one method (`handle`)
- Both approaches are functionally identical — lambda is just more concise

## 19. Gradle (Build System)

- **Gradle** = automated build system (lineage: Make → Ant → Maven → Gradle); other ecosystems have their own (Google's Blaze, Meta's Buck, Rust's built-in Cargo, etc.)
- Declares dependencies/plugins/versions in `build.gradle` — e.g. which JavaFX version, Java source compatibility, main class entry point, test runner (JUnit)
- Removes the pain of manually downloading `.jar` files and configuring run settings by hand
- Key commands (via the bundled wrapper): `./gradlew build` (compile/package), `./gradlew run` (launch the app), `./gradlew test` (run unit tests) — on Windows, `gradlew.bat` instead of `./gradlew`
- Standard project layout: source in `src/main/java`, tests in `src/test/java`, wrapper scripts (`gradlew`/`gradlew.bat`) and `build.gradle`/`settings.gradle` at the project root
