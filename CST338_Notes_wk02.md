# CST338 Notes: Overriding & Inheritance

## 1. `==` vs `.equals()`

- `==` on objects compares **memory address** (reference), not content
- Two objects with identical field values but different instances → `==` is `false`
- Strings can appear to work with `==` (string pool/interning) — don't rely on this
- **Rule: never use `==` to compare object content — use `.equals()`**

### String Pool / Interning

- JVM keeps a **string pool** (cache of string literals) on the heap
- String literals (`"abc"`) auto-**interned** — reused from pool if an identical literal already exists
  - `String a = "abc"; String b = "abc";` → `a == b` is `true` (same pooled object)
- `new String("abc")` bypasses the pool → new object → `==` is `false` even with same content
- `.intern()` manually adds/reuses a string from the pool
- Because `==` result depends on *how* the string was created, it's unreliable — this is exactly why the notes say not to rely on it

### 3 Ways to Compare Strings

1. `==` — reference/pool comparison (unreliable, avoid)
2. `.equals()` — content comparison ✅ **recommended**
3. `.compareTo()` — lexicographic comparison, returns `int` (`0` = equal); useful for sorting

## 2. The `Object` Class

- Every class inherits from `Object` (capital O) — root of all classes
- Non-primitive = `Object` subtype
- If a class has no other explicit parent, it implicitly `extends Object`; if it has a parent, that parent eventually leads back to `Object`
- `Object` provides: `toString()`, `equals()`, `hashCode()`, `clone()`, `getClass()`, more

## 3. `equals()` Method (auto-generated pattern)

Checks, in order:
1. `this == o` — same memory address → equal
2. `o == null` → not equal
3. `getClass()` match (or `instanceof`) → different class → not equal
4. Cast `o` to correct type, compare fields

- Auto-generate via IDE rather than memorizing

### Default `equals()` Resolution

- No override in a class → defaults to the closest parent class's overridden `equals(Object o)`
- No parent override anywhere → falls all the way back to `Object#equals(Object o)`
- `Object`'s version **== `==`** — true *if and only if* both variables reference the same object
- So: not overriding `equals()` means you're testing **object (reference) equality**, not functional/content equality

## 4. `String.equals()`

- Strings override `equals()` specially — checks:
  1. Is it a `String` (`instanceof`)?
  2. Same length?
  3. Same encoding?
  4. Char-by-char (byte-by-byte) comparison

### `String.replace()`

- Overloaded — **not** char-only:
  - `replace(char oldChar, char newChar)` — e.g. `"hello".replace('l', 'p')` → `"heppo"`
  - `replace(CharSequence target, CharSequence replacement)` — e.g. `"hello".replace("ll", "LL")` → `"heLLo"`
- Strings are immutable — `.replace()` returns a **new** `String`, doesn't modify the original

## 5. `toString()`

- Default (inherited, not overridden) → `ClassName@hashcode` (memory address)
- Override with `@Override public String toString()` for a meaningful representation
- Auto-generated version: class name + field values concatenated

## 6. Annotations & `@Override`

- `@Override` = annotation (like `@Test`)
- Annotations = **decorator pattern** — change behavior without changing underlying code
- `@Override` tells interpreter: use this version, not parent's
- Also triggers compiler/linter check that signature matches parent method
- Not required to work, but prevents silent bugs — strongly recommended
- These align with the Decorator design pattern (they "decorate" a method with extra behavior without changing the underlying code)
- **Overriding rule:** the overriding method must match name, parameters, **and** return type of the parent's method
- Call the parent's version from inside an override with `super.methodName(...)` (common for `toString()`: `super.toString() + extra stuff`)

## 7. Method Signature

- Signature = method name + parameter types/order
- Return type and access modifier do **NOT** count toward signature
- Same name + different params = valid overload
- Same name + same params + different return type = **not allowed** (ambiguous)

## 8. Inheritance

