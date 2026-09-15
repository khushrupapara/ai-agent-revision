# Agent Memory

## Quick Overview

| Name              | Definition                                                           | Example                                 |
| ----------------- | -------------------------------------------------------------------- | --------------------------------------- |
| Agent Memory      | Stores and retrieves information so an AI agent can use past context | Remembering a user's preferences        |
| Short-Term Memory | Keeps information needed during the current conversation or task     | Remembering the current booking steps   |
| Long-Term Memory  | Stores information that can be used across future sessions           | Remembering a user's preferred hotel    |
| Episodic Memory   | Stores specific past events or experiences                           | User previously booked a trip to London |
| Procedural Memory | Stores learned skills or ways of performing tasks                    | Remembering how to complete a booking   |
| Semantic Memory   | Stores facts, knowledge, and relationships                           | Storing product specifications          |

## 1. Agent Memory

**Definition:** Agent memory allows an AI agent to store, retrieve, and reuse information instead of starting from zero on every interaction.

**Example:**

```text
User: I prefer direct flights.

Later:

Agent: I'll look for direct flights for your next trip.
```

**Instructions:**

* Use memory to preserve useful information from previous interactions.
* Retrieve only information relevant to the current task.
* Store important facts, preferences, and past events when needed.
* Avoid storing unnecessary or sensitive information.

## 2. Short-Term Memory

**Definition:** Short-term memory stores information needed for the current conversation or task.

**Example:**

```text
User: Book a hotel in Mumbai.

Agent:
1. Search Mumbai hotels
2. Compare prices
3. Check availability
4. Book the selected hotel
```

The agent needs the previous steps and current conversation to continue the task correctly.

**Instructions:**

* Use it for current conversation context and active tasks.
* It is limited by the model's context window.
* Remove or summarize irrelevant information when the context becomes too large.
* Checkpointing can be used to save and restore conversation state.

## 3. Long-Term Memory

**Definition:** Long-term memory stores useful information that can be retrieved across different conversations or sessions.

**Example:**

```text
Session 1:
User: I prefer hotels with free Wi-Fi.

Session 2:
User: Find me a hotel in Delhi.

Agent:
Searches for hotels with free Wi-Fi.
```

**Instructions:**

* Store information that will be useful in future interactions.
* Use databases, files, or vector stores to persist memories.
* Retrieve memories based on the current context.
* Manage old or irrelevant memories to prevent memory pollution.

## 4. Episodic Memory

**Definition:** Episodic memory stores specific past events or experiences.

**Example:**

```text
User previously booked:
Destination → London
Purpose → Business conference
Hotel → City-center hotel
```

**Instructions:**

* Store important events from previous interactions.
* Include useful metadata such as dates or user IDs.
* Retrieve past events when they are relevant.
* Do not keep every conversation detail forever.

## 5. Procedural Memory

**Definition:** Procedural memory stores learned skills, procedures, or effective ways of performing tasks.

**Example:**

```text
Task: Book a flight

Procedure:
1. Check available flights
2. Prefer direct flights
3. Check suitable departure time
4. Confirm with the user
5. Complete the booking
```

**Instructions:**

* Use it to represent how tasks should be performed.
* Store reliable procedures and learned strategies.
* Update procedures when better methods are discovered.
* Important actions should still follow safety and permission rules.

## 6. Semantic Memory

**Definition:** Semantic memory stores general facts, knowledge, and relationships that an agent can retrieve when needed.

**Example:**

```text
Product:
Name → Laptop
RAM → 16 GB
Storage → 512 GB
Processor → Intel Core i7
```

**Instructions:**

* Store facts and relationships separately from conversation history when useful.
* Vector embeddings can help retrieve information based on meaning.
* Use metadata such as user IDs, timestamps, or categories for filtering.
* Keep stored knowledge accurate and up to date.

## 7. Memory Storage

**Definition:** Memory storage is where an agent keeps information so it can retrieve it later.

**Example:**

```text
AI Agent
   ↓
Memory System
   ↓
Database / Vector Store
   ↓
Stored Memories
```

**Instructions:**

* Databases can store structured facts and conversation state.
* Vector stores can support semantic retrieval using embeddings.
* Choose storage based on the type of memory you need.
* Protect stored data with appropriate access controls.

## 8. Memory Retrieval

**Definition:** Memory retrieval is the process of finding stored information that is relevant to the agent's current task.

**Example:**

```text
Current Query:
"What hotel did I choose last month?"

Query → Memory Search
      ↓
Relevant Past Memory
      ↓
Agent Response
```

**Instructions:**

* Retrieve memories based on relevance to the current context.
* Vector search can find memories with similar meanings.
* Structured filters can search by fields such as user ID or date.
* Avoid retrieving large amounts of irrelevant memory.

## 9. Memory Management

**Definition:** Memory management controls what an agent stores, retrieves, updates, summarizes, and eventually removes.

**Example:**

```text
Conversation
     ↓
Extract useful information
     ↓
Store memory
     ↓
Retrieve when relevant
     ↓
Update or remove outdated memory
```

**Instructions:**

* Decide which information is worth storing.
* Summarize long conversations when necessary.
* Remove or expire outdated information.
* Prevent irrelevant memories from affecting the agent's responses.

## Quick Memory

```text
Agent Memory → Lets an agent remember and reuse information

Short-Term → Current task or conversation

Long-Term → Information kept across sessions

Episodic → What happened

Procedural → How to do something

Semantic → What is known

Storage → Where memories are saved

Retrieval → How relevant memories are found

Management → Store, update, summarize, and remove memories
```
