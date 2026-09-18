# Unit Testing for Individual Agent Tools

## Quick Overview

| Name | Definition | Example |
| ---- | ---------- | ------- |
| Unit Test | Tests one small component in isolation. | Test a calculator function |
| Mock | Replaces a dependency with a controlled fake object. | Mock an API client |
| Stub | Provides predefined responses from a dependency. | Return a fixed database result |
| Fake | A lightweight working implementation used for testing. | In-memory database |
| Spy | Records how a dependency was called. | Check API arguments |
| Fixture | Reusable test data or setup. | Create a test user |
| Parameterized Test | Runs the same test with multiple inputs. | Test 10 date formats |
| Tool Contract | Defines valid tool inputs and outputs. | JSON schema for a search tool |
| Test Isolation | Keeps tests independent from each other. | Each test gets fresh data |
| Determinism | Same input produces a predictable test result. | Mock current time |
| Code Coverage | Measures which code was executed by tests. | 85% line coverage |
| CI | Automatically runs tests when code changes. | Run tests on every pull request |

## Comparison Table

| Technique | Purpose | Example in an AI Agent |
| --------- | ------- | ---------------------- |
| Unit Test | Verify one function or tool | Test `calculate_total()` |
| Mock | Replace an external dependency | Mock a payment API |
| Stub | Return controlled data | Stub a weather API response |
| Fake | Use a simple working replacement | In-memory database |
| Spy | Verify calls and arguments | Check search tool was called once |
| Fixture | Reuse setup and test data | Create a standard test document |
| Parameterized Test | Test many inputs efficiently | Test valid and invalid tool arguments |
| Contract Test | Verify input/output structure | Validate tool schema |
| Integration Test | Test multiple real components together | Agent + tool + database |
| CI Test | Automatically run tests | Run pytest in GitHub Actions |

## 1. Unit Testing

**Definition:** Unit testing checks one small piece of code independently from the rest of the system.

**Example:**

```python
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
```

**Instructions:**

* Test one function, method, or small component at a time.
* Test normal, edge, and expected error cases.
* Keep tests fast and independent.
* Run tests whenever the code changes.

## 2. Testing an AI Agent Tool

**Definition:** An agent tool is a function, API, database operation, or service that an AI agent can call to perform an action.

**Example:**

```python
def calculator(expression):
    return eval(expression)  # Simplified example; avoid eval in real applications.

def test_calculator():
    assert calculator("2 + 3") == 5
```

**Instructions:**

* Test the tool directly without requiring the full agent.
* Verify valid inputs, invalid inputs, and boundary cases.
* Test the exact output format expected by the agent.
* For external services, mock the network call instead of depending on the live service.

## 3. Tool Schema Validation

**Definition:** Schema validation checks that tool arguments and results have the expected structure and data types.

**Example:**

```python
tool_input = {
    "query": "Python testing",
    "limit": 5
}

assert isinstance(tool_input["query"], str)
assert isinstance(tool_input["limit"], int)
```

**Instructions:**

* Define required fields and their types.
* Reject missing, invalid, or unexpected values when appropriate.
* Validate tool output before passing it to later agent steps.
* JSON Schema or typed models such as Pydantic can be used for structured validation.

## 4. Mocking

**Definition:** Mocking replaces a real dependency with a controlled object during a test.

**Example:**

```python
from unittest.mock import Mock

search_api = Mock()
search_api.search.return_value = ["Result 1"]

result = search_api.search("Python")

assert result == ["Result 1"]
search_api.search.assert_called_once_with("Python")
```

**Instructions:**

* Mock APIs, databases, network calls, and other external dependencies when needed.
* Control the response so the test is predictable.
* Verify that the dependency was called correctly.
* Do not mock everything; test important integration behavior separately.

## 5. Stubs, Fakes, and Spies

**Definition:** Stubs return predefined data, fakes provide a lightweight working implementation, and spies record how a dependency is used.

**Example:**

```text
Stub → "Always return this test response."
Fake → "Use this simple in-memory database."
Spy  → "Record that the search tool was called with X."
```

**Instructions:**

* Use a stub when you only need predictable return values.
* Use a fake when a lightweight real implementation is easier than a mock.
* Use a spy when you need to verify calls, arguments, or call counts.
* Choose the simplest test double that proves the behavior you need.

## 6. Fixtures and Setup/Teardown

**Definition:** Fixtures provide reusable test data or setup, while teardown cleans up resources after a test.

**Example:**

```python
import pytest

@pytest.fixture
def user():
    return {"name": "Bob", "role": "developer"}

def test_user_role(user):
    assert user["role"] == "developer"
```

**Instructions:**

* Use fixtures for common test data and setup.
* Keep fixtures small and easy to understand.
* Clean up temporary files, database records, and other resources.
* Avoid shared mutable state that can make tests depend on execution order.

## 7. Parameterized Tests

**Definition:** Parameterized testing runs the same test logic against multiple inputs.

**Example:**

```python
import pytest

@pytest.mark.parametrize(
    "a,b,result",
    [(2, 3, 5), (0, 5, 5), (-1, 1, 0)]
)
def test_add(a, b, result):
    assert a + b == result
```

**Instructions:**

* Use it when the same behavior needs many test cases.
* Include valid, boundary, and invalid inputs.
* Give each test case meaningful data.
* It reduces repetitive test code.

## 8. Error Handling, Retries, and Timeouts

