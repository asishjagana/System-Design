# DRY (Don't Repeat Yourself) vs. WET (Write Everything Twice)

Core principles regarding code duplication and abstraction.

## DRY (Don't Repeat Yourself)
"Every piece of knowledge must have a single, unambiguous, authoritative representation within a system."

### Real-Time Use Case
**Database Connection String**: Instead of hardcoding the connection string in every service/class, you store it in a single configuration file or environment variable. If the DB address changes, you update it in **one** place.

### Why DRY?
- **Maintainability**: Change logic once, reflected everywhere.
- **Readability**: Code is more concise.
- **Testing**: Only need to test the logic in one location.

## WET (Write Everything Twice / We Enjoy Typing)
The opposite of DRY. Sometimes, duplicating code is better than creating a "wrong" abstraction.

### Real-Time Use Case
**Microservices DTOs**: Service A and Service B both have a `User` object. They look the same *now*, but in 6 months, Service A might need `TaxID` while Service B needs `ProfilePicture`. 
If you shared a single `Library.Models.User` to stay DRY, you've now **coupled** two independent services. Changing the model for A breaks B.

## Pros and Cons

| Principle | Pros | Cons |
| :--- | :--- | :--- |
| **DRY** | Reduced bugs, easier updates, consistent logic. | **Premature Abstraction**: Harder to read if you abstract too early. High coupling if different contexts share code. |
| **WET** | **Loose Coupling**: Changes in one place don't break another. Easy to understand (no complex abstractions). | **Maintenance Burden**: If a bug is fixed in one spot, you might forget to fix it in the other duplication. |

## The Rule of Three
Don't abstract the first time you see duplication. Don't even necessarily do it the second time. But the **third time** you write the same code, it's time to extract it into a function or class.

> "Duplication is far cheaper than the wrong abstraction." — Sandi Metz
