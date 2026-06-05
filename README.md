SWE-Agent Intelligence Platform
An enhanced software engineering agent platform built on top of SWE-Agent.

This project extends a coding agent beyond issue understanding, code editing, test execution, and patch generation. It adds repository intelligence, persistent memory, knowledge graphs, bug localization, research planning, and evaluation so agents can reason about a codebase before changing it.

Why This Project
Most coding agents are good at isolated tasks, but they usually lack:

Long-term memory across issues
Deep repository understanding
Graph-based impact analysis
Multi-agent workflows
Bug localization before editing
Evaluation dashboards
Project-management style planning
This project turns SWE-Agent into a more complete software engineering intelligence system.

Current Features
Repository Architect Agent
Builds static architecture facts from a repository:

Python AST parsing
File, module, class, and function summaries
Import/dependency graph
Function call graph
Module containment graph
Class inheritance and method relationships
Technology detection
Output:

.sweagent/repository_architecture.json
Knowledge Graph Agent
Converts repository architecture into a graph representation.

Outputs:

.sweagent/knowledge_graph.json
.sweagent/knowledge_graph.cypher
The Cypher output can be loaded into Neo4j for repository reasoning such as:

What depends on this module?
What classes inherit from this base class?
What functions call this function?
What could break if this service changes?
Memory Agent
Stores and retrieves agent memory.

Memory types:

short_term: current task context
long_term: previous issues and fixes
semantic: important repository knowledge
Example memory:

Issue #35 root cause was JWT middleware validation.
Storage:

.sweagent/memory/*.jsonl
Deep Research Agent
Creates a research plan before coding.

It generates source-aware queries for:

Official documentation
GitHub issues
StackOverflow
Security advisories
Papers and technical references
Output:

.sweagent/research/deep_research_plan.md
.sweagent/research/deep_research_plan.json
Bug Localization Agent
Ranks likely files for an issue before the coding agent starts searching.

Signals used:

Issue text
File/module/class/function names
Import graph centrality
Memory matches
Example output:

sweagent/agent/reviewer.py                 25.0%
tests/test_agent.py                        19.4%
sweagent/agent/agents.py                   13.9%
sweagent/agent/history_processors.py        8.3%
Output:

.sweagent/bug_localization.json
.sweagent/bug_localization.md
Evaluation Agent
Tracks agent performance over time.

Example metrics:

Fix success rate
Test pass rate
Latency
Cost
Graph size
Smoke test success
Output:

.sweagent/evaluation/runs.jsonl
.sweagent/evaluation/dashboard.md
Architecture
Repository
   |
   v
Repository Architect Agent
   |
   +--> repository_architecture.json
   |
   v
Knowledge Graph Agent
   |
   +--> knowledge_graph.json
   +--> knowledge_graph.cypher
   |
   v
Bug Localization Agent

Issue
   |
   +--> Deep Research Agent
   +--> Memory Agent
   +--> Bug Localization Agent

Agent Runs
   |
   v
Evaluation Agent
Tech Stack
Current local-first implementation:

Python
AST static analysis
JSON / JSONL artifacts
Markdown reports
Neo4j-compatible Cypher export
Planned production stack:

FastAPI backend
React dashboard
Neo4j for repository graphs
Qdrant or Weaviate for vector memory
PostgreSQL for tasks, issues, PRs, and agent runs
Redis and Celery for background jobs
Docker sandboxes for safe execution
Prometheus and Grafana for metrics
LangGraph for multi-agent orchestration
Quick Start
From the repository root:

python3 -m sweagent.run.run repo-architect . --summary
Build the knowledge graph:

python3 -m sweagent.run.run knowledge-graph .sweagent/repository_architecture.json
Add a memory:

python3 -m sweagent.run.run memory add "Issue #35 root cause was JWT middleware validation." --metadata issue=35
Search memory:

python3 -m sweagent.run.run memory search "JWT middleware"
Create a research plan:

python3 -m sweagent.run.run deep-research "Memory leak in FastAPI middleware" --architecture .sweagent/repository_architecture.json
Localize a bug:

python3 -m sweagent.run.run bug-localize "Model retry loop fails when history processor truncates context window" --architecture .sweagent/repository_architecture.json
Record evaluation metrics:

python3 -m sweagent.run.run evaluation record demo-run --metric fix_success=1 --metric latency_seconds=42 --metric cost_usd=0.18
Create an evaluation dashboard:

python3 -m sweagent.run.run evaluation dashboard
Generated Artifacts
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
Project Structure
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
Roadmap
Next Agents
Test Generation Agent
Security Agent
PR Review Agent
Refactoring Agent
Pull Request Agent
Project Manager Agent
Dashboard
Planned React dashboard:

Repository view
Interactive dependency graph
Agent monitoring
Memory explorer
Bug localization view
PR review dashboard
Cost dashboard
Evaluation dashboard
Production Infrastructure
Neo4j-backed graph queries
Qdrant-backed semantic memory
PostgreSQL-backed tasks and agent runs
Docker sandbox execution
LangGraph orchestration
Prometheus/Grafana monitoring
Interview Pitch
This project extends SWE-Agent from a coding executor into a repository-aware multi-agent system. It builds a static and semantic understanding of the codebase, stores long-term memory from previous fixes, localizes bugs before editing, creates research plans before coding, and evaluates agent performance over time.

The result is a stronger software engineering agent architecture that combines static analysis, knowledge graphs, memory, agent orchestration, and evaluation.

Attribution
This project is based on the open-source SWE-Agent project from Princeton University and Stanford University researchers.

Original project:

https://github.com/SWE-agent/SWE-agent
This repository adds an experimental intelligence layer for portfolio, research, and interview demonstration purposes.

License
The original SWE-Agent project is licensed under the MIT License. See LICENSE.
