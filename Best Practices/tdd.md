# TDD (Test Driven Development)

A software development process that relies on the repetition of a very short development cycle.

## The Red-Green-Refactor Cycle
1. **RED**: Write a failing unit test for a small piece of functionality.
2. **GREEN**: Write the minimum amount of code to make the test pass.
3. **REFACTOR**: Clean up the code while ensuring the test still passes.

## Real-Time Use Case: Financial Calculator
If you are building a tax calculation engine:
1. You write a test: `CalculateTax(100) should return 10`. (Test fails because method doesn't exist).
2. You implement: `return amount * 0.1;`. (Test passes).
3. You refactor: Move the `0.1` into a configuration constant `TaxRate`. (Test still passes).

## Testing Levels
- **Unit Tests**: Test a single function or class in isolation (Mocks are used).
- **Integration Tests**: Test how multiple components work together (e.g., API calling a Real DB).
- **E2E (End-to-End) Tests**: Test the entire user flow from the browser/app (e.g., Selenium/Cypress).

## Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Confidence**: You can refactor large parts of the system knowing the tests will catch breaks. | **Time Consuming**: Initially takes longer to write code because you're writing tests first. |
| **Better Design**: TDD naturally forces you to write decoupled, "testable" code. | **Maintenance**: Tests are code too. If requirements change, tests must be updated. |
| **Documentation**: Tests act as living documentation of how the system is supposed to behave. | **False Security**: 100% code coverage doesn't mean 100% bug-free. |

## When to use?
TDD is essential for complex business logic and core system components. It is less critical for UI styling or exploratory prototypes where the design is constantly shifting.
