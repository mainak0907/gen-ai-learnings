# MCP Advanced: JSON-RPC 2.0, Resources, Prompts & Making Agents Compatible

## 1. JSON-RPC 2.0 Recap
JSON-RPC 2.0 is a stateless, lightweight RPC protocol using JSON.

**Request Example:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {"city": "Mumbai"}
  }
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {"temperature": 29, "condition": "Cloudy"}
}
```

MCP builds its entire communication on top of this.

---

## 2. Resources & Prompt Templates in MCP (Detailed)

### Resources
**Resources** provide **read-only data** to the AI model. They are like dynamic context files that the AI can request on-demand.

- URI-based (e.g., `resource://notes/project-plan.md`)
- Can be files, DB records, API results, etc.
- AI/host decides when to load them into context.

**Code Example (Python with FastMCP):**
```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("MyKnowledgeBase")

@mcp.resource("article://{article_id}")
def get_article(article_id: str) -> str:
    """Fetch article content by ID"""
    articles = {
        "1": "MCP is a universal connector for AI agents...",
        "2": "JSON-RPC 2.0 explained..."
    }
    return articles.get(article_id, "Article not found")

@mcp.resource("file://{path}")
def read_file(path: str) -> str:
    """Read local file content"""
    try:
        with open(path, "r") as f:
            return f.read()
    except Exception as e:
        return f"Error reading file: {e}"
```

**How AI Uses It:**
The AI can call `resources/read` with URI → gets content injected into its context.

### Prompts (Prompt Templates)
**Prompts** are reusable, parameterized **prompt templates** or workflows. They help standardize how the AI behaves for specific tasks.

**Code Example:**
```python
@mcp.prompt()
def code_review(code: str, language: str = "Python", focus: str = "best practices") -> list:
    """Reusable code review prompt template"""
    return [
        {
            "role": "system",
            "content": f"You are an expert {language} code reviewer."
        },
        {
            "role": "user",
            "content": f"""Review the following {language} code focusing on {focus}:

{code}

Provide structured feedback with sections: Strengths, Issues, Suggestions."""
        }
    ]

@mcp.prompt()
def summarize_document(document: str, style: str = "concise") -> str:
    """Simple string-based prompt template"""
    return f"Summarize the following document in {style} style:\n\n{document}"
```

**Usage:** AI calls `prompts/get` with name and arguments → gets a ready-to-use prompt.

---

## 3. How to Make an Agent MCP Compatible

### Option 1: Use Existing Frameworks (Easiest)
- **LangChain / LangGraph**: Use `langchain-mcp-adapters`
- **PraisonAI** or other agent frameworks with built-in MCP support
- **Claude Desktop / Cursor / VS Code**: Connect directly via config

### Option 2: Build Your Own MCP Server (Full Control)

**Complete Minimal MCP Server Example:**
```python
# server.py
from mcp.server.fastmcp import FastMCP
import asyncio

mcp = FastMCP("GenAI_Learnings_Server")

# Tool
@mcp.tool()
def add_numbers(a: int, b: int) -> int:
    """Add two numbers"""
    return a + b

# Resource
@mcp.resource("notes://{topic}")
def get_learning_notes(topic: str) -> str:
    """Get your GenAI learning notes"""
    notes = {
        "mcp": "MCP is the USB-C for AI agents...",
        "agents": "AI agents are autonomous systems..."
    }
    return notes.get(topic.lower(), "No notes found")

# Prompt
@mcp.prompt()
def research_topic(topic: str) -> list:
    """Research prompt template"""
    return [
        {"role": "system", "content": "You are a helpful research assistant."},
        {"role": "user", "content": f"Provide a detailed overview on: {topic}"}
    ]

if __name__ == "__main__":
    mcp.run()
```

**Run it:**
```bash
pip install "mcp[cli]"
python server.py
```

**Connect to Claude Desktop / Cursor:**
Add to `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "genai-learnings": {
      "command": "python",
      "args": ["/path/to/server.py"]
    }
  }
}
```

### Option 3: Make Existing Agent MCP-Compatible
Wrap your agent to act as MCP client or expose its capabilities as an MCP server.

---

**Next Steps for Your Repo:**
- Create a full `projects/mcp-server/` folder with this code.
- Test with Claude Desktop or Cursor.

Pull updates with `git pull`!