# Prompt Engineering, Prompt Tuning & Fine-Tuning 

> **Purpose:** Exam/interview-ready notes covering prompt engineering,
> prompt tuning, PEFT, LoRA/QLoRA, supervised fine-tuning, preference
> optimization, and RAG.

---

## 1. The Big Picture

There are several ways to adapt an LLM for a task:

| Method | What changes? | Base model weights? | Typical effort |
|---|---|---|---|
| **Prompt Engineering** | Human-written input/context | No | Very low |
| **In-Context Learning (ICL)** | Examples/instructions placed in the context | No | Low |
| **Prompt Tuning** | Learned soft-prompt parameters | Usually frozen | Low compute compared with full fine-tuning |
| **PEFT / LoRA** | Small trainable adapter parameters | Usually frozen | Medium |
| **Full Fine-Tuning** | Model parameters | Yes | High |
| **RAG** | External information retrieved at inference time | No | Low–Medium |

### Key distinction

-   **Prompt Engineering:** changes the text you send to the model.
-   **Prompt Tuning:** learns continuous/soft prompt parameters while
    keeping the pretrained model frozen.
-   **LoRA/PEFT:** learns a small set of trainable parameters/adapters
    while normally keeping the base model frozen.
-   **Full Fine-Tuning:** updates the model's parameters.
-   **RAG:** adds retrieved external knowledge to the model's context;
    it does not itself retrain the model.

---

# 2. Prompt Engineering

## Definition

**Prompt engineering** is the manual design and refinement of
instructions, context, examples, constraints, and output formats to
guide an LLM toward a desired result.

It does **not** update model weights.

## Common workflow

1.  Define the task.
2.  Identify the required context and constraints.
3.  Write clear instructions.
4.  Specify the desired output format.
5.  Add examples when useful.
6.  Test the prompt.
7.  Evaluate the output.
8.  Iterate and refine.

## Useful techniques

### Zero-shot prompting

Ask the model to perform a task without examples.

> "Classify this review as positive, neutral, or negative."

### Few-shot prompting

Provide examples of the desired behavior.

> Input: "The product arrived early."\
> Output: Positive
>
> Input: "The package was damaged."\
> Output: Negative

### Role/context prompting

Give useful context or a role.

> "You are reviewing a Python API for security issues. Identify
> authentication and authorization weaknesses."

### Structured output

Specify exactly how the response should be formatted.

> "Return JSON with the keys: `issue`, `severity`, `recommendation`."

### Delimiters

Separate instructions, data, and examples clearly.

> `<instructions>...</instructions>`\
> `<input>...</input>`

## Pros

-   Fast to change
-   Very flexible
-   No training required
-   Usually the cheapest first approach

## Cons

-   Results can be sensitive to wording/context
-   Complex tasks may require iteration
-   Prompt behavior can vary across models

---

# 3. In-Context Learning (ICL)

**In-context learning** means giving the model instructions and/or
examples inside the context window so it can perform a task without
changing its parameters.

It is closely related to prompting but is useful to remember as a
separate concept.

### Example

``` text
Classify the following:

Example 1:
"Great service." → Positive

Example 2:
"Terrible delivery." → Negative

Now classify:
"The product is okay." →
```

### Important

ICL does **not** mean the model permanently learns the examples.

The examples affect the current context, not the model's stored
parameters.

---

# 4. Prompt Tuning

## Definition

**Prompt tuning** is a parameter-efficient adaptation method in which a
small set of **learnable soft prompt embeddings** is optimized while the
pretrained model's parameters remain frozen.

Unlike a normal prompt, a soft prompt is not necessarily readable
natural-language text. It is a learned tensor/embedding associated with
virtual tokens.

## How it works

1.  Start with a pretrained model.
2.  Freeze the pretrained model parameters.
3.  Add trainable virtual/soft prompt embeddings.
4.  Train those prompt parameters on a task dataset.
5.  Save the learned prompt parameters.
6.  Use the frozen model plus the learned prompt at inference.

### Simple picture

``` text
User input
    ↓
[Learned Soft Prompt] + [Input]
    ↓
Frozen Pretrained Model
    ↓
Output
```

## Important correction

Prompt tuning **does involve training**, but it normally does **not
update the pretrained model weights**. The learned prompt parameters are
the part being optimized.

## Prompt tuning vs prompt engineering