- Two forms: **extending a class** or **implementing an interface**
- UML: solid arrow = extends; dotted arrow = implements; arrow points to parent/interface
- Subclass can access parent's `public` and `protected` members only (not `private`, not package-private unless same package)
- Lets a class reuse/access parent's public + protected methods/fields
- Groups related objects under a common parent type

### Terminology

- **Superclass** = parent class = base class — the existing class
- **Subclass** = child class = derived class — the new class being created
- Terms are interchangeable; "superclass"/"subclass" tie to the `super` keyword

### Benefits

- Code reuse
- Single location for debugging/maintenance
- Supports open/closed principle: classes closed to modification, open to extension (extend instead of editing the parent)

### `extends` Keyword

- `public class Book extends Product;` — Book is subclass, Product is superclass
- Subclass does **NOT** inherit constructors — must define its own
- Anything defined in the subclass is only available to the subclass — visibility flows **one direction** (parent → child, not child → parent)

### Comparing Subclass/Superclass Objects

- Comparing a subclass instance to a superclass instance with `.equals()` generally fails (different `getClass()`/not `instanceof`)
- Generally **not good design** to compare across the hierarchy — a subclass and superclass usually represent meaningfully different things (e.g., a Burger ≠ a plain Sandwich)

## 9. `hashCode()`

- If you override `equals()`, you must override `hashCode()` too (part of `Object` contract) — otherwise you "break the contract"
- Used internally when comparing/looking up objects (e.g., hash-based collections)
- Auto-generate via IDE; not worth memorizing the formula
- **Contract:** if `equals()` says two objects are equal, `hashCode()` **must** return the same value for both
- **Converse is NOT required:** equal `hashCode()` values do not guarantee the objects are equal (per `equals()`)

## 10. References, Aliases, Shallow vs. Deep Copy

- `obj2 = obj1` → **alias**, not a copy — both point to same memory address
- Changing a field via one name changes it for both (same object)
- **Shallow copy** — copies the reference only, still same underlying object
- **Deep copy** — instantiates a new object, copies values over independently
- Shallow copies can break encapsulation (unexpected shared mutation)

## 11. Test-Driven Development (TDD)

- Write the smallest failing unit test first
- Write minimal code to make it pass
- Repeat: test → code → test → code
- Commit after each passing test; squash/rebase into a clean commit at end of day, then push

## 12. `this` and `super` in Constructors

- `this(...)` — calls another constructor **in the same class** (constructor chaining)
- `this.field` — disambiguates a field from a parameter of the same name (avoids "clobbering"/shadowing)
- `super(...)` — calls the **parent class's constructor**
- If a subclass constructor doesn't explicitly call `super(...)`, Java auto-inserts a call to the parent's **no-arg** constructor
  - If the parent has no no-arg constructor (only a parameterized one) and the subclass doesn't call `super(...)` explicitly → compile error ("no default constructor found")
- `this(...)` and `super(...)` can't both be used in the same constructor; whichever is used must be the **first statement**

## 13. HashMap

- Generic key-value container: `HashMap<K, V>`
- Example: `HashMap<String, Integer> fillings = new HashMap<>();`
- Keys must be **unique** and a **reference type** (must have a `hashCode()` — no primitives as keys, but wrapper classes work)
- Values can be any type
- Fast lookup — matches by hash value
- `.put(key, value)` — add/overwrite (same key = overwrite); returns the **previous** value for that key, or `null` if it's new
- `.get(key)` — returns the value, or **`null`** if key doesn't exist — does **not** throw
- `.getOrDefault(key, default)` — like `.get()`, but returns `default` instead of `null` on a missing key
- `.containsKey(key)` — `boolean`; check before `.get()` if `null` would be ambiguous (e.g. value type is Integer and a real value could itself be `null`)
- `.remove(key)` — removes the entry, returns its value, or `null` if key wasn't present
- Other useful methods: `.keySet()`, `.values()`, `.size()`, `.putIfAbsent()`

### Markov Chain Example (HashMap use case)

