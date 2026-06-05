# SWE-Agent Intelligence Platform

> A repository-aware multi-agent software engineering platform built on top of SWE-Agent.

SWE-Agent Intelligence Platform extends the original SWE-Agent with long-term memory, repository architecture analysis, knowledge graphs, bug localization, research planning, and evaluation metrics. The goal is to move from a coding agent that edits files reactively to a system that understands a codebase, remembers past fixes, reasons about impact, and improves over time.

## Overview

Traditional coding agents can usually:

- Understand an issue
- Edit code
- Run tests
- Generate patches
- Interact with repositories

This project adds the missing intelligence layer:

- Static repository understanding
- Knowledge graph reasoning
- Persistent memory
- Research before coding
- Bug localization
- Agent performance evaluation
- Dashboard-ready artifacts

The platform is designed as a local-first prototype. It works with plain JSON, JSONL, Markdown, and Cypher files today, while leaving a clear path toward Neo4j, Qdrant, PostgreSQL, LangGraph, Docker sandboxes, and a React dashboard.

## Key Idea

Most coding agents search the repository blindly when they receive an issue. This project changes that workflow.

Instead of:

```text
Issue -> Search repo -> Guess files -> Edit -> Test
```

This platform uses:

```text
Issue
  -> Research plan
  -> Memory retrieval
  -> Repository architecture graph
  -> Bug localization
  -> Focused edit/test loop
  -> Evaluation record
```

That makes the agent more explainable, more reusable, and easier to evaluate.

## Features

| Feature | Status | Description |
| --- | --- | --- |
| Repository Architect Agent | Implemented | Builds AST-based repository architecture facts |
| Knowledge Graph Agent | Implemented | Converts architecture facts into graph JSON and Neo4j Cypher |
| Memory Agent | Implemented | Stores short-term, long-term, and semantic memory |
| Deep Research Agent | Implemented | Creates research plans before coding |
| Bug Localization Agent | Implemented | Ranks likely files for an issue |
| Evaluation Agent | Implemented | Tracks metrics and generates dashboard summaries |
| Test Generation Agent | Planned | Generate unit, integration, and edge-case tests |
| Security Agent | Planned | Scan for secrets, injection risks, OWASP issues |
| PR Review Agent | Planned | Review patches like a senior engineer |
| Refactoring Agent | Planned | Detect duplication, dead code, and complexity |
| Project Manager Agent | Planned | Break high-level requests into tasks and epics |
| React Dashboard | Planned | Visualize repository graph, agents, memory, and metrics |

## Implemented Agents

### 1. Repository Architect Agent

Builds a static model of the repository.

It extracts:

- Source files
- Modules
- Classes
- Functions
- Imports
- Function calls
- Class inheritance
- Module containment
- Technology signals

Example output artifact:

```text
.sweagent/repository_architecture.json
```

Example use cases:

- Understand a repository quickly
- Show frontend/backend/module structure
- Feed architecture facts into other agents
- Build dependency and call graphs
- Prepare data for graph databases

### 2. Knowledge Graph Agent

Converts the repository architecture file into a graph model.

Outputs:

```text
.sweagent/knowledge_graph.json
.sweagent/knowledge_graph.cypher
```

The Cypher file is Neo4j-ready. It can represent relationships like:

```text
Module A imports Module B
Class X inherits Class Y
Function A calls Function B
Repository uses Technology T
File F defines Module M
```

This enables graph questions such as:

- What depends on this module?
- What could break if this class changes?
- Which files define this service?
- Which functions call this function?
- Which modules are most central?

### 3. Memory Agent

Stores persistent memories across agent runs.

Memory types:

```text
short_term  -> current task context
long_term   -> previous issues and fixes
semantic    -> important repository knowledge
```

Example memory:

```text
Issue #35 root cause was JWT middleware validation.
```

Storage:

```text
.sweagent/memory/
  long_term.jsonl
```

This is intentionally simple for the prototype. It can later be backed by Qdrant, Weaviate, or PostgreSQL.

### 4. Deep Research Agent

Creates a research plan before coding.

It generates queries for:

- Official documentation
- GitHub issues
- StackOverflow
- Security advisories
- Papers and technical references

Outputs:

```text
.sweagent/research/deep_research_plan.json
.sweagent/research/deep_research_plan.md
```

This is useful because agents often start coding too quickly. A research step makes the process more reliable and explainable.

### 5. Bug Localization Agent

Ranks likely files for a bug before the coding agent edits anything.