| Feature | Prompt Engineering | Prompt Tuning |
|---|---|---|
| Prompt Type | Human-written text | Learned vectors |
| Training Required | No | Yes |
| Base Model | Frozen | Frozen |
| Human-Readable | Yes | Usually no |
| Main Optimization | Human iteration | Gradient-based training |

## Related soft-prompt methods

-   **Prompt Tuning** --- trains prompt embeddings at the input.
-   **Prefix Tuning** --- learns task-specific prefix parameters
    injected through model layers.
-   **P-Tuning** --- uses trainable prompt representations/encoders.
-   Other PEFT prompt-based approaches also exist.

---

# 5. Parameter-Efficient Fine-Tuning (PEFT)

## Definition

**PEFT** adapts a pretrained model by training only a small number of
additional or selected parameters instead of updating the entire model.

This can significantly reduce:

-   trainable parameters
-   GPU memory requirements
-   optimizer state
-   storage required for each task-specific adaptation

The pretrained base model is commonly kept frozen.

## Why PEFT matters

Suppose a model has billions of parameters.

Full fine-tuning may require updating all of them.

With PEFT, you can keep the base model fixed and train a much smaller
adapter.

``` text
                 ┌── Frozen Base Model
Input → Model ───┤
                 └── Small Trainable Adapter
                          ↓
                       Output
```

## Examples of PEFT

-   LoRA
-   QLoRA-style training
-   Prefix Tuning
-   Prompt Tuning
-   P-Tuning
-   Other adapter-based methods

---

# 6. LoRA --- Low-Rank Adaptation

## Definition

**LoRA** is a popular PEFT method that represents the required weight
update using small low-rank matrices instead of directly updating the
original weight matrix.

The original pretrained weights are normally frozen.

Conceptually:

``` text
Original weight W
      +
Low-rank update ΔW
      ↓
Adapted behavior
```

The update is represented approximately as:

``` text
ΔW = B × A
```

where `A` and `B` are much smaller matrices.

## Typical LoRA workflow

1.  Load a pretrained model.
2.  Freeze the original model weights.
3.  Add LoRA adapter matrices.
4.  Train the adapter parameters.
5.  Save the adapter.
6.  Load the adapter with the base model for inference.

## Important LoRA parameters

-   `r` --- rank of the low-rank matrices
-   `lora_alpha` --- scaling factor
-   `lora_dropout` --- dropout applied to LoRA layers
-   `target_modules` --- model layers/modules receiving LoRA adapters

### Trade-off

Higher rank generally means:

-   more trainable parameters
-   potentially more adaptation capacity
-   potentially higher memory/storage cost

---

# 7. QLoRA

## Definition

**QLoRA** combines quantization with LoRA-style parameter-efficient
fine-tuning.

A common setup is:

1.  Load the base model in low-bit precision, often 4-bit.
2.  Keep the quantized base weights frozen.
3.  Train LoRA adapters.
4.  Store only the lightweight trainable adaptation parameters.

### Why use it?

QLoRA can substantially reduce memory requirements and make adaptation
of larger models more accessible on limited hardware.

### Remember

**LoRA ≠ QLoRA**

-   **LoRA:** low-rank adapters.
-   **QLoRA:** LoRA-style adaptation combined with quantized base-model
    weights.

---

# 8. Fine-Tuning

## Definition

**Fine-tuning** means adapting a pretrained model using additional
training data.

Fine-tuning is a broad category. It does **not always mean updating
every parameter**.

Two important categories are:

### Full Fine-Tuning

Most/all model parameters are updated.

**Pros** - Maximum adaptation capacity - Useful when substantial
behavior/domain adaptation is required

**Cons** - Expensive - High memory/compute requirements - Larger
checkpoints - Can cause overfitting or loss of previously learned
capabilities

### Parameter-Efficient Fine-Tuning

Only a smaller set of parameters is trained.

Examples include:

-   LoRA
-   QLoRA-style training
-   Adapters
-   Prompt tuning
-   Prefix tuning

---

# 9. Supervised Fine-Tuning (SFT)

## Definition

**Supervised Fine-Tuning (SFT)** trains a pretrained model on examples
containing desired outputs.

A dataset might look like:

``` text
Instruction → Desired response
```

### Example

``` text
Instruction:
Explain SQL injection in simple language.

Desired response:
SQL injection occurs when...
```

## Typical SFT workflow