- First-order Markov chain = a look-ahead predictor: given the current word, what's the most likely next word?
- Built with `HashMap<String, ArrayList<String>>` — key = a word (token), value = list of words observed to follow it
- Special key marks sentence starters (words that follow end-of-sentence punctuation)
- To generate text: pick a random starter word, then repeatedly pick a random word from the current word's list until hitting a punctuation-ending word
- Same underlying idea (much simpler) as what large language models use for next-token prediction

### Enhanced For-Each Loop

- Syntax: `for (Type name : collection) { ... }`
- More efficient/cleaner than an index-based loop; preferred when you don't need the index
- Works on `ArrayList`: `for (String name : names)`
- Works on `HashMap`:
  - Keys: `for (String key : map.keySet())`
  - Values: `for (Integer val : map.values())`
  - Both: `for (Map.Entry<String, Integer> entry : map.entrySet())`

## 14. Polymorphism, Dynamic Binding & Casting

- **Four pillars of OOP:** polymorphism, inheritance, encapsulation, abstraction
- **Polymorphism** ("many forms") — a variable of type X can reference an object of type X, any subclass of X, or any class implementing interface X
- Benefits: general code that works across many types, uniform processing, add new types without changing existing code
- Example: `Product p = new Book();`

### Static vs. Dynamic Type / Dynamic Binding

- **Static type** — the declared type (`Product`)
- **Dynamic type** — the actual object type (`Book`)
- **Dynamic binding** — which overridden method runs is based on the actual (dynamic) object type, decided at **runtime**, not compile time
- `p.toString()` calls `Book`'s `toString()`, even though `p` is declared as `Product`

### Upcasting vs. Downcasting

- **Upcasting** (subclass → superclass) — implicit, always safe: `Product p = new Book();`
- **Downcasting** (superclass → subclass) — requires an **explicit cast**, can throw `ClassCastException` if the object isn't actually that subclass
- Check safety first with `instanceof` before downcasting

## 15. The `final` Modifier

- `final` **method** — cannot be overridden in a subclass
- `final` **class** — cannot be extended at all
- `final` **variable** — value/reference cannot be reassigned after initialization
  - For a `final` container (e.g. `ArrayList`, `HashMap`): the reference can't change, but its **contents** still can
- `String`, `Integer`, and other wrapper classes are `final` (cannot be extended)

## 16. Abstract Classes & Interfaces

- **Abstract class** — an incomplete class that **cannot be instantiated**, only extended
- Declared with `abstract class`; can mix regular fields/constructors/methods with abstract methods
- **Abstract method** — signature only, no body, ends with `;` (e.g., `public abstract void example();`); must live in an abstract class or interface
- Subclasses must implement all abstract methods, or be declared abstract themselves
- Benefit over a plain (non-abstract) superclass method: the method is part of the superclass's type, so it can be called through a parent reference with no casting, and dynamic binding still calls the subclass's version
- **Interfaces** — all non-static methods are abstract and `public` by default

## 17. Functional Mapping (Lambdas & Streams)

- **Mapping** — run a function on each item of a collection to compute a new value for that item, in place
- `list.replaceAll(lambda)` — calls the lambda once per item, stores the result back into the same collection; returns nothing (`void`), so the list must be returned separately if needed

### Lambda Syntax

- `n -> n * 2` — parameter name, `->`, then the expression for the new value
- Type is inferred from context (no explicit type needed)

### Example

```java
public List<Integer> doubling(List<Integer> nums) {
    nums.replaceAll(n -> n * 2);
    return nums;
}
```

### `replaceAll()` Limitations

1. New value must be the **same type** as the original (e.g. `int` → `int`); to change type, use streams
2. Works best when each item is handled independently — no easy way to reference "neighboring" elements (use a traditional loop for that)

### Streams (More Flexible Mapping)

```java
public List<Integer> doubling(List<Integer> nums) {
    return nums.stream()
        .map(n -> n * 2)
        .collect(Collectors.toList());
}
```

