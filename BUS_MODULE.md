# Bus Module Explained

The Bus module is the central communication backbone of the nanobot system, responsible for decoupling all major components (channels, agent, skills, etc.) and enabling event-driven, asynchronous workflows. Below is a detailed breakdown of its key files, responsibilities, and integration points.

---

## File-by-File Breakdown & Integration

### queue.py: Message and Event Queue
- **Role:** Implements the core message/event queue for the system.
- **Responsibilities:**
	- Receives normalized messages from all channels.
	- Queues messages for processing by the agent.
	- Handles asynchronous dispatch and delivery of responses/events.
	- Supports prioritization and batching if needed.
- **Integration:**
	- `channels/`: All inbound and outbound messages pass through the bus.
	- `agent/`: Consumes messages from the queue, posts responses/events back.
	- `skills/`, `cron/`: Can enqueue events or receive notifications.

### events.py: Event Types and Handling
- **Role:** Defines the event types, schemas, and event-handling logic.
- **Responsibilities:**
	- Enumerates all supported event types (message, tool result, skill trigger, etc.).
	- Provides event dispatch and subscription mechanisms.
	- Enables extensibility for new event-driven features.
- **Integration:**
	- `queue.py`: Uses event types for routing and handling.
	- `skills/`, `cron/`: Can subscribe to or emit events.

---

## How the Bus Module Works with Others

- **channels/**: All user/platform messages are normalized and sent to the bus for processing.
- **agent/**: Consumes messages from the bus, posts responses and tool results back.
- **skills/**: Can listen for or trigger events via the bus.
- **cron/**: Schedules and dispatches timed events through the bus.

---

## Example Execution Flow

```
1. [Channel] → [queue.py]: Normalized message enqueued
2. [queue.py] → [agent/]: Message dispatched for processing
3. [agent/] → [queue.py]: Response/event enqueued
4. [queue.py] → [Channel]: Response delivered
```

---

## Extending the Bus Module

- Add new event types: Extend `events.py` for new workflows (e.g., skill triggers, system alerts).
- Enhance queue logic: Modify `queue.py` for advanced routing, prioritization, or batching.
- Integrate with new modules: Use the bus as the communication backbone for any new feature.

---

**This event-driven, decoupled design enables robust, scalable, and extensible communication across the nanobot system.**
