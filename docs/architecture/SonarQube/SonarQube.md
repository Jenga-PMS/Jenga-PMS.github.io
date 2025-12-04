## Backend

### 1. Reliability Improvements

- **Refactor Field Injection to Constructor Injection (via Lombok)**    
    - **Why:** Field injection (e.g., `@Autowired` on private fields) is often considered an anti-pattern because it hides dependencies and requires the Spring Context for instantiation. Moving to Constructor Injection (using Lombok’s `@RequiredArgsConstructor`) allows fields to be marked `final` (immutability), ensures the object is in a valid state upon creation, and simplifies unit testing by allowing direct mock injection.
- **Replace Generic Exceptions with Specific Library Exceptions**
    - **Why:** Throwing generic `Exception` or `RuntimeException` obscures the root cause. Using specific exceptions (e.g., `IllegalArgumentException`, `EntityNotFoundException`) improves error handling granularity and provides meaningful stack traces for debugging.
- **Return Empty Collections instead of Null**    
    - **Why:** Adopting the **Null Object Pattern** for arrays and lists prevents `NullPointerException` (NPE) on the client side. It removes the need for defensive null-checks in consumer code, streamlining iteration logic.
### 2. Maintainability & Clean Code

- **Standardize Logging (Replace System.out / err)**
    - **Why:** `System.out` prints to standard output without metadata. Replacing this with a Logger facade (e.g., SLF4J) enables log levels (INFO, WARN, ERROR), timestamping, and integration with log aggregation tools suitable for production environments.
- **Enforce Naming Conventions**
    - **Why:** Renaming methods and variables to adhere to standard Java conventions (CamelCase, semantic naming) reduces cognitive load and ensures the code is **self-documenting**.
- **Simplify Control Flow (Remove Complex Ternary Operations)**
    - **Why:** While concise, nested ternary operators reduce readability. Refactoring these into explicit `if/else` blocks improves scannability and simplifies step-through debugging.
- **Inline Returns (Remove Intermediate Variables)**
    - **Why:** Removing variables declared immediately before a return statement reduces visual noise and boilerplate, focusing the reader immediately on the operation's result.
- **Dead Code Elimination**
    - **Why:** Removing redundant code reduces the application footprint and decreases the maintenance surface area.

### Visual Comparison

Before:

![!\[\[Pasted image 20251204135355.png\]\]](<Pasted image 20251204135355.png>)

After:

![!\[\[Pasted image 20251204135411.png\]\]](<Pasted image 20251204135411.png>)