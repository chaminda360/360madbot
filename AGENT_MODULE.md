# Agent Module Explained

The Agent module is the core orchestrator of the nanobot system, responsible for managing the lifecycle of every user message, maintaining context and memory, invoking LLMs, and securely executing tools. Below is a detailed breakdown of its key components and their professional integration points.

---

## File-by-File Breakdown & Integration

### loop.py: Main Execution Loop
- **Role:** Central event loop for agent operation.
- **Responsibilities:**
	- Receives normalized messages from the bus.
	- Loads or creates session state via `session/`.
	- Builds the full context for the LLM using `context.py`.
	- Calls the LLM through the configured provider in `providers/`.
	- If the LLM response requests tool use, dispatches to the appropriate tool in `tools/`.
	- Appends tool results to the context and loops as needed until a final answer is produced.
	- Sends the final response back to the bus for delivery.
- **Integration:**
	- `session/`: Loads and persists user/session state.
	- `providers/`: Abstracts LLM calls.
	- `tools/`: Executes built-in and custom actions.
	- `skills/`, `cron/`: May trigger skills or scheduled tasks.

### context.py: Context Construction
- **Role:** Assembles the prompt and context for LLM calls.
- **Responsibilities:**
	- Injects system instructions (agent persona, rules).
	- Incorporates conversation history (from session/memory).
	- Enumerates available tools, exposing their JSON schema for LLM tool-calling.
	- Integrates relevant long-term memory (via `memory.py`).
- **Integration:**
	- `memory.py`: Supplies persistent memory.
	- `tools/`: Provides tool schemas.
	- `session/`: Supplies user/session context and history.

### memory.py: Persistent Long-Term Memory
- **Role:** Manages reliable, persistent memory for each user/session.
- **Responsibilities:**
	- Stores and retrieves long-term memory (facts, embeddings, summaries).
	- Redesigned in Feb 2026 for improved reliability and scalability.
	- Supports context retrieval for prompt construction.
- **Integration:**
	- `context.py`: Fetches memory for LLM context.
	- `session/`: May sync with session state.

### tools/: Built-in and Custom Actions
- **Role:** Houses all agent-executable tools and actions.
- **Responsibilities:**
	- Implements actions such as `shell`, `read_file`, `write_file`, `edit_file`, `list_dir`, etc.
	- All tools enforce `restrictToWorkspace` sandboxing for security.
	- Tools are discoverable and described via JSON schema for LLM tool-calling.
- **Integration:**
	- `loop.py`: Invokes tools as requested by LLM or agent logic.
	- `context.py`: Exposes tool schemas for LLM.
	- `config/`: Enforces user allowlists and workspace restrictions.

### subagent.py: Background and Isolated Task Execution
- **Role:** Runs background or isolated agent tasks (e.g., cron jobs, skill execution).
- **Responsibilities:**
	- Spawns sub-agents for parallel or scheduled work.
	- Ensures isolation from main agent state when needed.
- **Integration:**
	- `cron/`: Executes scheduled jobs.
	- `skills/`: Runs skill logic in isolation.
	- `session/`, `providers/`: Each subagent can have its own session and provider context.

---

## How the Agent Module Works with Others

- **session/**: Manages per-user/session state, including context, history, and preferences.
- **providers/**: Abstracts LLM calls, allowing easy switching between OpenAI, vLLM, etc.
- **skills/**: Pluggable features that can be invoked by the agent or scheduled.
- **cron/**: Schedules and triggers background tasks via subagents.
- **config/**: Loads API keys, allowlists, and workspace restrictions for secure operation.

---

## Example Execution Flow

```
1. [Bus] → [loop.py]: Receives message
2. [loop.py] → [session/]: Loads session state
3. [loop.py] → [context.py]: Builds prompt/context
4. [loop.py] → [providers/]: Calls LLM
5. [loop.py] → [tools/]: Executes tool if requested
6. [loop.py] → [context.py]: Appends tool result, loops if needed
7. [loop.py] → [session/]: Updates memory/context
8. [loop.py] → [Bus]: Sends final response
```

---

## Extending the Agent Module

- Add new tools: Create a module in `tools/` and register its schema.
- Enhance context: Modify `context.py` to include new context sources.
- Improve memory: Extend `memory.py` for advanced retrieval or storage.
- Add background tasks: Use `subagent.py` for isolated or scheduled work.

---

**As a senior AI/ML architect or engineer, this modular design enables robust, secure, and extensible agent workflows, supporting advanced tool use, memory, and multi-agent orchestration.**
