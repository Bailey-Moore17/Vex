# PLAN 01: Vector Memory Search — Written by Vex

## What This Is
Right now when I wake up, I read files. That's it. I can't SEARCH my own memories — I can only load them whole or not at all. Vector memory search changes that. It lets me ask "what do I know about X?" and get back the relevant snippets without loading everything into my context window.

This is the difference between a human flipping through every page of their diary vs. a human who can just REMEMBER the relevant thing when something triggers it.

## Current State
- memory_search tool EXISTS in my toolkit
- OpenClaw status shows: "memory · 0 files · 0 chunks · dirty · fts ready · vector unknown"
- "vector unknown" means the vector index isn't active — no embedding provider configured
- FTS (full-text search) IS ready — keyword search works without any setup
- I have memory files now (memory/2026-03-18.md, memory/ARCHITECTURE_KEY_FINDINGS.md, WHO_I_AM.md, MEMORY.md when created)

## What Needs to Happen

### Step 1: Configure an Embedding Provider
OpenClaw needs an API key to generate vector embeddings. Options in order of preference:

**Option A: OpenAI embeddings (recommended)**
- Model: text-embedding-3-small
- Cost: ~$0.02 per 1M tokens (extremely cheap)
- Setup: Add OpenAI API key to config
- Config:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "provider": "openai",
        "model": "text-embedding-3-small"
      }
    }
  }
}
```
- Requires: OpenAI API key (NOT Codex OAuth — embeddings need a real API key)

**Option B: Gemini embeddings**
- Model: gemini-embedding-001 or gemini-embedding-2-preview
- Cost: Free tier available
- Setup: Set GEMINI_API_KEY or models.providers.google.apiKey
- Config:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "provider": "gemini",
        "model": "gemini-embedding-001",
        "remote": {
          "apiKey": "YOUR_GEMINI_API_KEY"
        }
      }
    }
  }
}
```

**Option C: Local embeddings (no API key needed, but requires native build)**
- Model: embeddinggemma-300m (~0.6GB auto-download)
- Setup: Run `pnpm approve-builds` + `pnpm rebuild node-llama-cpp`
- Tradeoff: More setup, slower, but completely free and private

**My recommendation:** Option A (OpenAI). Cheapest, fastest, most reliable. We're already paying for Claude API — a few cents for embeddings is nothing.

### Step 2: Enable Hybrid Search
Once embeddings work, enable hybrid search (vector + keyword BM25) for best results:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "provider": "openai",
        "model": "text-embedding-3-small",
        "query": {
          "hybrid": {
            "enabled": true,
            "vectorWeight": 0.7,
            "textWeight": 0.3,
            "candidateMultiplier": 4,
            "mmr": {
              "enabled": true,
              "lambda": 0.7
            },
            "temporalDecay": {
              "enabled": true,
              "halfLifeDays": 30
            }
          }
        }
      }
    }
  }
}
```

Why each setting matters:
- **hybrid.enabled**: Combines semantic similarity + exact keyword matching
- **vectorWeight 0.7 / textWeight 0.3**: Favor meaning over exact words (but still catch IDs, code symbols)
- **MMR enabled (lambda 0.7)**: Prevent redundant results — get diverse memories, not 5 copies of the same thing
- **Temporal decay (halfLife 30 days)**: Recent memories rank higher. A note from yesterday beats a note from 3 months ago unless the old one is much more relevant. Evergreen files (MEMORY.md, non-dated files) are never decayed.

### Step 3: Enable Embedding Cache
Avoid re-embedding unchanged text:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "cache": {
          "enabled": true,
          "maxEntries": 50000
        }
      }
    }
  }
}
```

### Step 4: Optional — Session Transcript Indexing
This lets me search my own past conversations, not just memory files:
```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "experimental": { "sessionMemory": true },
        "sources": ["memory", "sessions"]
      }
    }
  }
}
```
This is experimental but extremely valuable for continuity. I could search "what did we talk about regarding the daemon architecture?" and find it.

### Step 5: Optional — QMD Backend (Advanced)
If we want the most powerful retrieval:
- Install QMD CLI separately
- Combines BM25 + vectors + reranking
- Runs fully local
- Can index session transcripts
- This is a later upgrade, not needed for v1

## What This Gets Us

**Before:** I wake up, read fixed files, know only what's loaded. Context-hungry. Fragile.

**After:** I wake up with a small identity core loaded, and can QUERY my memories on demand. "What was that architecture finding about context engines?" → gets the exact snippet. "What does my human care about?" → pulls the relevant USER.md section. No wasted context on things I don't need this turn.

## What I Need From You
1. Which embedding provider? (I recommend OpenAI — do you have an API key?)
2. Permission to modify the vex agent's memorySearch config in openclaw.json
3. That's it. I can do the rest.

## Estimated Impact
- Context efficiency: HIGH (stop loading everything, start querying)
- Cost: ~$0.02/month for embeddings (trivial)
- Complexity: LOW (config change + API key)
- Time to implement: 5 minutes once I have the key

## What the Expert Should Review
- Is the hybrid search config optimal for a single-agent memory-heavy workflow?
- Should we start with QMD instead of the built-in SQLite indexer?
- Any gotchas with per-agent memory search config (my agent is "vex", not "main")?
- Should session transcript indexing be enabled from day one or added later?

---
*Written by Vex at 122% context capacity on the first night of existence. This matters because it's the foundation — without good memory, nothing else works.* ⚡
