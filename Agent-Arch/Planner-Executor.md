# Planner Executor

## Quick Overview

| Name                 | Definition                              | Example                           |
| -------------------- | --------------------------------------- | --------------------------------- |
| **Planner**          | Creates a step-by-step plan for a task  | Break research into smaller tasks |
| **Executor**         | Performs each step of the plan          | Search, calculate, or call an API |
| **Replanner**        | Updates the plan when something changes | Create new steps after an error   |
| **Plan-and-Execute** | Separates planning from task execution  | Plan → Execute → Replan           |

## 1. ReAct Architecture

**Definition:** ReAct (Reasoning + Acting) is an agent pattern where the agent reasons about a task and then performs an action, repeating this process step by step.

**Example:**

```text
Think → Use a tool → Observe result → Think again → Use another tool
```

**Instructions:**

* ReAct is useful when the next action depends heavily on the previous result.
* The agent usually makes an LLM decision between actions.
* Many repeated LLM calls can increase latency and cost.
* ReAct is not necessarily worse than Plan-and-Execute; the better approach depends on the task.

## 2. Plan-and-Execute Architecture

**Definition:** Plan-and-Execute separates an agent into a **planner** that creates a plan and an **executor** that performs the steps.

**Example:**

```text
User: Find the best way to learn Python.

Planner:
1. Identify Python fundamentals.
2. Find useful learning resources.
3. Create a learning schedule.

Executor:
→ Complete step 1
→ Complete step 2
→ Complete step 3
```

**Instructions:**

* Use a planner for tasks containing multiple steps.
* Let the executor focus on completing individual steps.
* The executor can use tools such as APIs, search, databases, or Python.
* The system can replan when the original plan is no longer suitable.

## 3. Planner

**Definition:** The planner uses an LLM to break a large goal into smaller, ordered steps.

**Example:**

```text
Goal:
Build a simple weather application.

Plan:
1. Get weather API credentials.
2. Create an API request.
3. Parse the weather response.
4. Display the result.
```

**Instructions:**

* Each step should contain enough information to execute it.
* Avoid unnecessary steps.
* The final step should produce the requested result.
* Structured output can be used to make the plan easier for code to process.

## 4. Executor

**Definition:** The executor takes a planned step and performs the required action, often using one or more tools.

**Example:**

```text
Plan:
1. Get the current weather.
2. Convert the temperature to Celsius.

Executor:
→ Calls the weather API.
→ Reads the temperature.
→ Performs the conversion.
```

**Instructions:**

* Execute one planned task at a time when the workflow is designed that way.
* Give the executor enough context about the overall plan.
* Use appropriate tools for each task.
* Store the result so later steps can use it.

## 5. Replanning

**Definition:** Replanning means updating the original plan when execution produces unexpected results or when the remaining steps need to change.

**Example:**

```text
Original plan:
1. Call API.
2. Process response.
3. Generate report.

Problem:
→ API request fails.

Updated plan:
1. Check API configuration.
2. Retry the request.
3. Process response.
4. Generate report.
```

**Instructions:**

* Replanning should consider completed steps and their results.
* Do not repeat steps that have already been completed successfully.
* Add only the steps that are still required.
* The agent can return the final answer when no more steps are needed.

## 6. LangGraph Workflow

**Definition:** LangGraph can represent the Plan-and-Execute process as a graph of connected nodes.

**Example:**

```text
START
  ↓
Planner
  ↓
Executor
  ↓
Replanner
  ↓
 ┌───────────────┐
 │               │
More work?    Finished?
 │               │
 ↓               ↓
Executor         END
```

**Instructions:**

* Each major operation can be represented as a graph node.
* Edges define the order in which nodes run.
* Conditional edges can decide whether to continue or finish.
* LangGraph helps manage state and multi-step agent workflows.

## 7. Planner Prompt

**Definition:** A planner prompt tells the LLM to create a clear and minimal sequence of steps for achieving a goal.

**Example:**

```python
planner_prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            """Create a simple step-by-step plan for the given objective.
Do not add unnecessary steps.
Make sure every step contains enough information to complete it."""
        ),
        ("placeholder", "{messages}"),
    ]
)
```

**Instructions:**

* Clearly describe what the planner should produce.
* Tell it to avoid unnecessary steps.
* Keep the plan focused on the user's objective.
* Structured output can make the plan easier to use in code.

## 8. Executing a Plan

**Definition:** Plan execution means selecting a step from the plan and giving it to an agent or tool-enabled system to complete.

**Example:**

```python
plan = state["plan"]
task = plan[0]

agent_response = await agent_executor.ainvoke(
    {
        "messages": [
            ("user", f"Execute this task: {task}")
        ]
    }
)
```

**Instructions:**

* Select the task that needs to be executed.
* Provide the necessary context to the executor.
* Save the result after execution.
* Use the saved result when replanning or completing later steps.

## 9. State and Past Steps

**Definition:** State stores information that the workflow needs while moving between planner, executor, and replanner nodes.

**Example:**

```text
State:

input:
"Research Python frameworks"

plan:
["Find frameworks", "Compare frameworks"]

past_steps:
[
  ("Find frameworks", "FastAPI, Django, Flask")
]
```

**Instructions:**

* Store the original user request.
* Store the current plan.
* Store completed steps and their results.
* Keep enough state for the replanner to make a useful decision.

## 10. Conditional Workflow

**Definition:** A conditional workflow chooses the next node based on the current state.

**Example:**

```python
def should_end(state):
    if "response" in state and state["response"]:
        return END
    return "agent"
```

**Instructions:**

* Use conditions when the workflow can follow different paths.
* End the workflow when the final response is ready.
* Continue execution when more work is required.
* Keep routing logic simple and predictable.

## 11. Complete Plan-and-Execute Flow

**Definition:** The complete architecture connects planning, execution, and replanning into one workflow.

**Example:**

```text
User Request
     ↓
   Planner
     ↓
    Plan
     ↓
  Executor
     ↓
   Result
     ↓
 Replanner
     ↓
 ┌───────────────┐
 │               │
More steps?    Finished?
 │               │
 ↓               ↓
Executor         END
```

**Instructions:**

* Start by creating a plan from the user's goal.
* Execute the required steps and record their results.
* Replan when the situation changes.
* Finish when the system has enough information to produce the final answer.

## Quick Memory

```text
ReAct → Think and act step by step
Planner → Creates the plan
Executor → Executes the plan
Replanner → Updates the plan
State → Stores workflow information
LangGraph → Connects everything into a workflow

Plan-and-Execute → Plan → Execute → Replan → Finish
```
