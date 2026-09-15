# Model Context Protocol (MCP)

## Quick Overview

| Name             | Definition                                                                       | Example                                                    |
| ---------------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Function Calling | Lets an AI model request a specific function or tool                             | Call a weather API                                         |
| MCP              | An open protocol that standardizes how AI applications connect to tools and data | Connect an AI agent to GitHub                              |
| MCP Host         | The AI application that uses MCP                                                 | Claude Desktop, Cursor                                     |
| MCP Client       | The component that connects a host to an MCP server                              | Client connected to GitHub server                          |
| MCP Server       | Provides tools, resources, or prompts to an AI application                       | GitHub MCP Server                                          |
| AI Agent         | A system that can decide and perform multiple actions to achieve a goal          | Search GitHub, analyze an issue, then send a Slack message |

MCP (Model Context Protocol) was introduced by Anthropic in 2024 as an open protocol for connecting AI applications with external tools, data, and services. It helps developers use a common interface instead of building a different integration for every AI application.

## 1. Function Calling

**Definition:** Function calling allows an AI model to request that an application execute a specific function.

**Example:**

```text
User: What is the weather in Ahmedabad?

AI → Calls get_weather("Ahmedabad")
Application → Gets data from weather API
AI → "The weather is 32°C."
```

**Instructions:**

* Define the available functions and their input parameters clearly.
* Validate the arguments before executing a function.
* Use function calling when the task has clear, well-defined actions.
* Function calling by itself does not create a complete autonomous agent.

## 2. Model Context Protocol (MCP)

**Definition:** MCP is an open standard that provides a common way for AI applications to communicate with external tools, resources, and services.

**Example:**

```text
AI Application
      ↓
   MCP Client
      ↓
   MCP Server
      ↓
   GitHub API
```

**Instructions:**

* Use MCP when an AI application needs standardized access to external capabilities.
* An MCP server can expose tools, resources, and prompts.
* MCP reduces the need to create separate integration formats for every AI application.
* Always review permissions before allowing an MCP server to perform actions.

## 3. MCP Host

**Definition:** The MCP Host is the AI application that provides the environment where MCP connections are used.

**Example:**

```text
Cursor
  ↓
MCP Client
  ↓
GitHub MCP Server
```

**Instructions:**

* The host manages the overall AI interaction.
* Examples include AI-powered development environments and desktop applications.
* The host can connect to one or more MCP servers.
* The host is different from the MCP server.

## 4. MCP Client

**Definition:** An MCP Client is the component inside an MCP host that communicates with an MCP server.

**Example:**

```text
Cursor
  ↓
MCP Client
  ↓
GitHub MCP Server
```

**Instructions:**

* The client establishes communication with an MCP server.
* It discovers the capabilities exposed by the server.
* It sends requests to use available tools or access resources.
* A host can use multiple MCP clients for different server connections.

## 5. MCP Server

**Definition:** An MCP Server is a program that exposes capabilities such as tools and resources through the MCP protocol.

**Example:**

```text
GitHub MCP Server

Tools:
- search_repositories
- search_issues
- create_issue
```

**Instructions:**

* Clearly describe every tool and its parameters.
* Validate input before calling external APIs.
* Return structured, useful results to the client.
* Give servers only the permissions they actually need.

## 6. MCP Tools

**Definition:** MCP tools are actions that an AI application can request an MCP server to perform.

**Example:**

```text
Tool: search_repositories

Input:
{
  "query": "python security"
}

Output:
{
  "repositories": [...]
}
```

**Instructions:**

* Give each tool a clear name and description.
* Define its input schema.
* Validate user-controlled input.
* Be careful with tools that can modify or delete data.

## 7. MCP Resources

**Definition:** MCP resources provide information or data that an AI application can access through an MCP server.

**Example:**

```text
Local Log MCP Server
        ↓
   application.log
        ↓
     AI Agent
```

**Instructions:**

* Resources can represent files, database information, or other application data.
* Use resources when the AI needs information rather than an action.
* Control access to sensitive data.
* Avoid exposing unnecessary private information.

## 8. AI Agent

**Definition:** An AI Agent is a system that can reason about a goal, choose actions, use tools, and continue until the task is completed.

**Example:**

```text
User:
"Find the GitHub issue related to this error and send it to Slack."

AI Agent:
1. Read the error log
2. Search GitHub repositories
3. Search GitHub issues
4. Analyze the results
5. Send the relevant issue to Slack
```

