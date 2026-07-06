# CST338 Quiz — Week 2, Set B

Based on: CST338_Notes_wk02.md

Instructions: circle or type your answer letter next to each question. When you're ready, ask me to grade this quiz before generating the next one.

**1. Given `Product p = new Book();` and both classes override `toString()`, which version runs when you call `p.toString()`?**
A. `Product`'s version, because `p` is declared as `Product`
B. `Book`'s version, determined at runtime by the actual object type
C. It's a compile error
D. Both versions run, concatenated

Your answer: ___

**2. Which of these is true about an abstract class in Java?**
A. It can be instantiated directly with `new`
B. It cannot be instantiated, only extended, and may contain both abstract and regular methods
C. It cannot have any regular (non-abstract) methods
D. It automatically implements all its own abstract methods

Your answer: ___

**3. If you call `.get(key)` on a `HashMap` for a key that doesn't exist, what happens?**
A. It throws a `NoSuchElementException`
B. It returns `null`
C. It returns the key itself
D. It automatically adds the key with a `null` value and returns `null`

Your answer: ___

**4. A static variable belongs to:**
A. Each individual object/instance separately
B. The class itself, shared across all instances
C. Only the `main` method
D. Only static methods, not the class

Your answer: ___

**5. Which statement about wrapper classes (e.g., `Integer`) is correct, per the notes?**
A. Wrapper classes have a no-argument constructor just like other classes
B. Wrapper classes do NOT have a no-argument constructor
C. Autoboxing requires manually calling `.valueOf()` every time
D. Wrapper classes cannot store constants like `MAX_VALUE`

Your answer: ___
