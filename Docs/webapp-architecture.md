# NoA Web Application Architecture

## Overview
This document outlines the architecture for the NoA Web Application, designed to run on top of the `NoaClient` SDK. The architecture follows a **Hexagonal** pattern where the Core Logic (`NoaClient`) is decoupled from the Presentation Layer (CLI, Web, VSCode Extension).

## 1. System Architecture

```mermaid
graph TD
    User[User] -->|Browser| WebUI[Web Frontend (React/Next.js)]
    WebUI -->|REST/WS| Server[API Server (Node.js)]
    Server -->|Uses| SDK[NoaClient SDK]
    SDK -->|Calls| Engine[Core Engine]
    Engine -->|LLM API| OpenRouter[OpenRouter/OpenAI]
    Engine -->|WASM| LocalLLM[Local LLM (WASM)]
    SDK -->|Reads/Writes| FileSystem[File System (Memories/History)]
```

## 2. Core SDK (`NoaClient`)
The SDK (already implemented in `noa-client/core/client.js`) serves as the single source of truth for:
- **State Management**: History, Memories, Context, Metrics.
- **Business Logic**: Proposer-Reviewer-Fusion workflow.
- **Persistence**: Saving/Loading session data.

## 3. Web Backend (API Server)
A lightweight Node.js server will wrap `NoaClient`.

### API Endpoints
- `POST /api/chat`: Send a message (wraps `client.send()`).
- `GET /api/state`: Get full session state (history, memories, context).
- `POST /api/memory`: Add/Delete memories (wraps `client.updateMemories()`).
- `POST /api/config`: Update runtime config (model, policy).

### Real-time Updates
- **WebSocket / SSE**: To stream partial responses (if supported) or push state updates (e.g., "Memory Updated" events) to the frontend.

## 4. Web Frontend (GUI)
The frontend will visualize the internal state exposed by the SDK.

### Components
1.  **Chat Interface**:
    - Standard chat bubble layout.
    - Markdown rendering for responses.
    - Typing indicators (mapped to `client.state.busy`).

2.  **Memory Panel (Sidebar)**:
    - List of active memories.
    - "Add Memory" form (Key, Value, Importance).
    - Delete buttons for each memory.
    - *Maps to `/mem` CLI command.*

3.  **Context Inspector**:
    - Read-only view of `multi_session_context`.
    - Visual summary of the current session.
    - *Maps to `/ctx` CLI command.*

4.  **Control Center**:
    - Model Selector (Dropdown).
    - Policy Toggles (Strict Mode, Prompt Mode).
    - Mix Strategy Input.
    - *Maps to `/model`, `/set` CLI commands.*

## 5. Development Roadmap
1.  **Phase 1 (Done)**: Extract Core Logic into `NoaClient` SDK.
2.  **Phase 2 (Done)**: Implement CLI using `NoaClient` to verify abstraction.
3.  **Phase 3**: Build `server.js` (Express/Fastify) importing `NoaClient`.
4.  **Phase 4**: Build React Frontend consuming the API.

## 6. CLI vs Web Mapping
| Feature | CLI Command | Web GUI Element |
|---------|------------|-----------------|
| Chat | (Text Input) | Chat Input Box |
| Memory | `/mem list/add/del` | Memory Sidebar Panel |
| Context | `/ctx summary` | Session Info Card |
| Model | `/model set` | Settings Dropdown |
| History | `/save`, `/load` | Session Management Page |

This architecture ensures that the **CLI and Web App are feature-equivalent** because they share the exact same `NoaClient` instance and logic.
