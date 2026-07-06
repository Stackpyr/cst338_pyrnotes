# CST338 Quiz — Week 2, Set A

Based on: CST338_Notes_wk02.md

Instructions: circle or type your answer letter next to each question. When you're ready, ask me to grade this quiz before generating the next one.

**1. Two Strings are created with `new String("cat")` each. What does `==` return when comparing them, and why?**
A. `true`, because string literals are always pooled
B. `false`, because `new String(...)` creates separate objects on the heap, bypassing the string pool
C. `true`, because Strings always override `==`
D. It throws a compile error

Your answer: ___

**2. If a class never overrides `equals()`, and none of its parent classes override it either, what does `equals()` end up doing?**
A. Comparing field values automatically
B. Comparing memory addresses — same as `==`
C. Always returning `true`
D. Throwing a `NullPointerException`

Your answer: ___

**3. Per the equals/hashCode contract, if two objects are equal according to `.equals()`, what must be true of their `hashCode()` values?**
A. They must be different
B. They must be the same
C. There's no requirement
D. Only one of them needs a `hashCode()`

Your answer: ___

**4. When class `Book` is declared as `public class Book extends Product`, which of the following is true?**
A. `Book` automatically inherits `Product`'s constructors
B. `Book` does NOT inherit `Product`'s constructors and must define its own
C. `Product` can now access `Book`'s new fields directly
D. `Book` can no longer have its own fields

Your answer: ___

**5. Which statement about `this(...)` and `super(...)` in a constructor is correct?**
A. Both can be called together in the same constructor, in any order
B. Only one of them can be used per constructor, and it must be the first statement
C. Neither is ever necessary in Java
D. `super(...)` can only be called in a static method

Your answer: ___