1.  Choose a pretrained model.
2.  Prepare high-quality training examples.
3.  Split data into training/validation sets.
4.  Train using a suitable objective.
5.  Evaluate on held-out data.
6.  Check for overfitting and unwanted behavior.
7.  Deploy the adapted model.

### SFT is commonly used for

-   instruction following
-   formatting behavior
-   domain/task adaptation
-   style consistency
-   specialized response patterns

---

# 10. Preference Optimization and RLHF

## Why preference training?

Sometimes there is no single objectively correct answer.

For example:

> Response A is more helpful than Response B.

Human or synthetic preference data can be used to optimize model
behavior.

## RLHF

A classic RLHF pipeline can involve:

``` text
Pretrained Model
      ↓
Supervised Fine-Tuning
      ↓
Preference Data
      ↓
Reward Model
      ↓
Reinforcement Learning
      ↓
Aligned Model
```

RLHF is therefore more specific than simply saying "fine-tuning."

## DPO --- Direct Preference Optimization

**DPO** is a preference-optimization method that can optimize a model
directly from preference pairs without the traditional separate
reward-model + PPO-style RL pipeline.

Conceptually:

``` text
Prompt
  ├── Preferred response
  └── Rejected response
          ↓
        DPO
          ↓
   Updated model
```

### Key distinction

-   **SFT:** learns from desired responses.
-   **RLHF:** uses human preference signals with an RL-based alignment
    pipeline.
-   **DPO:** directly optimizes preference data using a simpler
    objective than classic RLHF.

---

# 11. Catastrophic Forgetting

**Catastrophic forgetting** occurs when adaptation causes a model to
lose some previously learned capabilities or knowledge.

This is one reason evaluation should test both:

-   the new target task
-   important capabilities the model already had

PEFT can reduce the amount of base-model modification, but it does not
automatically eliminate every adaptation risk.

---

# 12. RAG --- Retrieval-Augmented Generation

## Definition

**RAG** retrieves relevant external information and places it into the
model's context before generating an answer.

Basic flow:

``` text
User Question
      ↓
Retriever
      ↓
Relevant Documents
      ↓
Prompt + Retrieved Context
      ↓
LLM
      ↓
Answer
```

## When RAG is useful

Use RAG when:

-   information changes frequently
-   answers should be grounded in private documents
-   you need citations/source grounding
-   retraining the model for every knowledge update is impractical

## RAG vs Fine-Tuning

| Feature | RAG | Fine-Tuning |
|---|---|---|
| **Main purpose** | Provide knowledge/context | Change behavior/capability |
| **Updates** | Easy to update documents | Requires training |
| **External knowledge** | Retrieved at runtime | Learned during training |
| **Good for changing facts** | Yes | Usually not ideal |
| **Good for consistent behavior/style** | Limited | Stronger |

### Important

RAG does **not** automatically teach the model new permanent knowledge.

It supplies relevant information during inference.

---

# 13. Full Comparison

| Feature | Prompt Engineering | Prompt Tuning | LoRA / PEFT | Full Fine-Tuning | RAG |
|---|---|---|---|---|---|
| **Changes base weights?** | No | No | Usually no | Yes | No |
| **Training required?** | No | Yes | Yes | Yes | No model training required |
| **Uses learned parameters?** | No | Yes | Yes | Yes | No |
| **Main goal** | Control output | Efficient task adaptation | Efficient model adaptation | Deep adaptation | Ground answers in external data |
| **Cost** | Very low | Low–Medium | Low–Medium | High | Low–Medium |
| **Flexibility** | High | Medium | High | Medium | High |
| **Best for** | Fast-changing tasks | Learned task prompts | Efficient specialization | Major adaptation | Changing/private knowledge |

---

# 14. When to Use Which?

### Use Prompt Engineering when:

-   You need a solution quickly.
-   The task changes frequently.
-   You don't need model training.
-   Good instructions/context are enough.

### Use Prompt Tuning when:

-   You have a repeated task.
-   You have training data.
-   You want a very small learned task-specific parameter set.
-   You want to keep the base model frozen.

### Use LoRA/PEFT when:

-   Prompting is not enough.
-   You need stronger behavioral/task adaptation.
-   Full fine-tuning is too expensive.
-   You want lightweight adapters for multiple tasks.

### Use Full Fine-Tuning when:

