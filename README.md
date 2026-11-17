# Node Translator

**An AI-powered agent for creating and editing native Blender node graphs through natural language.**

Node Translator aims to make procedural workflows in Blender more accessible by allowing users to describe what they want in plain English and have an AI agent plan, construct, validate, and modify the corresponding node graphs.

The goal is not simply to generate scripts or select from predefined effects. The node graph itself is the primary output: native, editable, inspectable, and reusable inside Blender.

---

## Project Vision

Node Translator is being designed as a modular AI agent that translates natural-language instructions into structured node graphs.

Instead of requiring users to manually understand every node, socket, connection, and parameter, the agent will interpret the requested result and construct the corresponding graph using controlled operations.

The long-term vision is to support multiple Blender node systems through a shared architecture.

## Initial Scope

The first implementation will focus on **Blender Geometry Nodes**.

The initial development priorities are:

- Natural-language input in English.
- Understanding procedural modeling instructions.
- Planning node graphs from user requests.
- Discovering available nodes and their sockets.
- Generating a structured graph representation.
- Validating nodes, sockets, connections, and parameters.
- Building native Geometry Nodes graphs in Blender.
- Reading and modifying existing node graphs.
- Supporting iterative changes through follow-up instructions.

## Example Workflows

A user should eventually be able to request operations such as:

**Scatter Objects**

> Scatter 100 rocks across the selected surface with random scale and rotation.

**Slope-Based Distribution**

> Distribute points on a sloped surface and adjust object scale according to the surface slope.

**Modify an Existing Graph**

> Double the number of rocks and make them slightly smaller.

These examples represent intended capabilities, not features that are already implemented.

## Architecture Overview

The planned architecture consists of the following components:

1. **AI Agent** — Interprets natural-language requests and plans graph operations.
2. **Universal Graph IR** — Represents node graphs in a structured, domain-independent format.
3. **Domain Adapters** — Translate the universal representation into Blender-specific node operations.
4. **Graph Validator** — Checks node types, sockets, links, parameters, and graph consistency.
5. **Controlled Graph Builder** — Constructs and modifies graphs using predefined, trusted operations.
6. **Blender Bridge** — Communicates with Blender and applies validated graph changes.
7. **MCP Integration** — Provides an interface between the agent and Blender.

Geometry Nodes will be the first supported domain. The architecture is intended to allow future extensions for Shader Nodes and Compositor Nodes.

## Safety and Design Principles

Node Translator is being designed around several important principles:

- **Structured Output:** The AI should produce structured graph instructions rather than arbitrary executable Python code.
- **Controlled Execution:** Blender operations should be performed through trusted, predefined functions.
- **Validation First:** Graph instructions must be validated before they are applied.
- **Native Graphs:** Generated results must remain editable inside Blender.
- **Modularity:** Core graph logic should remain separate from Blender-specific implementation details.
- **Transparency:** Users should be able to inspect the generated graph and understand its structure.
- **Recoverability:** Failed operations should not silently corrupt existing graphs.

## Development Roadmap

The project is organized into twelve development stages:

1. Research and Product Scope
2. Technical Architecture and System Design
3. Natural Language Understanding and Graph Planning
4. Universal Graph Intermediate Representation
5. Graph Construction Engine
6. Node Discovery and Reusable Node Library
7. Validation and Security
8. AI and MCP Integration
9. Feedback and Self-Correction
10. VS Code User Experience
11. Testing and Optimization
12. Documentation and Public Release

Each stage will be designed, implemented, tested, and reviewed before moving to the next.

## Current Development Status

**Project Stage:** Initial Specification Completed

**Current Focus:** Technical Architecture and System Design

**Initial Target Domain:** Blender Geometry Nodes

**Initial Interface:** Visual Studio Code

**Initial Language:** English

The project is currently in its early development and architecture-planning phase. The features described above are goals and planned capabilities, not claims of an existing working implementation.

## Long-Term Vision

The long-term goal is to develop a general-purpose, extensible AI agent capable of understanding complex procedural instructions and translating them into reliable, editable node graphs.

Node Translator aims to make node-based workflows more intuitive without hiding the underlying graph structure from the user.

---

*Node Translator — Turning natural language into editable node graphs.*
