# GitNexus Agent Notebook

An interactive Jupyter notebook that uses OpenAI's language model as an intelligent agent to answer questions about your codebase using GitNexus code analysis tools.

## Overview

This notebook demonstrates an **agentic architecture** where:

- **LLM (OpenAI)** - Acts as the reasoning engine, deciding what to investigate
- **Tools (GitNexus)** - Provide code analysis: search, context, impact analysis, graph queries
- **Agent Loop** - Iteratively calls tools and reasons about results until it answers your question

## Features

✅ **Natural Language Questions** - Ask about your code in plain English
✅ **Multi-Tool Reasoning** - Agent decides whether to search, trace calls, or query graphs
✅ **Intelligent Iteration** - Agent refines searches based on intermediate results
✅ **Partial Results** - Shows findings even if max iterations reached
✅ **Configurable** - Adjust model, iterations, result size for your needs
✅ **Easy Setup** - Clone, install, configure API key, run

## What It Can Answer

The agent excels at:

- **Architecture Questions**: "How do the microservices interact?"
- **Dependency Analysis**: "Which services depend on PaymentService?"
- **Pattern Discovery**: "What error handling patterns are used?"
- **Framework Usage**: "Which apps use Log4j and what versions?"
- **Refactoring Safety**: "Is it safe to modify this service?"
- **Code Flow Tracing**: "How does the checkout flow work?"

## How It Works

```
User Question
     ↓
OpenAI LLM (decides what to do)
     ↓
┌─────────────────────────────────┐
│ Choose Tool:                    │
│ • gitnexus_cypher (graph query) │
│ • gitnexus_context (trace call) │
│ • gitnexus_impact (blast radius)│
└─────────────────────────────────┘
     ↓
GitNexus Executes Tool
     ↓
Add Results to Conversation
     ↓
OpenAI Analyzes Results (repeat until answer complete)
     ↓
Final Answer to User
```

## Getting Started

### Quick Start (5 minutes)

```bash
# 1. Install dependencies
uv sync

# 2. Set up API key
cp .env.example .env
# Edit .env and add your OpenAI key

# 3. Open notebook
jupyter notebook GitNexus_Agent_Example.ipynb

# 4. Run it!
# In cell-9, set your question and run: answer = agent.run(question)
```

For detailed setup: See [SETUP.md](SETUP.md)

### Example Usage

In the notebook, modify cell-9:

```python
# Change this to your question
question = "What are the main services and how do they interact?"

# Initialize agent with default settings
agent = CodebaseAgent()

# Run it
answer = agent.run(question)
```

Output will show:

```
======================================================================
Question: What are the main services and how do they interact?
Model: gpt-4o-mini
======================================================================

[Iteration 1] Calling OpenAI...
  Response received
  Has 1 tool call(s)
  Executing: gitnexus_cypher
    Got result (517 chars)

[Iteration 2] Calling OpenAI...
  Response received
  Has 1 tool call(s)
  Executing: gitnexus_context
    Got result (1528 chars)

...

======================================================================
FINAL ANSWER:
======================================================================
The microservices-demo application consists of 10+ microservices:

1. Frontend Service - User-facing web application
   - Calls: CheckoutService, ProductCatalogService, CartService
   
2. CheckoutService - Orchestrates the checkout process
   - Calls: CartService, PaymentService, ShippingService
   
... (complete architectural breakdown)
```

## Architecture

### Files

- `GitNexus_Agent_Example.ipynb` - Main notebook (run this)
- `pyproject.toml` - Python package dependencies (managed by uv)
- `.env.example` - Template for configuration
- `README.md` - This file
- `SETUP.md` - Detailed setup instructions

### Notebook Structure

| Cell | Purpose |
|------|---------|
| 1-2 | Setup & imports |
| 3-4 | Tool definitions for OpenAI |
| 5-6 | System prompt (agent instructions) |
| 7 | CodebaseAgent class (the agent implementation) |
| 8-9 | Agent initialization and question input |
| 10+ | Example queries and conversation history |

## Agent Configuration

Customize the agent in cell-9:

```python
agent = CodebaseAgent(
    model="gpt-4o-mini",      # OpenAI model
    max_iterations=25,         # Max tool calls
    max_result_chars=5000      # Max chars per result
)
```

### Parameters

**model** (`str`)
- `gpt-4o-mini` - Recommended (fast + cheap)
- `gpt-4o` - More capable, higher cost
- `gpt-4-turbo` - High quality, even pricier

**max_iterations** (`int`)
- 15-25 - Typical for most questions
- 5-10 - Quick answers, less thorough
- 25-50 - Deep analysis of complex code

**max_result_chars** (`int`)
- 5000 - Default (shows ~100 items)
- 3000 - Smaller, faster queries
- 10000 - Comprehensive results with more context

