# Agent Memory Storage and Management

## Quick Overview

| Name                 | Definition                                        | Example                           |
| -------------------- | ------------------------------------------------- | --------------------------------- |
| RAG                  | Retrieves relevant information from external data | Search documents before answering |
| Vector Database      | Stores embeddings for similarity search           | Find similar documents            |
| User Profile Storage | Stores stable information about a user            | Name, language, preferences       |
| Summarization        | Condenses information while keeping key points    | Summarize old chat history        |
| Compression          | Reduces the size of stored or in-context data     | Shorten retrieved documents       |
| Forgetting           | Removes information that is no longer useful      | Delete expired memories           |
| Aging                | Reduces the importance of old information         | Lower priority of unused memories |

## 1. RAG and Vector Databases

**Definition:** RAG (Retrieval-Augmented Generation) allows an AI agent to retrieve relevant information from external data before generating an answer. A vector database stores information as embeddings and finds relevant data using similarity search.

**Example:**

```text
User asks: "What is our refund policy?"

RAG → Searches the knowledge base
Vector Database → Finds relevant refund information
LLM → Uses the retrieved information to answer
```

**Instructions:**

* Split documents into smaller chunks before creating embeddings.
* Store the embeddings in a vector database.
* Retrieve only the most relevant information for the current task.
* Use retrieved information as context for the LLM.

## 2. User Profile Storage

**Definition:** User profile storage keeps stable information about a user so an agent can reuse it across conversations.

**Example:**

```text
User: "I prefer Python examples."

Profile:
language = Python
preference = Practical examples

Later:
Agent automatically uses Python examples.
```

**Instructions:**

* Store long-term facts separately from temporary conversation history.
* Update the profile when the user provides a lasting preference or changes existing information.
* Avoid storing unnecessary or temporary information.
* A file, SQL database, or other persistent storage can be used.

## 3. Summarization and Compression

**Definition:** Summarization or compression reduces the amount of information while keeping the most important details.

**Example:**

```text
Long conversation:
User discussed Python, n8n, RAG, and AI agents.

Compressed memory:
User is learning Python, n8n, RAG, and AI agents.
```

**Instructions:**

* Use summarization when conversation history becomes too large.
* Keep important facts, decisions, and context.
* Remove repetitive or low-value details.
* Use compressed information to reduce context usage.

## 4. Forgetting and Aging Strategies

**Definition:** Forgetting and aging strategies control which memories should be removed or given lower priority over time.

**Example:**

```text
Old memory:
User wanted to attend an event next month.

After the event:
→ Remove or deprioritize the memory.
```

**Instructions:**

* Remove information that has expired or is no longer useful.
* Replace outdated information with newer information when appropriate.
* Lower the priority of memories that are rarely used.
* Prevent old or contradictory information from filling the memory system.

## Quick Memory

```text
RAG → Retrieve relevant external information
Vector Database → Store and search embeddings
User Profile → Remember stable user information
Summarization → Keep the important points in shorter form
Compression → Reduce the amount of context
Forgetting → Remove information that is no longer useful
Aging → Reduce the importance of old information
```