Signals used:

- Issue text
- File names
- Module names
- Class names
- Function names
- Import graph centrality
- Memory matches

Example output:

```text
sweagent/agent/reviewer.py              25.0%
tests/test_agent.py                     19.4%
sweagent/agent/agents.py                13.9%
sweagent/agent/history_processors.py     8.3%
```

Outputs:

```text
.sweagent/bug_localization.json
.sweagent/bug_localization.md
```

This helps the system avoid searching the entire repository blindly.

### 6. Evaluation Agent

Tracks agent performance over time.

Example metrics:

- Fix success rate
- Test pass rate
- Latency
- Cost
- Number of analyzed files
- Knowledge graph node count
- Knowledge graph relationship count
- Smoke test success

Outputs:

```text
.sweagent/evaluation/runs.jsonl
.sweagent/evaluation/dashboard.md
```

Example dashboard table:

```text
| Metric | Avg | Min | Max | Count |
| --- | ---: | ---: | ---: | ---: |
| smoke_success | 1.000 | 1.000 | 1.000 | 1 |
| knowledge_graph_nodes | 1799.000 | 1799.000 | 1799.000 | 1 |
```

## Architecture

```text
                         +----------------+
                         |   Repository   |
                         +-------+--------+
                                 |
                                 v
                 +---------------+----------------+
                 | Repository Architect Agent     |
                 | AST, imports, calls, classes   |
                 +---------------+----------------+
                                 |
                 repository_architecture.json
                                 |
                                 v
                 +---------------+----------------+
                 | Knowledge Graph Agent          |
                 | graph JSON + Neo4j Cypher      |
                 +---------------+----------------+
                                 |
               knowledge_graph.json / .cypher
                                 |
                                 v
       +-------------------------+--------------------------+
       |                                                    |
       v                                                    v
+------+----------------+                         +---------+----------+
| Bug Localization Agent |                         | Memory Agent       |
| ranks likely files     |                         | remembers fixes    |
+------+----------------+                         +---------+----------+
       |                                                    |
       +-------------------------+--------------------------+
                                 |
                                 v
                         +-------+--------+
                         | Coding Agent   |
                         | edit/test/fix  |
                         +-------+--------+
                                 |
                                 v
                         +-------+--------+
                         | Evaluation     |
                         | metrics/report |
                         +----------------+
```

## Tech Stack

Current implementation:

- Python
- Python AST parsing
- Dataclasses
- JSON / JSONL
- Markdown reports
- Neo4j-compatible Cypher export
- CLI commands integrated into SWE-Agent

Planned production stack:

- FastAPI backend
- React frontend
- Neo4j for dependency and architecture graphs
- Qdrant or Weaviate for semantic memory
- PostgreSQL for issues, tasks, PRs, users, and agent runs
- Redis and Celery for queues and background jobs
- Docker sandboxes for safe code execution
- LangGraph for agent orchestration
- Prometheus and Grafana for metrics

## Quick Start

### 1. Analyze the Repository

```bash
python3 -m sweagent.run.run repo-architect . --summary
```

This creates:

```text
.sweagent/repository_architecture.json
```

### 2. Build the Knowledge Graph

```bash
python3 -m sweagent.run.run knowledge-graph .sweagent/repository_architecture.json
```

This creates:

```text
.sweagent/knowledge_graph.json
.sweagent/knowledge_graph.cypher
```

### 3. Add a Memory

```bash
python3 -m sweagent.run.run memory add "Issue #35 root cause was JWT middleware validation." --metadata issue=35
```

### 4. Search Memory

```bash
python3 -m sweagent.run.run memory search "JWT middleware"
```

### 5. Create a Research Plan

```bash
python3 -m sweagent.run.run deep-research "Memory leak in FastAPI middleware" --architecture .sweagent/repository_architecture.json
```

This creates:

```text
.sweagent/research/deep_research_plan.md
```

### 6. Localize a Bug

```bash
python3 -m sweagent.run.run bug-localize "Model retry loop fails when history processor truncates context window" --architecture .sweagent/repository_architecture.json
```

This creates:

```text
.sweagent/bug_localization.md
.sweagent/bug_localization.json
```

### 7. Record Evaluation Metrics

```bash
python3 -m sweagent.run.run evaluation record demo-run --metric fix_success=1 --metric latency_seconds=42 --metric cost_usd=0.18
```

### 8. Generate an Evaluation Dashboard

```bash
python3 -m sweagent.run.run evaluation dashboard
```

