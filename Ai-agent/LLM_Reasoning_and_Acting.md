# Revision Notes: LLM Reasoning & Acting --- Updated 2026

## 1. ReAct (Reason + Act)

**Definition:** ReAct is a prompting/agentic framework that combines
reasoning-style intermediate steps with actions such as search, API
calls, calculations, or other tool use. The model uses observations from
those actions to decide what to do next.

**Origin:** Introduced by **Yao et al. (2022)**. ReAct combines
reasoning traces with actions that interact with an external
environment. It is related to earlier modular reasoning/tool-use ideas,
but it is more precise to describe ReAct as its own framework rather
than simply as an extension of MRKL.

### Core Loop

``` text
Thought / Reasoning
        ↓
      Action
        ↓
   Observation
        ↓
   Next reasoning step
        ↓
      Action
        ↓
      ...
        ↓
   Final Answer
```

-   **Thought / Reasoning** → determine what information or action is
    needed next.
-   **Action** → perform an operation such as searching, calling a tool,
    querying an API, or calculating.
-   **Observation** → receive the result from the environment/tool.
-   **Repeat** → use the observation to decide the next step.
-   **Final answer** → stop when enough information has been gathered.

### Important clarification

**ReAct is not itself a reinforcement-learning algorithm.**

The loop can be compared conceptually with an agent's state → action →
observation cycle, but a tool observation is **not automatically a
reward**.

### Original ReAct evaluation

The ReAct work evaluated the approach on tasks including:

-   **HotpotQA** --- multi-hop question answering
-   **FEVER** --- fact verification

The paper demonstrated benefits from combining reasoning and acting,
including the ability to interact with external information during
problem solving.

**Key source:** Yao et al., 2022.

------------------------------------------------------------------------

## 2. ReAct vs Chain-of-Thought

 | Aspect | Chain-of-Thought | ReAct |
|---|---|---|
| **Main idea** | Reason through a problem step by step | Reason + interact with tools/environment |
| **External actions** | Usually no | Yes |
| **Tool use** | Not required | Central concept |
| **Observations** | Usually no external observations | Yes |
| **Good for** | Multi-step reasoning | Research, tool use, multi-step tasks |

### Simple memory trick

> **CoT = Think**\
> **ReAct = Think → Act → Observe → Think**

------------------------------------------------------------------------

## 3. Reasoning Models vs General/Standard Models

A **reasoning model** is designed or trained to spend additional
computation on difficult reasoning tasks such as mathematics, coding,
planning, and multi-step problem solving.

A **general-purpose/standard model** can also reason, but it may be
optimized more broadly for fast, general-purpose interaction rather than
explicitly allocating substantial additional inference computation to
difficult reasoning.

### Comparison

 | Aspect | Reasoning Models | General/Standard Models |
|---|---|---|
| **Process** | Often allocate more computation to difficult reasoning | Usually optimized for broad, efficient generation |
| **Strengths** | Complex math, coding, planning, multi-step tasks | Chat, summarization, extraction, general generation |
| **Latency** | Can be higher depending on reasoning effort | Often lower for comparable tasks |
| **Compute** | Can use more inference-time compute | Often uses less inference-time compute |
| **Correctness** | Can improve difficult-task performance, but not guaranteed | Can perform many reasoning tasks well |
| **Best use** | Complex tasks where additional reasoning is valuable | Fast/general tasks and simpler workflows |

### Important

**Reasoning ≠ guaranteed correctness.**

A reasoning model can still:

-   make factual mistakes;
-   make logical mistakes;
-   use incorrect assumptions;
-   produce an incorrect final answer.

More reasoning generally means **more opportunity to solve difficult
problems**, not guaranteed accuracy.

------------------------------------------------------------------------

## 4. Inference-Time Compute

One important modern concept is **inference-time compute**.

Instead of relying only on the model's fixed computation pattern, a
reasoning system can spend additional computation/tokens on a difficult
problem.

``` text
Easy task
   ↓
Less reasoning effort
   ↓
Fast answer

Hard task
   ↓
More reasoning effort
   ↓
More computation
   ↓
Potentially better answer
```

The amount of reasoning effort can be controlled or influenced by the
model/system's reasoning configuration.

### Key idea

> **Training gives the model capabilities; inference-time compute can
> give it more opportunity to use those capabilities on difficult
> problems.**

------------------------------------------------------------------------

## 5. Reasoning Traces vs Actual Internal Reasoning

A model may produce visible intermediate reasoning-like text, but that
text should **not automatically be treated as a faithful record of the
model's internal computation**.

Therefore:

❌ "The visible chain of thought shows exactly how the model internally
reasoned."

Better:

✅ "Intermediate outputs can help inspect a solution process, but they
are not guaranteed to faithfully represent the model's internal
computation."

For practical systems, it is often safer to inspect:

-   tool calls
-   retrieved evidence
-   intermediate structured outputs
-   calculations
-   verification results
-   final answer

rather than assuming a generated reasoning trace is a perfect
explanation.

------------------------------------------------------------------------

## 6. ReAct in Modern AI Agents

Modern agents often combine several capabilities:

``` text
User Goal
    ↓
Planning / Reasoning
    ↓
Choose Tool
    ↓
Tool Execution
    ↓
Observation / Result
    ↓
Evaluate Result
    ↓
Continue / Change Plan
    ↓
Final Response
```

An agent may use:

-   Web search
-   APIs
-   Databases
-   Code execution
-   File search
-   Browsing
-   External applications

### Important distinction

**Tool calling** is the capability of invoking a tool.

**ReAct** is a broader reasoning-and-action loop in which observations
from actions influence subsequent decisions.

A modern agent does not have to use the exact original ReAct prompting
format to implement a similar iterative reasoning/action pattern.

------------------------------------------------------------------------

## 7. Verification and Reflection

Modern agentic systems may include additional steps after an action:

### Verification

Check whether the retrieved result or generated answer is correct.

``` text
Generate
   ↓
Verify
   ↓
Correct if needed
   ↓
Final
```

### Reflection

The system evaluates its previous attempt and decides whether another
attempt is necessary.

This can improve reliability in some workflows, but **reflection is not
a guarantee of correctness** and can add latency and cost.

------------------------------------------------------------------------

## 8. ReAct vs Tool Calling vs Agents

  | Concept | Meaning |
|---|---|
| **Tool Calling** | Model requests execution of an external tool |
| **ReAct** | Iterative reasoning + action + observation framework |
| **Agent** | System that can pursue a goal through planning, tool use, state, and iterative decisions |
| **RAG** | Retrieves external information to ground generation |
| **Chain-of-Thought** | Step-by-step reasoning approach without requiring external tool actions |

These concepts can overlap.

For example:

``` text
Agent
 ├── Reasoning
 ├── Tool Calling
 ├── RAG
 ├── Verification
 └── Iterative Actions
```

------------------------------------------------------------------------

## 9. When to Use What?

### Use a general/standard model when:

-   The task is straightforward.
-   Low latency matters.
-   You need normal chat or summarization.
-   Extensive reasoning is unnecessary.

### Use a reasoning model when:

-   The problem requires multiple logical steps.
-   Mathematics or difficult coding is involved.
-   Planning or complex decision-making is required.
-   Additional inference-time computation is worthwhile.

### Use ReAct/agentic patterns when:

-   The model needs external tools.
-   Information must be gathered iteratively.
-   The next action depends on previous observations.
-   The task requires multiple steps.

### Use RAG when:

-   The answer needs external/private/current information.
-   Documents need to be retrieved and supplied to the model.
-   Knowledge changes more frequently than model retraining is
    practical.

------------------------------------------------------------------------

## 10. Common Interview Traps

### Trap 1

**"ReAct is reinforcement learning."**

❌ Not exactly.

ReAct is a reasoning-and-action framework. Its loop can be compared
conceptually with agent/RL interaction, but ReAct itself is not an RL
algorithm.

------------------------------------------------------------------------

### Trap 2

**"Reasoning models always give correct answers."**

❌ False.

Reasoning improves the model's ability on many difficult tasks but does
not guarantee correctness.

------------------------------------------------------------------------

### Trap 3

**"Standard models cannot reason."**

❌ False.

General-purpose language models can perform reasoning. The difference is
in how they are trained/optimized and how much computation they allocate
to difficult reasoning.

------------------------------------------------------------------------

### Trap 4

**"More reasoning always means better results."**

❌ False.

Additional computation can help, but it can also increase latency/cost
and does not eliminate incorrect assumptions or errors.

------------------------------------------------------------------------

### Trap 5

**"Visible chain-of-thought is a perfect explanation of internal
reasoning."**

❌ False.

Generated reasoning-like text should not automatically be interpreted as
a faithful description of internal computation.

------------------------------------------------------------------------

### Trap 6

**"ReAct and tool calling are the same thing."**

❌ False.

Tool calling is an execution capability. ReAct describes an iterative
reasoning/action/observation pattern.

------------------------------------------------------------------------

## 11. Quick Recall Summary

-   **ReAct** = Reason → Act → Observe → Repeat.
-   ReAct was introduced by **Yao et al. (2022)**.
-   ReAct combines reasoning-style steps with external actions/tool use.
-   **ReAct ≠ reinforcement learning**, although the interaction loop
    can be compared conceptually with agent/RL interaction.
-   **Chain-of-Thought** focuses on reasoning; **ReAct** adds
    interaction with an external environment.
-   **Reasoning models** can allocate more inference-time computation to
    difficult tasks.
-   **General models can also reason**; they are not simply
    "pattern-matching machines."
-   **Reasoning does not guarantee correctness.**
-   Visible reasoning traces are **not guaranteed to faithfully
    represent internal computation**.
-   **Tool calling** = invoke a tool.
-   **ReAct** = iterative reasoning + action + observation.
-   **Agent** = goal-oriented system that can plan, use tools, maintain
    state, and iterate.
-   Modern agents can combine **reasoning, tools, RAG, verification, and
    reflection**.

## 12. One-Line Memory Model

> **Prompting tells the model what to do → Reasoning helps it solve
> difficult problems → Tool calling lets it act → ReAct connects
> reasoning with actions and observations → Agents repeat this process
> to achieve a goal.**