-   You have substantial high-quality data and compute.
-   Deep model adaptation is justified.
-   The benefits outweigh the cost and risk.

### Use RAG when:

-   Knowledge changes frequently.
-   You have a private/document corpus.
-   Answers need grounding in external sources.
-   You want to update knowledge without retraining the model.

---

# 15. Decision Tree

``` text
Do you need to change the model?
        │
        ├── No
        │    │
        │    ├── Need external/changing knowledge?
        │    │        └── RAG
        │    │
        │    └── Need better instructions?
        │             └── Prompt Engineering / ICL
        │
        └── Yes
             │
             ├── Want very small learned parameters?
             │        └── Prompt Tuning
             │
             ├── Need stronger efficient adaptation?
             │        └── LoRA / PEFT
             │
             └── Need broad/deep adaptation and have resources?
                      └── Full Fine-Tuning
```

---

# 16. Common Interview Traps

### Trap 1

**"Prompt tuning means manually improving prompts."**

❌ Incorrect.

That describes **prompt engineering**.

Prompt tuning learns soft prompt parameters through training.

------------------------------------------------------------------------

### Trap 2

**"Fine-tuning always updates every model parameter."**

❌ Incorrect.

Full fine-tuning does, but PEFT methods can adapt a model by training
only a small subset/additional parameters.

------------------------------------------------------------------------

### Trap 3

**"LoRA changes the original model weights during training."**

Usually ❌ incorrect.

In standard LoRA, the original pretrained weights remain frozen and the
trainable low-rank adapters learn the update.

------------------------------------------------------------------------

### Trap 4

**"RAG teaches the model new knowledge permanently."**

❌ Incorrect.

RAG retrieves information at inference time and provides it as context.

------------------------------------------------------------------------

### Trap 5

**"DPO and RLHF are exactly the same."**

❌ Incorrect.

Both can use preference data for alignment, but DPO uses a direct
preference-optimization objective rather than the classic reward-model +
reinforcement-learning pipeline.

---

# 17. Quick Memory Table

| Concept | Remember it as |
|---|---|
| **Prompt Engineering** | **Write better instructions** |
| **ICL** | **Show examples in context** |
| **Prompt Tuning** | **Learn soft prompts** |
| **PEFT** | **Train a small part** |
| **LoRA** | **Train low-rank adapters** |
| **QLoRA** | **Quantization + LoRA** |
| **SFT** | **Learn from desired answers** |
| **RLHF** | **Optimize from human preferences with RL** |
| **DPO** | **Direct preference optimization** |
| **RAG** | **Retrieve knowledge at runtime** |
| **Full Fine-Tuning** | **Update most/all model parameters** |

---

# 18. Core Takeaways --- Exam Ready

1.  **Prompt Engineering** = manually design the input/context; no model
    training.
2.  **ICL** = use instructions/examples in the context without changing
    model parameters.
3.  **Prompt Tuning** = train learnable soft prompt parameters while
    keeping the pretrained model frozen.
4.  **PEFT** = adapt a pretrained model by training a small number of
    parameters.
5.  **LoRA** = PEFT method using low-rank trainable matrices.
6.  **QLoRA** = quantized base model + LoRA-style adapters.
7.  **SFT** = train on instruction/response or other supervised
    examples.
8.  **RLHF** = preference-based alignment using a reinforcement-learning
    pipeline.
9.  **DPO** = directly optimize preference pairs with a simpler
    objective than classic RLHF.
10. **RAG** = retrieve external information at inference time instead of
    retraining for every knowledge update.
11. **Full fine-tuning** is not the same thing as PEFT; PEFT is a
    parameter-efficient form of model adaptation.
12. Choose the method based on **task requirements, data, compute, cost,
    update frequency, and desired behavior**.

---

## 19. One-Line Mental Model

> **Prompt Engineering changes the instructions.\
> ICL adds examples to the context.\
> Prompt Tuning learns soft prompts.\
> PEFT/LoRA learns small adapters.\
> Full Fine-Tuning updates the model broadly.\
> RAG supplies external knowledge at runtime.**

------------------------------------------------------------------------

## 20. Sources for Further Study

-   Hugging Face PEFT --- soft prompts and prompt tuning
-   Hugging Face PEFT --- LoRA
-   Hugging Face PEFT --- quantization / QLoRA
-   InstructGPT --- RLHF
-   DPO --- Direct Preference Optimization