This creates:

```text
.sweagent/evaluation/dashboard.md
```

## CLI Commands

| Command | Alias | Purpose |
| --- | --- | --- |
| `repo-architect` | `ra` | Build repository architecture JSON |
| `knowledge-graph` | `kg` | Build graph JSON and Neo4j Cypher |
| `memory` | `mem` | Add, list, search, and clear memories |
| `deep-research` | `dr` | Create a research plan |
| `bug-localize` | `bl` | Rank likely files for an issue |
| `evaluation` | `eval` | Record and summarize metrics |

## Generated Artifacts

```text
.sweagent/
  README.md
  repository_architecture.json
  knowledge_graph.json
  knowledge_graph.cypher
  bug_localization.json
  bug_localization.md
  memory/
    long_term.jsonl
  research/
    deep_research_plan.json
    deep_research_plan.md
  evaluation/
    runs.jsonl
    dashboard.md
```

## Project Structure

```text
sweagent/
  intelligence/
    repository_architect.py
    knowledge_graph.py
    memory_agent.py
    deep_research.py
    bug_localization.py
    evaluation_agent.py
  run/
    repository_architect.py
    knowledge_graph.py
    memory.py
    deep_research.py
    bug_localization.py
    evaluation.py
docs/
  intelligence_agents.md
tests/
  test_repository_architect.py
  test_knowledge_graph.py
  test_memory_agent.py
  test_deep_research.py
  test_bug_localization.py
  test_evaluation_agent.py
```

## Example Demo Flow

A strong demo flow for this project:

1. Run the Repository Architect Agent on the repo.
2. Show the generated architecture JSON.
3. Generate the knowledge graph and Cypher file.
4. Add a memory from a previous bug.
5. Give the system a new issue.
6. Generate a research plan.
7. Run bug localization.
8. Show ranked files before coding.
9. Record evaluation metrics.
10. Show the evaluation dashboard.

This demonstrates static analysis, graph reasoning, memory, planning, localization, and evaluation in one workflow.

## What Makes This Different

Many coding-agent projects focus only on calling an LLM and editing files. This project focuses on the systems around the coding agent:

- How does the agent understand a large repository?
- How does it remember previous fixes?
- How does it know where to look before editing?
- How does it explain why a file is relevant?
- How do we measure whether the agent is improving?

Those are the questions this platform is designed to answer.

## Current Limitations

- Static analysis currently focuses mainly on Python.
- JavaScript and TypeScript support is lightweight.
- Memory retrieval uses local lexical search for now.
- Neo4j, Qdrant, PostgreSQL, and dashboard integration are planned but not required for the local demo.
- The system generates research plans but does not yet automatically browse the web.

## Roadmap

### Agent Roadmap

- Test Generation Agent
- Security Agent
- PR Review Agent
- Refactoring Agent
- Pull Request Agent
- Project Manager Agent
- CI/CD Agent
- Multi-repository Agent

### Dashboard Roadmap

- Repository view
- Interactive dependency graph
- Agent run monitor
- Memory explorer
- Bug localization dashboard
- Research plan viewer
- PR review dashboard
- Evaluation dashboard
- Cost and latency dashboard

### Infrastructure Roadmap

- Neo4j graph persistence
- Qdrant semantic memory
- PostgreSQL app database
- FastAPI service layer
- React frontend
- Docker sandbox execution
- LangGraph orchestration
- Prometheus/Grafana observability

## Interview Pitch

This project extends SWE-Agent from a coding executor into a repository-aware multi-agent software engineering system. It builds an architecture model of the repository, converts it into a knowledge graph, stores long-term memory, creates research plans, localizes bugs before editing, and evaluates agent performance over time.

It combines ideas from static analysis, compiler tooling, graph databases, retrieval systems, agent orchestration, and MLOps-style evaluation.

## Skills Demonstrated

- Software architecture
- Static analysis
- AST parsing
- Graph modeling
- Knowledge graph design
- Agent system design
- Persistent memory
- CLI design
- Evaluation and metrics
- Testable Python engineering
- Product thinking for developer tools

## Attribution

This project is based on the open-source SWE-Agent project created by researchers from Princeton University and Stanford University.

Original SWE-Agent repository:

```text
https://github.com/SWE-agent/SWE-agent
```

This repository adds an experimental intelligence layer for portfolio, research, and interview demonstration purposes.

## License

The original SWE-Agent project is licensed under the MIT License. See [LICENSE](LICENSE).
