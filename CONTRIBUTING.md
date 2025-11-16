# Contributing to Quiz Application with Microservices

First off, thank you for considering contributing to this project! It's people like you that make this a great learning resource.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Commit Message Guidelines](#commit-message-guidelines)

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](./CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When creating a bug report, include:

- **Clear title and description**
- **Steps to reproduce** the issue
- **Expected behavior** vs **actual behavior**
- **Environment details** (Java version, OS, etc.)
- **Logs or error messages** if applicable
- **Screenshots** if relevant

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- Use a clear and descriptive title
- Provide a detailed description of the proposed enhancement
- Explain why this enhancement would be useful
- Include examples or mockups if applicable

### Pull Requests

1. Fork the repository and create your branch from `main`
2. Make your changes following our coding standards
3. Add or update tests as needed
4. Ensure all tests pass
5. Update documentation if you're changing functionality
6. Submit a pull request!

## Development Setup

### Prerequisites

- Java 17 or higher
- Maven 3.6+
- Docker & Docker Compose
- PostgreSQL 15+ (or use Docker)
- Git

### Local Development

```bash
# Clone your fork
git clone https://github.com/YOUR-USERNAME/Quiz-Application-With-Microservices.git
cd Quiz-Application-With-Microservices

# Add upstream remote
git remote add upstream https://github.com/TheAvgProgrammer/Quiz-Application-With-Microservices.git

# Create a feature branch
git checkout -b feature/my-new-feature

# Build the project
mvn clean install

# Run tests
mvn test
```

### Running Services Locally

```bash
# Option 1: Docker Compose (Recommended)
docker-compose up -d

# Option 2: Manual (start in order)
# 1. Service Registry
cd MicroserviceTutorials/service-registry && ./mvnw spring-boot:run

# 2. API Gateway
cd MicroserviceTutorials/api-gateway && ./mvnw spring-boot:run

# 3. Quiz Service
cd MicroserviceTutorials/quiz-service && ./mvnw spring-boot:run
```

## Pull Request Process

1. **Update Documentation**: If you're adding or changing functionality, update the README.md and any relevant documentation.

2. **Add Tests**: All new features should include appropriate test coverage.

3. **Pass All Checks**: Ensure your code passes all existing tests and any new tests you've added.

4. **Follow Coding Standards**: Adhere to the project's coding conventions (see below).

5. **Commit Message Format**: Use clear, descriptive commit messages (see guidelines below).

6. **Update Changelog**: Add a note about your changes in the PR description.

7. **Request Review**: Once your PR is ready, request a review from maintainers.

8. **Address Feedback**: Be responsive to code review comments and make requested changes.

9. **Squash Commits**: Before merging, squash your commits into logical units.

## Coding Standards

### Java Code Style

- Follow standard Java naming conventions
- Use meaningful variable and method names
- Keep methods small and focused (Single Responsibility Principle)
- Add JavaDoc comments for public APIs
- Maximum line length: 120 characters
- Use 4 spaces for indentation (no tabs)

### Spring Boot Conventions

- Use constructor injection over field injection
- Prefer `@RequiredArgsConstructor` from Lombok when appropriate
- Use `@Slf4j` for logging instead of manual logger creation
- Follow REST API best practices (proper HTTP methods, status codes)
- Use DTOs for API request/response objects

### Example Code Style

```java
@RestController
@RequestMapping("/api/v1/quizzes")
@RequiredArgsConstructor
@Slf4j
public class QuizController {

    private final QuizService quizService;

    /**
     * Creates a new quiz based on the provided criteria.
     *
     * @param quizDto DTO containing quiz creation parameters
     * @return ResponseEntity with creation status
     */
    @PostMapping
    public ResponseEntity<QuizResponse> createQuiz(@Valid @RequestBody QuizDto quizDto) {
        log.info("Creating quiz with title: {}", quizDto.getTitle());
        QuizResponse response = quizService.createQuiz(quizDto);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
}
```

### Package Structure

```
com.telusko.<service-name>
  ├── config/          # Configuration classes
  ├── controller/      # REST controllers
  ├── dto/             # Data Transfer Objects
  ├── exception/       # Custom exceptions and handlers
  ├── model/           # Domain entities
  ├── repository/      # Data access layer
  ├── service/         # Business logic
  └── util/            # Utility classes
```

## Testing Guidelines

### Test Coverage

- Aim for at least 80% code coverage
- All public methods should have unit tests
- Critical paths should have integration tests
- Test both success and failure scenarios

### Test Structure

Use the AAA (Arrange-Act-Assert) pattern:

```java
@Test
void shouldCreateQuizSuccessfully() {
    // Arrange
    QuizDto quizDto = QuizDto.builder()
        .title("Java Quiz")
        .categoryName("Java")
        .numQuestions(5)
        .build();
    
    // Act
    ResponseEntity<String> response = quizService.createQuiz(
        quizDto.getCategoryName(), 
        quizDto.getNumQuestions(), 
        quizDto.getTitle()
    );
    
    // Assert
    assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
    assertThat(response.getBody()).isEqualTo("Success");
}
```

### Running Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=QuizServiceTest

# Run tests with coverage
mvn test jacoco:report

# Run integration tests
mvn verify
```

## Commit Message Guidelines

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Code style changes (formatting, missing semicolons, etc.)
- **refactor**: Code refactoring without functionality changes
- **test**: Adding or updating tests
- **chore**: Maintenance tasks (dependency updates, build config, etc.)
- **perf**: Performance improvements

### Examples

```
feat(quiz-service): add pagination support for quiz listing

Implement page-based pagination for GET /quizzes endpoint
to improve performance with large datasets.

Closes #42
```

```
fix(gateway): resolve routing issue for quiz-service endpoints

The gateway was not properly forwarding requests to quiz-service
due to incorrect path configuration.

Fixes #38
```

### Best Practices

- Use the imperative mood ("add feature" not "added feature")
- Keep the subject line under 50 characters
- Capitalize the subject line
- Don't end the subject line with a period
- Separate subject from body with a blank line
- Wrap the body at 72 characters
- Reference issues and pull requests in the footer

## Code Review Process

### For Contributors

- Be open to feedback and constructive criticism
- Respond to review comments in a timely manner
- Ask questions if review feedback is unclear
- Update your PR based on feedback

### For Reviewers

- Be respectful and constructive in feedback
- Provide specific, actionable suggestions
- Approve PRs that meet quality standards
- Request changes when standards aren't met

## Questions?

If you have questions about contributing, feel free to:

- Open an issue with the `question` label
- Reach out to project maintainers
- Check existing documentation

Thank you for contributing! 🎉
