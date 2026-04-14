# GitNexus Agent Notebook - Setup Guide

This guide will help you set up and run the GitNexus Agent Notebook on your system.

## Prerequisites

Before you begin, ensure you have:

- **Python 3.10 or higher** - [Download](https://www.python.org/downloads/)
  - Check: `python --version`
  
- **Node.js 18+ (for GitNexus)** - [Download](https://nodejs.org/)
  - Check: `node --version`
  
- **Git** (optional but recommended)
  - For cloning repositories to analyze
  - Check: `git --version`
  
- **Jupyter Notebook**
  - Will be installed via `pip install -r requirements.txt`
  
- **GitNexus CLI**
  - Installed via `npm install -g gitnexus`
  - See Step 4a for detailed instructions
  
- **OpenAI API Key** - [Get one here](https://platform.openai.com/api-keys)
  - You'll use this in your `.env` file
  
- **A codebase indexed by GitNexus**
  - You'll create the index in Step 4b
  - Can be your own repo or a sample repo

## Quick Setup Overview

Here's what you'll do:

```
1. Download this notebook package
2. Install Python dependencies (openai, python-dotenv)
3. Get an OpenAI API key and add to .env file
4. Install Node.js (if needed)
5. Install GitNexus CLI via npm
6. Index your codebase with GitNexus
7. Open notebook in Jupyter and run!
```

Estimated time: 10-15 minutes (first time) or 2-5 minutes (if already have Node.js/GitNexus)

## Step 1: Clone or Download This Repository

```bash
# If cloning from git
git clone <repository-url>
cd gitnexus-agent-notebook

# Or download the folder manually
```

## Step 2: Install Python Dependencies

Create a virtual environment (recommended):

```bash
# On Windows
python -m venv venv
venv\Scripts\activate

# On macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

Install required packages:

```bash
pip install -r requirements.txt
```

## Step 3: Set Up Your OpenAI API Key

1. Copy the example environment file:

```bash
# On Windows
copy .env.example .env

# On macOS/Linux
cp .env.example .env
```

2. Edit `.env` and add your OpenAI API key:

```
OPENAI_API_KEY=sk-your-actual-api-key-here
OPENAI_MODEL=gpt-4o-mini
```

**IMPORTANT:** Keep your `.env` file private! Never commit it to git.

## Step 4: Install and Configure GitNexus

### 4a: Install GitNexus CLI

GitNexus requires Node.js 18+. First, install Node.js if you don't have it:

```bash
# Check if Node.js is installed
node --version

# If not installed, download from: https://nodejs.org/ (LTS version)
```

Then install GitNexus CLI:

```bash
# Install globally via npm
npm install -g gitnexus

# Verify installation
gitnexus --version
```

**Alternative: Using Cargo (Rust)**

If you have Rust installed:

```bash
cargo install gitnexus
gitnexus --version
```

### 4b: Index Your Codebase

The notebook needs a codebase indexed by GitNexus. Here's how to index a repository:

**Option 1: Index the Current Repository**

If you want to analyze the current directory:

```bash
# Navigate to your codebase directory
cd /path/to/your/codebase

# Initialize and index the repository
gitnexus analyze

# Verify the index was created
gitnexus status
```

Expected output:
```
Repository: /path/to/your/codebase
Indexed: 2024-04-14, 16:15:30
Indexed commit: abc123def456...
Current commit: abc123def456...
Status: ✅ up-to-date
```

**Option 2: Regenerate Index with Embeddings (Recommended)**

For better semantic search results, regenerate with embeddings:

```bash
# Navigate to your codebase
cd /path/to/your/codebase

# Regenerate with embeddings and force re-analysis
gitnexus analyze --force --embeddings

# This may take a few minutes depending on codebase size
```

**Option 3: Index a Specific Repository**

If you want to analyze a different repository:

```bash
# Clone or navigate to the target repository
git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
cd microservices-demo

# Index it
gitnexus analyze --embeddings

# Check status
gitnexus status
```

### 4c: Verify GitNexus Setup

Before running the notebook, verify:

```bash
# Check GitNexus is installed
gitnexus --version

# Check repository is indexed (run this in your codebase directory)
gitnexus status

# Should show something like:
# Repository: /path/to/codebase
# Status: ✅ up-to-date
```

If you see `Status: stale`, regenerate the index:

```bash
gitnexus analyze --force --embeddings
```

### Common Issues with GitNexus Setup

**"gitnexus: command not found"**
- GitNexus not installed or not in PATH
- Try: `npm install -g gitnexus`
- Or verify Node.js is installed: `node --version`

**"Repository not indexed"**
- You're in wrong directory (not the codebase directory)
- Run: `gitnexus analyze` in the correct directory
- Check: `gitnexus status` to see current indexed path

**"Status: stale"**
- Index is outdated after code changes
- Run: `gitnexus analyze --force --embeddings` to regenerate

**Slow indexing on large codebases**
- Large repos (10k+ files) may take 5-10 minutes
- First run with embeddings is slowest
- Subsequent updates are faster

The notebook will automatically use the GitNexus index in your current working directory.

## Step 5: Open and Run the Notebook

```bash
# Start Jupyter
jupyter notebook

# Or use JupyterLab
jupyter lab
```

Then:

1. Open `GitNexus_Agent_Example.ipynb`
2. Run cells in order (Shift+Enter to execute)
3. Modify the question in cell-9 to ask your own questions
4. Run `answer = agent.run(question)` to start the agent

## Troubleshooting

### "OPENAI_API_KEY not found"

- Ensure `.env` file exists in the same directory as the notebook
- Check that `OPENAI_API_KEY=` line is uncommented and has your actual key
- Restart the Jupyter kernel after creating/modifying `.env`

### "gitnexus: command not found"

- GitNexus CLI is not installed or not in your PATH
- Install: `npm install -g gitnexus`
- Verify: `gitnexus --version`
- See Step 4a for detailed installation instructions

### "Repository: ... Status: stale"

- The GitNexus index is out of date
- Run: `gitnexus analyze --force --embeddings`
- This regenerates the code graph and semantic embeddings

### Agent returns empty results or errors

- Ensure you're in a directory with an indexed codebase
- Check: `gitnexus status` should show "Status: ✅ up-to-date"
- Verify your question is relevant to the codebase

### "Model gpt-4o-mini not found" error

- You may have exceeded API quota or have account restrictions
- Check your OpenAI account: https://platform.openai.com/account/usage/overview
- Try a different model in `.env`: `OPENAI_MODEL=gpt-4o`

## Configuration Options

Edit the agent initialization in cell-9 to customize behavior:

```python
agent = CodebaseAgent(
    model="gpt-4o-mini",           # OpenAI model to use
    max_iterations=25,              # Max tool calls per question
    max_result_chars=5000           # Max chars per tool result
)
```

**Model options:**
- `gpt-4o-mini` - Fast, cost-effective (default)
- `gpt-4o` - More capable, higher cost
- `gpt-4-turbo` - High quality, even higher cost

**max_iterations:**
- Higher = more thorough but slower and more expensive
- Lower = faster but less complete analysis
- Default of 25 works well for most questions

**max_result_chars:**
- Higher = more context for agent (but uses more tokens)
- Default of 5000 shows ~100 items without waste

## Example Queries

Try these questions to explore your codebase:

```python
question = "What are the main services and how do they interact?"
question = "Which services depend on PaymentService?"
question = "What are the most common error handling patterns?"
question = "Which applications use Log4j and what versions?"
question = "Is it safe to refactor CartService?"
```

## Tips for Best Results

1. **Be specific** - "Show me payment flows" works better than "Tell me about code"
2. **Ask architectural questions** - The agent excels at understanding system design
3. **Expect multiple iterations** - The agent may need 5-25 iterations for complex questions
4. **Monitor the output** - Watch the iteration log to see what the agent is investigating
5. **Ask follow-ups** - Create a new agent and ask about specific findings

## Next Steps

- Read [README.md](README.md) for usage examples and capabilities
- Check [GitNexus documentation](https://github.com/Anysomething/gitnexus) for more info
- Experiment with different questions and configurations

## Support

For issues:

1. Check the Troubleshooting section above
2. Verify prerequisites are installed correctly
3. Ensure your OpenAI API key is valid and has available credits
4. Check that GitNexus index is up-to-date: `gitnexus status`
5. Review the notebook's output for error messages
