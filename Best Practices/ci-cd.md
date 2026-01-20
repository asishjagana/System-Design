# CI/CD (Continuous Integration / Continuous Deployment)

Automating the path from code to production.

## Continuous Integration (CI)
Developers merge their code changes back to a central branch as often as possible. Each merge triggers an automated build and test sequence.

### Key Steps in CI:
1. **Build**: Compile the code.
2. **Unit Tests**: Run automated tests.
3. **Linting**: Check code style and standards.
4. **Security Scan**: Check for vulnerable dependencies.

## Continuous Deployment/Delivery (CD)
- **Continuous Delivery**: Code is always in a "ready-to-deploy" state. Deployment to production is a manual click.
- **Continuous Deployment**: Every change that passes the CI pipeline is automatically deployed to production.

## Real-Time Use Case: GitHub Actions
A developer pushes a commit to their repo. GitHub Actions automatically:
- Provisions a virtual machine.
- Installs dependencies.
- Runs the test suite.
- If successful, builds a Docker image and pushes it to AWS.

## Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Smaller Releases**: Frequent, small updates are less risky than giant monster releases. | **Initial Setup**: Setting up robust pipelines takes time and expertise. |
| **Immediate Feedback**: Developers know within minutes if they broke the build. | **Tooling Costs**: CI/CD tools (CircleCI, GitHub Actions) can become expensive at scale. |
| **Consistency**: Manual deployment errors (forgotten config, wrong file version) are eliminated. | **Testing Dependency**: CD is only as good as your automated test suite. |

## Common Tools
- **Jenkins**: Open-source, highly customizable.
- **GitHub Actions**: Integrated directly into the repository.
- **GitLab CI**: Built-in to GitLab.
- **Azure DevOps**: Robust suite for enterprise needs.
