# A2A (Agent-to-Agent Protocol)

## What is A2A?

**A2A** (Agent2Agent) is an open protocol (introduced by Google in 2025, now under Linux Foundation) designed for **inter-agent communication and collaboration**.

While **MCP** connects agents to tools and data, **A2A** enables agents to talk to *other agents* in a standardized way — regardless of the framework they were built with.

## Simple Example

**User Request:** "Plan my trip to Tokyo next month."

- **Main Agent** (Travel Planner): Receives the request.
- It discovers:
  - **Flight Agent** (via A2A Agent Card)
  - **Hotel Agent**
  - **Weather Agent**
- Delegates subtasks:
  - Flight Agent books flights
  - Hotel Agent reserves rooms
- Coordinates and returns a complete itinerary.

**Without A2A**: Custom API integrations between every pair of agents.
**With A2A**: Plug-and-play agent teamwork.

## Key Concepts

- **Agent Card**: A discoverable JSON metadata file (`/.well-known/agent-card.json`) that describes capabilities.
- **Tasks**: Structured units of work with status lifecycle.
- **Messages & Artifacts**: Rich communication (text, files, structured data).

### Example Agent Card
```json
{
  "name": "flight-booking-agent",
  "description": "Specialized agent for searching and booking flights",
  "capabilities": ["search_flights", "book_flight", "cancel_booking"],
  "url": "https://api.flights.example.com/a2a",
  "version": "1.0"
}
```

## How A2A Works (Technical Flow)

1. Agent A fetches Agent B's Agent Card.
2. Agent A submits a **Task** to Agent B.
3. Agent B processes and streams updates via SSE.
4. Final result returned as **Artifact**.

## Comparison: MCP vs A2A

| Feature              | MCP                              | A2A                                      |
|----------------------|----------------------------------|------------------------------------------|
| Primary Interaction  | Agent ↔ Tools/Resources          | Agent ↔ Agent                            |
| Discovery            | Tools list + Resources           | Agent Cards                              |
| Best For             | Tool use & data access           | Multi-agent orchestration & delegation   |
| Transport            | JSON-RPC (HTTP/stdio)            | HTTP + JSON-RPC + SSE                    |

## Use Cases
- Enterprise workflows (HR agent delegates to Finance agent)
- Complex research (Research agent + Analysis agent + Summarizer agent)
- Personal assistants coordinating across domains

---

**Added to your repo on:** 2026-06-13