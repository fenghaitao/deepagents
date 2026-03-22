# Configuration & Customization

<cite>
**Referenced Files in This Document**
- [README.md](file://README.md)
- [AGENTS.md](file://AGENTS.md)
- [action.yml](file://action.yml)
- [examples/content-builder-agent/README.md](file://examples/content-builder-agent/README.md)
- [examples/content-builder-agent/AGENTS.md](file://examples/content-builder-agent/AGENTS.md)
- [examples/content-builder-agent/subagents.yaml](file://examples/content-builder-agent/subagents.yaml)
- [examples/deep_research/README.md](file://examples/deep_research/README.md)
- [examples/deep_research/agent.py](file://examples/deep_research/agent.py)
- [examples/text-to-sql-agent/README.md](file://examples/text-to-sql-agent/README.md)
- [examples/text-to-sql-agent/agent.py](file://examples/text-to-sql-agent/agent.py)
- [examples/nvidia_deep_agent/src/agent.py](file://examples/nvidia_deep_agent/src/agent.py)
- [examples/nvidia_deep_agent/src/backend.py](file://examples/nvidia_deep_agent/src/backend.py)
</cite>

## Table of Contents
1. [Introduction](#introduction)
2. [Project Structure](#project-structure)
3. [Core Components](#core-components)
4. [Architecture Overview](#architecture-overview)
5. [Detailed Component Analysis](#detailed-component-analysis)
6. [Dependency Analysis](#dependency-analysis)
7. [Performance Considerations](#performance-considerations)
8. [Troubleshooting Guide](#troubleshooting-guide)
9. [Conclusion](#conclusion)
10. [Appendices](#appendices)

## Introduction
This document explains how to configure and customize DeepAgents, focusing on:
- Provider-agnostic model specification and smart defaults
- System prompt customization and extending base agent behavior
- Middleware customization and skills/memory loading for context injection
- Backend configuration and execution environment support
- Environment variables, configuration precedence, and common patterns
- Troubleshooting configuration issues

DeepAgents is a batteries-included agent harness built on LangGraph, designed to be ready-to-run with sensible defaults while remaining highly customizable.

## Project Structure
The repository is a monorepo with multiple independently versioned packages under libs/. The primary SDK package is deepagents, with supporting libraries for CLI, Agent Context Protocol (ACP), evaluation, and partner integrations. Examples demonstrate practical configuration patterns across domains.

```mermaid
graph TB
A["Repository Root"] --> B["libs/deepagents<br/>SDK"]
A --> C["libs/cli<br/>CLI Tool"]
A --> D["libs/acp<br/>Agent Context Protocol"]
A --> E["libs/evals<br/>Evaluation Suite"]
A --> F["libs/partners<br/>Integrations"]
A --> G["examples/<domain>-agent<br/>Usage Patterns"]
G --> G1["content-builder-agent"]
G --> G2["deep_research"]
G --> G3["text-to-sql-agent"]
G --> G4["nvidia_deep_agent"]
```

**Section sources**
- [AGENTS.md:11-23](file://AGENTS.md#L11-L23)

## Core Components
- Model configuration: Provider-agnostic model specification using provider:model format and LangChain model initialization utilities.
- Smart defaults: Built-in planning, filesystem, shell, sub-agent delegation, and context management.
- System prompts: Extendable base behavior via system_prompt parameter and domain-specific prompts.
- Middleware and skills: Tools and skills loaded per agent to inject capabilities and context.
- Backends and execution environments: LangGraph native runtime with streaming, persistence, and checkpointing support.

**Section sources**
- [README.md:24-53](file://README.md#L24-L53)
- [README.md:58-70](file://README.md#L58-L70)

## Architecture Overview
DeepAgents exposes a factory function that returns a compiled LangGraph graph. Users can customize model, tools, system prompts, and sub-agents. The system emphasizes provider-agnostic model selection and automatic capability inheritance through defaults.

```mermaid
graph TB
U["User Code"] --> F["create_deep_agent()<br/>Factory Function"]
F --> M["Model Provider<br/>provider:model"]
F --> T["Tools & Skills"]
F --> S["System Prompt"]
F --> G["Compiled LangGraph"]
G --> R["Runtime<br/>Streaming, Persistence, Checkpointing"]
```

**Section sources**
- [README.md:46-51](file://README.md#L46-L51)
- [README.md:86-88](file://README.md#L86-L88)

## Detailed Component Analysis

### Model Configuration and Provider-Agnostic Specification
- Provider-agnostic model format: Use provider:model notation to select models from various providers.
- LangChain model initialization: Leverage LangChain’s model initialization utilities to construct chat models.
- Example pattern: Passing a model configured via provider:model to the agent factory.

```mermaid
sequenceDiagram
participant Dev as "Developer"
participant Factory as "create_deep_agent()"
participant LM as "LangChain Model<br/>provider : model"
participant Graph as "Compiled Graph"
Dev->>Factory : "model=init_chat_model('provider : model')"
Factory->>LM : "Initialize model"
LM-->>Factory : "Chat model instance"
Factory-->>Dev : "Agent with selected model"
```

**Section sources**
- [README.md:63-70](file://README.md#L63-L70)

### Smart Defaults and Capability Inheritance
- Smart defaults include planning, filesystem operations, shell execution, sub-agent delegation, and context management.
- Agents inherit these capabilities automatically, enabling immediate productivity with minimal setup.

```mermaid
flowchart TD
Start(["Agent Creation"]) --> Defaults["Apply Smart Defaults<br/>Planning, FS, Shell, Sub-agents, Context"]
Defaults --> Custom["Apply User Overrides<br/>Model, Tools, Prompts"]
Custom --> Runtime["Compiled LangGraph Runtime"]
```

**Section sources**
- [README.md:26-34](file://README.md#L26-L34)

### System Prompt Customization and Extending Base Behavior
- Customize the agent’s behavior by supplying a system_prompt parameter.
- Domain-specific prompts can be integrated alongside tools and skills to tailor agent behavior.

```mermaid
sequenceDiagram
participant Dev as "Developer"
participant Factory as "create_deep_agent()"
participant Prompt as "System Prompt"
participant Graph as "Agent Graph"
Dev->>Factory : "system_prompt='Domain-specific instructions'"
Factory->>Prompt : "Set base/system behavior"
Prompt-->>Factory : "Behavior configured"
Factory-->>Dev : "Customized agent"
```

**Section sources**
- [README.md:65-70](file://README.md#L65-L70)

### Middleware Customization
- Middleware enables cross-cutting concerns such as logging, tracing, and request/response transformation.
- Add or remove middleware components by configuring the agent’s runtime pipeline or wrapping the underlying LangGraph graph.

[No sources needed since this section provides general guidance]

### Skills and Memory Loading Systems
- Skills: Domain-specific capabilities packaged as tools or modules and loaded per agent.
- Memory: Context management and summarization are part of smart defaults; memory can be extended or customized via tools and prompts.
- Sub-agents: Isolated context windows for delegated tasks, configured via YAML or programmatic definition.

```mermaid
graph LR
A["Agent Definition"] --> B["Skills<br/>Tools & Modules"]
A --> C["Memory<br/>Context & Summarization"]
A --> D["Sub-agents<br/>Isolated Context Windows"]
B --> E["Context Injection"]
C --> E
D --> E
```

**Section sources**
- [examples/content-builder-agent/README.md](file://examples/content-builder-agent/README.md)
- [examples/content-builder-agent/AGENTS.md](file://examples/content-builder-agent/AGENTS.md)
- [examples/content-builder-agent/subagents.yaml](file://examples/content-builder-agent/subagents.yaml)

### Backend Configuration and Execution Environments
- LangGraph native runtime: The agent is a compiled LangGraph graph, enabling streaming, persistence, and checkpointing.
- Execution environments: Support for web search, remote sandboxes, and human-in-the-loop workflows via CLI and integrations.

```mermaid
graph TB
A["Agent Runtime"] --> B["Streaming"]
A --> C["Persistence"]
A --> D["Checkpointing"]
A --> E["CLI Integrations<br/>Web Search, Sandboxes"]
```

**Section sources**
- [README.md:86-88](file://README.md#L86-L88)
- [README.md:74-84](file://README.md#L74-L84)

### Environment Variables and Configuration Precedence
- Provider credentials: Environment variables are used to supply API keys for model providers.
- CLI model configuration: The CLI maintains a mapping of provider names to environment variable names for credential lookup.
- Precedence: Command-line flags and environment variables override defaults; explicit parameters passed to the factory take highest precedence.

```mermaid
flowchart TD
Env["Environment Variables<br/>Provider API Keys"] --> CLI["CLI Settings"]
CLI --> Factory["create_deep_agent() Parameters"]
Factory --> Agent["Agent Configuration"]
```

**Section sources**
- [AGENTS.md:267-281](file://AGENTS.md#L267-L281)

### Examples of Common Configuration Patterns
- Content Builder Agent: Demonstrates skills, sub-agents, and YAML-based configuration.
- Deep Research Agent: Shows domain-specific tools and prompts.
- Text-to-SQL Agent: Illustrates skill-based context injection and tool usage.
- NVIDIA Deep Agent: Highlights backend-specific agent and tool configuration.

```mermaid
graph TB
CB["Content Builder Agent<br/>skills, subagents.yaml"] --> P1["Pattern: Skills + YAML"]
DR["Deep Research Agent<br/>tools + prompts"] --> P2["Pattern: Domain Tools + Prompts"]
TS["Text-to-SQL Agent<br/>skills + tools"] --> P3["Pattern: Skill-Based Context"]
NA["NVIDIA Deep Agent<br/>backend + tools"] --> P4["Pattern: Backend-Specific Config"]
```

**Section sources**
- [examples/content-builder-agent/README.md](file://examples/content-builder-agent/README.md)
- [examples/content-builder-agent/subagents.yaml](file://examples/content-builder-agent/subagents.yaml)
- [examples/deep_research/agent.py](file://examples/deep_research/agent.py)
- [examples/text-to-sql-agent/agent.py](file://examples/text-to-sql-agent/agent.py)
- [examples/nvidia_deep_agent/src/agent.py](file://examples/nvidia_deep_agent/src/agent.py)
- [examples/nvidia_deep_agent/src/backend.py](file://examples/nvidia_deep_agent/src/backend.py)

## Dependency Analysis
- The SDK depends on LangChain and LangGraph for model abstraction and graph runtime.
- Optional provider integrations are supported via LangChain adapters and environment variables.
- CLI adds convenience wrappers and credential management for model providers.

```mermaid
graph TB
SDK["deepagents SDK"] --> LC["LangChain"]
SDK --> LG["LangGraph"]
CLI["deepagents CLI"] --> SDK
CLI --> Providers["Optional Providers<br/>via LangChain Adapters"]
```

**Section sources**
- [README.md:72-72](file://README.md#L72)
- [AGENTS.md:267-281](file://AGENTS.md#L267-L281)

## Performance Considerations
- Prefer streaming for responsive user experiences.
- Use persistence and checkpointing to resume long-running sessions.
- Minimize unnecessary tool calls and optimize prompts to reduce latency.

[No sources needed since this section provides general guidance]

## Troubleshooting Guide
- Model initialization failures: Verify provider:model format and ensure the corresponding provider package is installed and environment variables are set.
- Missing tools or skills: Confirm that skills are properly loaded and tools are passed to the factory function.
- Sub-agent configuration errors: Validate YAML structure and paths referenced in subagents configuration.
- Environment variable issues: Check provider API key mappings and ensure environment variables are exported before running the agent.

**Section sources**
- [AGENTS.md:267-281](file://AGENTS.md#L267-L281)
- [examples/content-builder-agent/subagents.yaml](file://examples/content-builder-agent/subagents.yaml)

## Conclusion
DeepAgents offers a robust, provider-agnostic configuration system with smart defaults, flexible customization via system prompts and tools, and seamless integration with LangGraph’s runtime features. By leveraging environment variables, YAML configurations, and programmatic overrides, you can tailor agents to diverse domains and execution environments while maintaining simplicity and reliability.

[No sources needed since this section summarizes without analyzing specific files]

## Appendices
- Action workflow: The repository includes an action workflow for CI/CD automation.

**Section sources**
- [action.yml](file://action.yml)