- `.stream()` — sets up the collection for mapping/filtering
- `.map(lambda)` — transforms each element (can be chained, can change type)
- `.collect(Collectors.toList())` — collapses the stream back into a list/set/etc.
- Unlike `replaceAll()`, streams return a **new** collection rather than mutating the original

## 18. Static Variables & Methods

- **Static variable** — belongs to the **class**, shared by all instances (not per-object like an instance variable)
- Declared like a normal field + `static`: `private static int numOfCarsMade;`
- Value **persists** across instances — incrementing it via one object affects what every other object/new object sees
- Should be `private` unless also `final` — otherwise anything can mutate it uncontrolled
- Access from outside the class via `ClassName.field` / `ClassName.method(...)`

### `this` and Static Context

- `this` refers to the current **object instance** — static methods belong to the class, not an instance, so there's no `this` to refer to
- Using `this` inside a static method → compile error: *"cannot be referenced from a static context"*

### Static/Instance Call Rules

- Static methods **cannot** call instance methods or reference instance variables (no object context to use)
- Static methods **can** call other static methods and reference static variables
- Instance methods **can** call static methods and reference static variables (no restriction going that direction)
- A single class can freely mix static and non-static methods (e.g., every class can have `public static void main`)

### Static Constants

- `public static final` = a symbolic/static constant, e.g. `public static final int BIRTH_YEAR = 1982;`
- `final` locks the **reference** — for a container (e.g. `ArrayList`), the variable can't be reassigned, but its contents can still change

## 19. The `Math` Class

- All fields/methods are `static` — call via `Math.xyz(...)`, no import or instantiation needed
- Constants: `Math.PI`, `Math.E`
- Common methods: `Math.pow(base, exp)`, `Math.abs(x)`, `Math.min(a, b)`, `Math.max(a, b)`, `Math.round(x)`, `Math.ceil(x)`, `Math.floor(x)`, `Math.sqrt(x)`

## 20. Wrapper Classes

- Reference-type equivalents of primitives: `Byte`, `Short`, `Integer`, `Long`, `Float`, `Double`, `Boolean`, `Character`
- Provide useful constants and **static** methods — this is why they're relevant here

### Boxing (primitive → wrapper object)

- Manual: `Integer intObject = Integer.valueOf(42);` — takes the primitive as an argument
- The created object stores its own copy of the primitive value
- **A wrapper class has no no-argument constructor** — can't do `new Integer()`
- **Autoboxing** — Java does this conversion implicitly: `Integer i = 42;` (no `valueOf()` needed)

### Unboxing (wrapper object → primitive)

- Manual: `int i = intObject.intValue();` — also `.byteValue()`, `.shortValue()`, `.doubleValue()`, etc.
- **Auto-unboxing** — Java does this implicitly too: `int i = intObject;` (no `.intValue()` needed)

- Constants: `Integer.MAX_VALUE`, `Integer.MIN_VALUE`, `Double.MAX_VALUE`, etc.
- Parsing a `String` to a number: `Double.parseDouble(str)`, `Integer.parseInt(str)`
- Converting a number to a `String`: `Double.toString(x)` (the "proper" way) vs. the common shortcut `x + ""` (concatenation forces boxing + `toString()` under the hood)

## 21. `var` Keyword (Local Variable Type Inference)

- Compiler infers the type from the right-hand side of the assignment
- Only for **local variables** — not fields, parameters, or return types
- Must be initialized at the point of declaration (`var x;` alone is invalid)
- Works in both static and non-static contexts

## 22. Static Methods in Interfaces

- An interface can define a static method with a full body
- Implementing classes **cannot override** it — it's shared, fixed utility functionality
- Useful for grouping related utility/helper methods with the interface they support

## 23. Enhanced `instanceof`

- Old way: `if (obj instanceof String) { String s = (String) obj; ... }` — check, then manually cast
- Modern way: `if (obj instanceof String s) { ... }` — checks **and** casts into `s` in one step, ready to use immediately
