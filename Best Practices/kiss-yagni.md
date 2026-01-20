# KISS and YAGNI

Principles for keeping software simple and focused.

## KISS (Keep It Simple, Stupid)
Most systems work best if they are kept simple rather than made complicated; therefore, simplicity should be a key goal in design, and unnecessary complexity should be avoided.

### Real-Time Use Case
**Authentication Logic**: Instead of building a custom multi-factor authentication system with biometric support from day one, you start with a simple Email/Password login using a trusted library like IdentityServer or Auth0. You only add complexity when the security requirements actually demand it.

### Why KISS?
- **Lower Bug Density**: Complex code hides more bugs.
- **Easier Onboarding**: New developers can understand the system faster.
- **Faster Maintenance**: Simple code is easier to refactor.

## YAGNI (You Ain't Gonna Need It)
A programmer should not add functionality until deemed necessary.

### Real-Time Use Case
**Database "Generic" Wrappers**: Developers often spend weeks building a "Generic Repository Pattern" that can switch between SQL, MongoDB, and XML, thinking they might change databases later. In 99% of cases, the DB never changes, and the complex abstraction becomes a maintenance nightmare.
**YAGNI says**: Just use the SQL client directly until you actually need to switch.

## Pros and Cons

| Principle | Pros | Cons |
| :--- | :--- | :--- |
| **KISS** | High reliability, easy debugging, clear intent. | Can sometimes be interpreted as "Lazy" or lacking robust error handling if taken to extremes. |
| **YAGNI** | Saves time, prevents "bloated" codebases, reduces technical debt. | If a feature *is* eventually needed, you might need to refactor significantly if the initial design was too rigid. |

## How to follow them
1. **Don't over-engineer**: Solve the problem you have today, not the one you might have in two years.
2. **Avoid "Future-Proofing"**: Every line of code is a liability. Only write what is needed for the current requirement.
3. **Use standard libraries**: Don't reinvent the wheel.
