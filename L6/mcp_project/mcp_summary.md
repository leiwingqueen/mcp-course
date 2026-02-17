# Model Context Protocol (MCP) - Architecture Overview

## Scope

The Model Context Protocol includes the following projects:
- **MCP Specification**: A specification outlining implementation requirements for clients and servers
- **MCP SDKs**: SDKs for different programming languages that implement MCP
- **MCP Development Tools**: Tools including the MCP Inspector
- **MCP Reference Server Implementations**: Reference implementations of MCP servers

**Important**: MCP focuses solely on the protocol for context exchange—it does not dictate how AI applications use LLMs or manage the provided context.

## Core Concepts of MCP

### Participants

MCP follows a client-server architecture with three key participants:

1. **MCP Host**: The AI application that coordinates and manages one or multiple MCP clients (e.g., Claude Code, Claude Desktop, Visual Studio Code)
2. **MCP Client**: A component that maintains a connection to an MCP server and obtains context from it for the MCP host to use
3. **MCP Server**: A program that provides context to MCP clients (can be local or remote)

**Note**: Local MCP servers using STDIO transport typically serve a single MCP client, whereas remote MCP servers using Streamable HTTP transport typically serve many MCP clients.

### Layers

MCP consists of two layers:

#### 1. Data Layer
Defines the JSON-RPC based protocol for client-server communication, including:
- **Lifecycle management**: Connection initialization, capability negotiation, and connection termination
- **Server features**: Tools, resources, and prompts
- **Client features**: Sampling, elicitation, and logging
- **Utility features**: Notifications and progress tracking

#### 2. Transport Layer
Manages communication channels and authentication between clients and servers:
- **Stdio transport**: Uses standard input/output streams for direct process communication (local, no network overhead)
- **Streamable HTTP transport**: Uses HTTP POST for client-to-server messages with optional Server-Sent Events (remote, supports OAuth authentication)

## Data Layer Protocol

MCP uses JSON-RPC 2.0 as its underlying RPC protocol.

### Lifecycle Management
MCP is a stateful protocol requiring lifecycle management to negotiate capabilities between client and server.

### Primitives

Primitives define what clients and servers can offer each other.

#### Server Primitives (Servers expose to Clients):
1. **Tools**: Executable functions that AI applications can invoke to perform actions
   - Examples: file operations, API calls, database queries
   - Discovery: `tools/list`
   - Execution: `tools/call`

2. **Resources**: Data sources that provide contextual information to AI applications
   - Examples: file contents, database records, API responses
   - Discovery: `resources/list`
   - Retrieval: `resources/get`

3. **Prompts**: Reusable templates that help structure interactions with language models
   - Examples: system prompts, few-shot examples
   - Discovery: `prompts/list`
   - Retrieval: `prompts/get`

**Example**: An MCP server for a database can expose:
- Tools for querying the database
- A resource containing the database schema
- A prompt with few-shot examples for interacting with the tools

#### Client Primitives (Clients expose to Servers):
1. **Sampling**: Allows servers to request language model completions from the client's AI application
   - Method: `sampling/complete`
   - Use case: Servers want access to a language model without including an LLM SDK

2. **Elicitation**: Allows servers to request additional information from users
   - Method: `elicitation/request`
   - Use case: Get more information from the user or ask for confirmation

3. **Logging**: Enables servers to send log messages to clients for debugging and monitoring

#### Utility Primitives:
- **Tasks (Experimental)**: Durable execution wrappers for deferred result retrieval and status tracking
  - Use cases: expensive computations, workflow automation, batch processing, multi-step operations

### Notifications

Real-time notifications enable dynamic updates between servers and clients:
- Sent as JSON-RPC 2.0 notification messages (no response expected)
- Example: Tool update notifications when server's available tools change
- Enables MCP servers to provide real-time updates to connected clients

## Example Flow

### 1. Initialization (Lifecycle Management)
Client sends an `initialize` request with:
- Protocol version
- Client capabilities (e.g., elicitation support)
- Client information (name, version)

Server responds with:
- Protocol version
- Server capabilities (e.g., tools, resources, prompts)
- Server information

### 2. Tool Discovery
Client requests available tools using `tools/list`

Server responds with list of available tools, each containing:
- Name
- Description
- Input schema

### 3. Tool Execution
Client sends `tools/call` request with:
- Tool name
- Arguments

Server responds with:
- Execution results
- Content (text, images, or embedded resources)

### 4. Notifications
Server can send notifications to inform clients about changes:
- Tool list changes
- Resource updates
- Progress updates

## Key Design Principles

1. **Transport Agnostic**: Same JSON-RPC message format across all transport mechanisms
2. **Dynamic Discovery**: Clients use `*/list` methods to discover available primitives dynamically
3. **Model Independent**: Servers can use sampling to access language models without embedding LLM SDKs
4. **Real-time Updates**: Notifications enable servers to push updates to clients
5. **Capability Negotiation**: Initialize handshake ensures both parties agree on supported features

## Architecture Benefits

- **Standardization**: Common protocol for AI applications to access context
- **Flexibility**: Supports both local and remote servers
- **Extensibility**: New primitives can be added without breaking existing implementations
- **Security**: Transport layer handles authentication and secure communication
- **Developer-Friendly**: SDKs abstract away protocol complexity