**Definition:** Tool tests should verify how the tool behaves when dependencies fail, take too long, or temporarily reject requests.

**Example:**

```python
def test_search_timeout():
    try:
        search()
    except TimeoutError:
        assert True
```

**Instructions:**

* Test timeouts and expected exceptions explicitly.
* Test retry behavior without waiting for real delays.
* Test rate-limit responses such as HTTP `429` when relevant.
* Make sure retry logic has limits and does not create endless loops.

## 9. Side Effects and Idempotency

**Definition:** A side effect changes something outside the function, while an idempotent operation can be repeated without creating an unintended additional effect.

**Example:**

```text
Create User
1st call → User created
2nd call → Should not accidentally create a duplicate
```

**Instructions:**

* Test database writes, emails, payments, file changes, and other side effects carefully.
* Use mocks or test environments when real actions are unsafe.
* Use idempotency keys where an operation may be retried.
* Verify that retries do not duplicate important actions.

## 10. Testing Agent Tool Selection

**Definition:** Tool-selection tests check whether an agent chooses the correct tool and provides the correct arguments for a task.

**Example:**

```text
User:
"Convert 10 USD to INR."

Expected:
Tool → currency_converter
Arguments → {"amount": 10, "from": "USD", "to": "INR"}
```

**Instructions:**

* Test whether the correct tool is selected.
* Validate the generated tool arguments.
* Test similar requests that should use different tools.
* Test cases where no tool should be called.

## 11. Testing LLM Function Calling

**Definition:** Function-calling tests verify that an LLM produces the expected tool name and structured arguments.

**Example:**

```python
expected = {
    "name": "search",
    "arguments": {
        "query": "Python testing"
    }
}

assert tool_call["name"] == expected["name"]
assert tool_call["arguments"] == expected["arguments"]
```

**Instructions:**

* Test the tool name and argument structure separately from the real tool execution.
* Include ambiguous and invalid user requests.
* Use deterministic settings or mocked model responses when exact outputs must be tested.
* Do not assume an LLM will always produce identical text; test structured behavior where possible.

## 12. Permissions and Security Boundaries

**Definition:** Security tests verify that an agent tool can perform only the actions and access only the data it is authorized to use.

**Example:**

```text
User A requests:
"Delete User B's account."

Expected:
→ Permission denied
→ No database change
```

**Instructions:**

* Test authorized and unauthorized actions.
* Check access to sensitive data and privileged operations.
* Never rely on the LLM alone to enforce permissions.
* Enforce authorization in the application or tool layer.

## 13. Test Isolation and Determinism

**Definition:** Test isolation means tests do not depend on other tests, while determinism means the same test conditions produce predictable results.

**Example:**

```text
Bad:
Test B depends on data created by Test A.

Good:
Test B creates its own required test data.
```

**Instructions:**

* Give each test its own data and state when practical.
* Mock unstable dependencies such as current time, random values, and external APIs.
* Avoid tests that depend on execution order.
* Flaky tests should be investigated rather than simply ignored.

## 14. Code Coverage

**Definition:** Code coverage measures how much of the code is executed while tests run.

**Example:**

```text
100 lines of code
80 lines executed by tests

Line coverage = 80%
```

**Instructions:**

* Coverage can help find untested code.
* High coverage does not automatically mean high-quality tests.
* Focus on meaningful behavior, edge cases, and important failure paths.
* Use coverage together with test results and code review.

## 15. Continuous Integration (CI)

**Definition:** CI automatically runs tests when developers push code or create pull requests.

**Example:**

```text
Developer Push
      ↓
CI Pipeline
      ↓
Install Dependencies
      ↓
Run Tests
      ↓
Pass → Merge
Fail → Fix
```

**Instructions:**

* Run unit tests automatically for every relevant code change.
* Include linting, type checking, and security checks when appropriate.
* Keep fast unit tests early in the pipeline.
* Do not merge code when required tests fail.

## 16. AI-Assisted Testing Tools

**Definition:** AI-assisted testing tools use AI to help generate, explain, review, or maintain tests. They are different from testing the individual tools used by an AI agent.

**Example:**

```text
Developer Code
      ↓
AI Coding/Test Assistant
      ↓
Suggested Unit Tests
      ↓
Developer Review
      ↓
Run Tests
```

**Instructions:**

* Examples include GitHub Copilot, Diffblue Cover, Amazon Q Developer, and Symflower.
* Use AI to speed up test creation, but review generated tests.
* Generated tests can contain incorrect assumptions or weak assertions.
* Keep the main focus on whether the software behavior is actually correct.

## Quick Memory

```text
Unit Test       → Test one small component
Mock            → Replace a dependency
Stub            → Return fixed data
Fake            → Lightweight working replacement
Spy             → Record calls
Fixture         → Reusable test setup/data
Parameterized   → Same test, many inputs
Schema          → Validate tool input/output structure
Error Test      → Check failures, retries, and timeouts
Side Effect     → Check external changes
Idempotency     → Repeating an action should be safe when required
Tool Selection  → Did the agent choose the right tool?
Function Call   → Did it produce the right tool + arguments?
Security        → Can the tool do only what it is allowed to do?
Isolation       → Tests do not depend on each other
Determinism     → Same conditions → predictable result
Coverage        → What code did the tests execute?
CI              → Run tests automatically on code changes
AI Testing Tool → AI helps create or maintain tests
```
