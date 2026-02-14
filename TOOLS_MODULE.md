# Tools Module Explained

The Tools module provides secure, extensible access to system-level and custom actions (shell commands, file operations, web requests, etc.) that the agent can invoke as part of its reasoning and response generation. Below is a detailed breakdown of its files, responsibilities, and integration points.

---

## File-by-File Breakdown & Integration

### base.py: Tool Interface Definition
- **Role:** Defines the standard interface and contract for all tools.
- **Responsibilities:**
	- Specifies required methods (e.g., `execute`, `isAllowed`).
	- Ensures all tools are discoverable and safely invokable by the agent.
- **Integration:**
	- All tool implementations subclass this base.
	- `registry.py`: Uses the base interface to manage tools.

### shell.py, filesystem.py, web.py, etc.: Tool Implementations
- **Role:** Implements specific actions (e.g., shell commands, file read/write, web requests).
- **Responsibilities:**
	- Implements the logic for each tool action.
	- Enforces security checks (workspace restrictions, user allowlists, etc.).
	- Returns results in a standard format for the agent.
- **Integration:**
	- `base.py`: Subclasses the tool interface.
	- `registry.py`: Registers itself for agent discovery.
	- `config/`: Reads security settings and restrictions.

### registry.py: Tool Registration and Management
- **Role:** Registers and manages all available tools.
- **Responsibilities:**
	- Discovers and registers tool implementations.
	- Makes tool schemas available for LLM tool-calling.
	- Supports dynamic extension of available tools.
- **Integration:**
	- `agent/`: Looks up and invokes tools as needed.
	- `context.py`: Exposes tool schemas for LLM prompt construction.

---

## How the Tools Module Works with Others

- **agent/**: Invokes tools as requested by LLM or agent logic.
- **context.py**: Exposes available tool schemas for LLM tool-calling.
- **config/**: Enforces workspace restrictions and user allowlists for tool execution.
- **session/**: May use session/user context for access control.

---

## Example Execution Flow

```
1. [agent/] → [registry.py]: Looks up tool by name
2. [agent/] → [base.py]: Invokes `execute` method
3. [base.py] → [shell.py]/[filesystem.py]/etc.: Executes action with security checks
4. [tool implementation] → [agent/]: Returns result
```

---

## Extending the Tools Module

- Add a new tool: Subclass `base.py`, implement required methods, and register in `registry.py`.
- Harden security: Ensure all tools enforce workspace and user restrictions from config.
- Expose schemas: Make new tools discoverable for LLM tool-calling.

---

**This secure, extensible design enables powerful, safe tool use by the agent, supporting advanced automation and reasoning.**