**Instructions:**

* Agents can combine multiple tools to complete a larger task.
* MCP provides standardized access to tools and resources.
* The agent decides which available capabilities are useful.
* Add human approval for sensitive operations such as deleting data or changing infrastructure.

## 9. MCP vs Function Calling

**Definition:** Function calling is a mechanism for invoking functions, while MCP standardizes how AI applications discover and communicate with external capabilities.

**Example:**

```text
Function Calling:
AI → Application → Function → API

MCP:
AI Application → MCP Client → MCP Server → API
```

**Instructions:**

* Function calling can be implemented directly inside an application.
* MCP provides a reusable protocol for connecting clients and servers.
* MCP does not replace function calling; MCP tools can ultimately be executed by the application.
* Choose the simpler approach when MCP is unnecessary.

## 10. MCP Architecture

**Definition:** MCP architecture separates the AI application, connection layer, capability provider, and external systems.

**Example:**

```text
                    AI Application
                    (MCP Host)
                         |
                    MCP Client
                         |
                    MCP Server
                    /          \
              Local Data    Remote Services
              Files/DB      APIs/Services
```

**Instructions:**

* The host provides the AI application environment.
* The client communicates with MCP servers.
* Servers expose tools and resources.
* External systems should be protected with authentication and appropriate permissions.

## 11. GitHub MCP Server Example

**Definition:** A GitHub MCP server can expose GitHub operations as MCP tools.

**Example:**

```text
Available tools:

search_repositories
search_issues
create_issue
```

A simplified tool definition might look like:

```javascript
{
  name: "search_repositories",
  description: "Search for GitHub repositories",
  inputSchema: {
    type: "object",
    properties: {
      query: {
        type: "string"
      }
    }
  }
}
```

**Instructions:**

* Define the tool name and purpose clearly.
* Describe required inputs with a schema.
* Validate arguments before using the GitHub API.
* Return useful structured results to the AI application.

The example in the source uses a GitHub MCP server to expose repository and issue operations, while the actual implementation calls the GitHub API.

## 12. MCP Server and APIs

**Definition:** An MCP server usually acts as a standardized interface between an AI application and an existing service or API.

**Example:**

```text
AI Agent
   ↓
MCP Client
   ↓
GitHub MCP Server
   ↓
GitHub REST API
   ↓
GitHub
```

**Instructions:**

* MCP does not magically create the underlying service.
* The server may call existing APIs, databases, files, or other systems.
* Authentication should be handled securely.
* API errors should be handled properly and returned in a useful format.

The source demonstrates this pattern with the GitHub API: the MCP server describes available capabilities and then uses GitHub's API to perform the actual operation.

## 13. Multi-Server AI Agent

**Definition:** An AI agent can use multiple MCP servers to complete a multi-step task.

**Example:**

```text
                 AI Agent
                /    |    \
               /     |     \
       Log Server  GitHub   Slack
                    Server  Server

1. Read error logs
2. Search GitHub issues
3. Send useful results to Slack
```

**Instructions:**

* Use separate servers when capabilities belong to different systems.
* Let the agent select the required tools based on the task.
* Control the order of dependent operations.
* Require confirmation before high-impact actions.

The source describes this pattern using separate local-log, GitHub, and Slack servers.

## 14. Why MCP Is Useful

**Definition:** MCP makes it easier to connect AI applications with many external systems using a common protocol.

**Example:**

```text
Without MCP:

AI → Custom GitHub integration
AI → Custom Slack integration
AI → Custom Database integration

With MCP:

AI → MCP → GitHub
         → Slack
         → Database
```

**Instructions:**

* Reuse existing MCP servers when they are trustworthy.
* Avoid rebuilding the same integration for every AI application.
* Use open standards to improve interoperability.
* Check server documentation and security before connecting it.

MCP's main ecosystem benefit is that service providers can expose capabilities through a common standard, while developers can reuse existing MCP servers.

## Quick Memory

```text
Function Calling → AI asks an application to run a function
MCP → Standard way for AI apps to connect to capabilities
Host → The AI application
Client → Connects the host to an MCP server
Server → Provides tools and resources
Tool → An action the AI can request
Resource → Data the AI can access
Agent → Uses reasoning + tools to achieve a goal
API → The underlying service the MCP server may call
```
