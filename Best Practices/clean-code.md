# Clean Code Principles

Writing code for humans first, computers second.

## 1. Meaningful Names
Variables and functions should reveal intent.
- **Bad**: `var d = 5; // days since last update`
- **Good**: `var daysSinceLastUpdate = 5;`

## 2. Functions should do One Thing
A function should be small and have a single responsibility.
- **Bad**: `ValidateAndSaveUserAndSendEmail()`
- **Good**: `SaveUser()`, `SendWelcomeEmail()`, `ValidateUserData()`

## 3. Comments are a Failure
"Don't comment bad code—rewrite it."
Code should be so clear that it doesn't need comments to explain *what* it is doing. Use comments only to explain *why* something unusual was done (Rational).

## 4. The Boy Scout Rule
"Leave the campground cleaner than you found it."
Whenever you work on a file, make at least one small improvement (refactor a variable, fix a typo, split a long function).

## Real-Time Use Case: Code Reviews
Clean code isn't just about the author. It's about the team. When a coworker reviews your code, they should be able to understand the flow without you sitting next to them explaining it.

## Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Legacy Proof**: Makes codebases maintainable for years. | **Subjective**: "Clean" can be interpreted differently by different developers. |
| **Reduced Turnover Impact**: New developers can be productive faster. | **Slower Initial Writing**: Thinking of perfect names and small functions takes effort. |
| **Fewer Bugs**: Logical, simple code is easier to verify. | |

## Key Resource
*Clean Code* by Robert C. Martin (Uncle Bob) is the definitive book on this topic.