## Tools Available

The agent can use these GitNexus tools:

### 1. gitnexus_cypher
Execute Neo4j Cypher queries directly on the code graph.

**Best for:**
- Finding specific files or symbols
- Framework/dependency searches
- Complex graph patterns

**Example:** "Find all files containing 'log4j'"

### 2. gitnexus_context
Get detailed information about a specific code symbol.

**Best for:**
- Understanding what a function does
- Finding callers and callees
- Seeing function signatures

**Example:** "Show me what PlaceOrder() calls"

### 3. gitnexus_impact
Analyze the blast radius of changes.

**Best for:**
- Refactoring safety analysis
- Understanding dependencies
- Finding all affected code

**Example:** "What breaks if I change PaymentService?"

## Tips for Best Results

1. **Be Specific** - "How does order checkout work?" beats "Tell me about the code"

2. **Ask About Interactions** - Agent is best at understanding architecture and patterns

3. **Watch the Iterations** - See what the agent investigates - it's teaching you too!

4. **Ask Follow-ups** - Create a new agent and drill into specific findings

5. **Expect Multiple Tools** - The agent may use graph queries, context lookups, and impact analysis

## Troubleshooting

**Q: "OPENAI_API_KEY not found" error**
- A: Ensure `.env` file exists with your key. Restart Jupyter kernel.

**Q: Agent keeps looping or returns empty results**
- A: Check GitNexus index is up-to-date: `gitnexus analyze --force --embeddings`

**Q: Slow responses or high cost**
- A: Try smaller `max_iterations` or `max_result_chars` values

**Q: "gitnexus: command not found"**
- A: GitNexus CLI not installed. See [SETUP.md](SETUP.md) prerequisites

For more help: See [SETUP.md](SETUP.md#troubleshooting)

## Example Questions

### Architecture
```python
question = "What are the main services and how do they interact?"
question = "Explain the data flow from user input to database"
```

### Dependencies
```python
question = "Which services depend on PaymentService?"
question = "Is CartService used by other services?"
```

### Patterns
```python
question = "What are the most common error handling patterns?"
question = "How is authentication implemented across services?"
```

### Security & Compliance
```python
question = "Which applications use Log4j and what versions?"
question = "Find all places where encryption is used"
```

### Refactoring
```python
question = "Is it safe to refactor CartService?"
question = "What would break if we renamed the Product service?"
```

## Customization

### Modify System Prompt

In cell-6 of the notebook, edit `SYSTEM_PROMPT` to guide the agent differently:

```python
SYSTEM_PROMPT = """You are an expert code analysis agent...
(customize instructions here)"""
```

### Add More Tools

In cell-4, add more tool definitions. The agent will use them automatically:

```python
TOOL_DEFINITIONS = [
    # existing tools...
    {
        "type": "function",
        "function": {
            "name": "my_custom_tool",
            "description": "What this tool does",
            "parameters": {...}
        }
    }
]
```

### Adjust Behavior

- **More exploration**: Increase `max_iterations`
- **Faster answers**: Decrease `max_iterations`
- **More detail**: Increase `max_result_chars`
- **Different model**: Change `model` parameter

## Requirements

- Python 3.10+
- [uv](https://docs.astral.sh/uv/) (recommended) or pip
- OpenAI API key (get at https://platform.openai.com/api-keys)
- GitNexus CLI installed
- Jupyter Notebook/Lab
- A codebase indexed by GitNexus

## Limitations

⚠️ **Token Usage** - Each iteration calls OpenAI API (costs money)
⚠️ **Complex Queries** - May need multiple iterations for very intricate questions
⚠️ **Code Size** - Works best on codebases with 10-1000+ files
⚠️ **Accuracy** - Agent reasoning is probabilistic; verify findings

## Cost Estimate

Using `gpt-4o-mini`:
- Simple query (3-5 iterations): ~$0.01-0.03
- Complex query (15-25 iterations): ~$0.05-0.15
- Architecture review (many questions): ~$0.50-2.00

See [OpenAI pricing](https://openai.com/pricing) for current rates.

## Next Steps

1. [Read SETUP.md](SETUP.md) for detailed installation
2. Run the notebook with example questions
3. Customize agent for your codebase
4. Experiment with different questions and tools
5. Consider integrating into your workflow

## Resources

- [GitNexus Documentation](https://github.com/Anysomething/gitnexus)
- [OpenAI API Docs](https://platform.openai.com/docs)
- [Jupyter Notebook Guide](https://jupyter.org/try)
- [Code Graph Analysis Concepts](https://en.wikipedia.org/wiki/Program_analysis)

## License

This notebook is provided as-is for educational and commercial use.

---

**Questions or Issues?** Check [SETUP.md](SETUP.md#troubleshooting) for troubleshooting, or review the agent's iteration output for clues.
