# Self-Critique Agents

## Quick Overview

| Concept                 | Definition                                                                                             | Example                                       |
| ----------------------- | ------------------------------------------------------------------------------------------------------ | --------------------------------------------- |
| **Self-Critique Agent** | An AI agent that reviews its own output to find mistakes and improve it.                               | Generate an answer → critique it → improve it |
| **Reflection**          | The process of analyzing previous actions or outputs and using the result to improve future decisions. | Checking why an answer was incorrect          |
| **Critique**            | Identifying errors, missing information, or weak parts of an output.                                   | Finding incorrect facts in a response         |
| **Refinement**          | Improving an output based on the critique.                                                             | Rewriting an answer after finding mistakes    |
| **Feedback Loop**       | Repeating generation, evaluation, and improvement until a stopping condition is reached.               | Generate → Critique → Improve → Repeat        |

## 1. What Are Self-Critique Agents?

**Definition:**
Self-critique agents are AI agents that evaluate their own actions or outputs and try to improve them.

Instead of generating an answer once and stopping, the agent follows a loop:

```text
Generate
   ↓
Critique
   ↓
Improve
   ↓
Generate Again
```

This allows the agent to detect weaknesses and refine its results.

---

## 2. Reflection

**Definition:**
Reflection is the process where an AI agent analyzes its previous actions, decisions, or outputs to identify what went wrong and what could be improved.

For example:

```text
Agent generates an answer
        ↓
Agent reviews the answer
        ↓
Agent finds a mistake
        ↓
Agent explains how to fix it
        ↓
Agent generates an improved answer
```

Reflection can help an agent move beyond simply reacting to a task and instead evaluate its previous work.

---

## 3. Self-Critique Loop

A basic self-critique system can use two LLM calls:

```text
User Request
     ↓
Generator
     ↓
Initial Output
     ↓
Critic
     ↓
Feedback
     ↓
Generator
     ↓
Improved Output
```

The **Generator** creates the initial response.

The **Critic** reviews that response and provides feedback.

The **Generator** then uses the feedback to create a better response.

This process can be repeated a fixed number of times or until a stopping condition is reached.

---

## 4. Generator

**Definition:**
The generator is the part of the agent that creates the initial output.

**Example:**

```text
User: Write a Python function to calculate factorial.

Generator:
def factorial(n):
    return n * factorial(n - 1)
```

The first output may contain mistakes or missing cases.

---

## 5. Critic

**Definition:**
The critic evaluates the generated output and identifies problems.

It can look for:

* Incorrect logic
* Missing information
* Poor structure
* Invalid assumptions
* Errors in code
* Unclear explanations

**Example:**

```text
Critic:
The function does not handle the base case.
For n = 0, the recursion will never stop.
```

---

## 6. Refinement

**Definition:**
Refinement means improving the original output using the critic's feedback.

**Example:**

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

The agent uses the identified problem to produce a corrected version.

---

## 7. Feedback Loop

The complete process can be represented as:

```text
Generate
   ↓
Evaluate
   ↓
Critique
   ↓
Refine
   ↓
Evaluate Again
   ↓
Final Output
```

A stopping condition can be used to prevent the agent from continuing forever.

For example:

```text
if output_is_good:
    stop
else:
    improve_again
```

Another simple approach is to stop after a fixed number of iterations.

---

## 8. External Feedback

Self-critique becomes more useful when the agent can use external feedback.

Examples include:

* Tool results
* Error messages
* Test results
* Search results
* Environment feedback
* Human feedback

For example, a coding agent can write code, run tests, inspect the errors, and then revise the code.

```text
Write Code
    ↓
Run Tests
    ↓
Test Failure
    ↓
Analyze Error
    ↓
Fix Code
    ↓
Run Tests Again
```

Reflexion is an example of an architecture that uses verbal feedback and external information to guide improvement.

---

## 9. Basic Reflection vs Reflexion

| Approach             | How It Works                                                                |
| -------------------- | --------------------------------------------------------------------------- |
| **Basic Reflection** | Generate an output and ask another LLM call to critique it.                 |
| **Reflexion**        | Uses feedback and verbal self-reflection to guide future attempts.          |
| **ReAct**            | Combines reasoning and actions in an iterative loop.                        |
| **LATS**             | Uses reflection, evaluation, and search to explore multiple possible paths. |

Basic reflection may improve an answer through multiple attempts, but it can be limited if the critic has no reliable external information to verify the output.

---

## 10. Self-Critique Example

Imagine an AI agent asked:

```text
Calculate the result of a complex mathematical problem.
```

The agent could work like this:

```text
Step 1 → Generate solution
Step 2 → Check the solution
Step 3 → Find an error
Step 4 → Explain the error
Step 5 → Correct the solution
Step 6 → Check again
Step 7 → Return final answer
```

This is more reliable than simply generating one answer and immediately returning it.

---

## 11. LATS: Reflection + Search

**LATS (Language Agent Tree Search)** combines reflection and evaluation with search.

Instead of following only one path, the agent can explore multiple possible actions.

```text
                 Start
                   |
          ┌────────┼────────┐
          ↓        ↓        ↓
        Path A   Path B   Path C
          ↓        ↓        ↓
       Evaluate Evaluate Evaluate
          ↓        ↓        ↓
          └────────┼────────┘
                   ↓
             Select Path
```

The basic process includes:

1. **Select** a promising path.
2. **Expand** by generating possible actions.
3. **Reflect and evaluate** the results.
4. **Update** the scores of the paths.
5. Continue searching or return a solution.

This approach is useful for complex problems where a single reasoning path may get stuck.

---

## 12. Benefits

Self-critique can help agents:

* Detect mistakes
* Improve output quality
* Correct incorrect reasoning
* Reduce repeated errors
* Handle complex tasks
* Use feedback from previous attempts
* Produce more reliable results

However, reflection requires additional LLM calls, so it can increase latency and computational cost.

---

## 13. Limitations

Self-critique is not automatically correct.

An AI can sometimes:

* Miss its own mistakes
* Produce incorrect criticism
* Repeat the same error
* Waste computation on unnecessary reflection
* Improve the wording without improving correctness

For important tasks, external validation such as tests, tools, reliable data, or human review can make the feedback loop more useful.

---

## Quick Memory

```text
Self-Critique Agent → AI that checks and improves its own output

Reflection → Think about what happened and what can improve

Critique → Find mistakes or weaknesses

Refinement → Fix the identified problems

Feedback Loop → Generate → Critique → Improve → Repeat

Reflexion → Learn from feedback using verbal self-reflection

ReAct → Reason → Act → Observe → Repeat

LATS → Explore multiple paths → Evaluate → Select
```
