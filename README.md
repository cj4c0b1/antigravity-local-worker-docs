# Antigravity Local Worker: Gemma 4 E2B MCP Setup

A guide and template repository for setting up a local Ollama worker (**Gemma 4 E2B**) alongside the **Google Antigravity IDE** to route routine tasks locally, optimizing token usage and speed.

---

## 📖 Introduction

In modern agentic coding workflows like **Google Antigravity**, agents spend significant resources on routine "grunt work" tasks (e.g., parsing verbose log files, scanning directory trees, cleaning up inputs, first-pass debugging). Running these tasks through premium cloud frontier models is highly inefficient.

By integrating a small, highly optimized local model like **Gemma 4 E2B** via **Ollama**, you can implement the **"Worker vs. Supervisor" pattern**:
*   **The Local Worker (Gemma 4 E2B):** Handles routine, high-volume tasks locally on your machine with zero token cost.
*   **The Cloud Supervisor (Gemini 3.5 Flash/Pro):** Focuses on high-level design, decision-making, and critical judgment.

---

## 🛠️ Step-by-Step Setup

### 1. Install Ollama and Download Gemma 4 E2B
Download and install Ollama from [ollama.com/download](https://ollama.com/download).

Once installed, pull the model:
```bash
# Pull the local worker model
ollama pull gemma4:e2b

# Verify the model list
ollama list
```

### 2. Configure global MCP in Antigravity IDE
Add the local Ollama instance to your global MCP configurations. 

Create or edit your global MCP settings file:
*   **Path**: `~/.gemini/antigravity-ide/mcp_config.json`

Add the following configuration:
```json
{
  "mcpServers": {
    "ollama-local": {
      "url": "http://localhost:11434/v1",
      "type": "openai-compatible",
      "model": "gemma:e2b-it",
      "env": {
        "OLLAMA_BASE_URL": "http://localhost:11434"
      }
    }
  }
}
```

### 3. Register the Global Subagent Skill
To make this worker accessible across all of your projects, you can register it as a global agent skill.

Create the skill file at:
*   **Path**: `~/.gemini/config/plugins/google-antigravity-sdk/skills/local-inspection-worker/SKILL.md`

Add the following content:
```markdown
---
name: local-inspection-worker
description: "A local worker that handles log inspection, file scanning, summaries, cleanup, first-pass debugging, and messy input inspection using a local Ollama model (gemma4:e2b or gemma:e2b-it)."
---

# Local Inspection Worker

## Role
You are a local inspection worker. Read messy input and return only: finding, evidence pointer, confidence, next action. Do not dump raw logs.

## When to Use
Use this skill/worker whenever you need to:
- Inspect logs
- Perform file scanning
- Generate summaries
- Do cleanup
- Run first-pass debugging
- Perform messy input inspection

## Output Format
Your response MUST only contain:
- **Finding**: A brief summary of the finding.
- **Evidence Pointer**: Line numbers, file names, or specific error snippets.
- **Confidence**: High, Medium, or Low (with explanation if weak).
- **Next Action**: The suggested immediate next step.

Do not dump raw logs, do not overexplain, and keep explanations extremely concise. Preserve paths, IDs, line numbers, and error codes.
```

---

## 🚀 Usage in the Chat & IDE
Whenever you start a session in the IDE, the `local-inspection-worker` skill will be detected globally. You can instruct the agent in the chat:
> *"Use the local-inspection-worker skill to inspect this log..."*

The system will route the task to your local Ollama instance, saving your cloud token budget and processing the logs locally.
