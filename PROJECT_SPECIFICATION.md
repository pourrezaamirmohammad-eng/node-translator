# Node Translator

## Project Specification

**Document version:** 0.1.0\
**Status:** Initial product specification\
**Project type:** AI-powered Blender node-graph engineering agent\
**Initial interface:** Visual Studio Code (VS Code)\
**Initial language:** English\
**Initial node domain:** Blender Geometry Nodes\
**Architecture direction:** Universal core with domain-specific adapters

------------------------------------------------------------------------

## 1. Project Overview

Node Translator is an AI-powered agent that translates natural-language
instructions into native, editable node graphs inside Blender.

The user describes a desired procedural result in natural language. Node
Translator interprets the request, plans the required graph, validates
that plan against Blender's available node and socket definitions, and
constructs the graph through a controlled integration with Blender.

The primary deliverable is the node graph itself---not a Python script
presented to the user, a rendered image, or a selection from a fixed
collection of predefined effects.

The generated graph must remain native to Blender so that users can
inspect it, edit it, extend it, and learn from its structure.

### 1.1 Product Vision

Make procedural node workflows in Blender accessible through natural
language while preserving the transparency, editability, and control of
native node systems.

### 1.2 Core Principle

**The AI plans the graph; trusted software constructs it.**

The AI must produce structured graph data and approved operations. It
must not generate arbitrary Python code for execution inside Blender.

A trusted, prewritten builder may use the Blender Python API to create
and modify nodes, sockets, links, node groups, and modifiers. Its
operations must be constrained by explicit validation and allowlists.

------------------------------------------------------------------------

## 2. Problem Statement

Creating procedural effects in Blender often requires users to
understand node systems, node types, socket compatibility, data flow,
and domain-specific workflows.

This creates a barrier for: - Beginners who understand the result they
want but do not know how to build the graph. - Intermediate users who
know node concepts but need to construct complex graphs more
efficiently. - Experienced users who want to prototype, inspect, and
modify procedural systems through a faster interface.

Node Translator aims to reduce this barrier without hiding the
underlying graph or removing the user's ability to edit it.

------------------------------------------------------------------------

## 3. Product Goals

Node Translator should:

1.  Translate English natural-language requests into structured graph
    plans.
2.  Create native Blender node graphs rather than relying on generated
    scripts as the main output.
3.  Discover available node types, sockets, properties, and relevant
    capabilities from the running Blender environment.
4.  Validate graph plans before applying changes.
5.  Construct Geometry Nodes graphs using controlled, trusted builder
    operations.
6.  Apply generated Geometry Nodes modifiers to the selected Blender
    object when appropriate.
7.  Inspect existing graphs and use their structure as context for
    follow-up requests.
8.  Modify existing graphs in response to natural-language instructions.
9.  Explain what was created, what changed, and why an operation failed.
10. Preserve user control and avoid silently damaging existing work.

------------------------------------------------------------------------

## 4. Initial Product Decisions

The following decisions define the initial direction of the project.

  -----------------------------------------------------------------------
  Area                                Decision
  ----------------------------------- -----------------------------------
  Architecture                        Hybrid: universal core plus
                                      domain-specific adapters

  Initial node domain                 Geometry Nodes

  Initial user interface              VS Code

  Initial prompt language             English

  Blender integration                 Controlled Blender-side bridge

  AI output                           Structured graph representation and
                                      approved operations

  Graph construction                  Trusted builder using the Blender
                                      API

  Future domains                      Shader Nodes and Compositor Nodes,
                                      after the Geometry Nodes MVP
  -----------------------------------------------------------------------

The architecture should be designed to support multiple node domains,
but the first implementation must focus on Geometry Nodes. Supporting
every domain in the MVP would make the initial scope unnecessarily
large.

------------------------------------------------------------------------

## 5. MVP Scope

The Minimum Viable Product (MVP) is the first usable version of Node
Translator.

### 5.1 Included in the MVP

-   Accept English natural-language instructions from a VS Code
    workflow.
-   Interpret the requested procedural behavior and identify relevant
    parameters.
-   Discover relevant Geometry Nodes capabilities from the running
    Blender instance.
-   Produce a structured, machine-readable graph plan.
-   Validate node types, socket references, links, properties, and
    required inputs.
-   Create a Geometry Nodes node group.
-   Create nodes, set supported values, connect sockets, and arrange
    nodes.
-   Apply the generated node group to the selected object through a
    Geometry Nodes modifier when requested and valid.
-   Inspect an existing Geometry Nodes graph at a useful level of
    detail.
-   Apply follow-up changes to an existing graph.
-   Return clear explanations and actionable errors.
-   Provide basic protection against failed or invalid graph updates.

### 5.2 Explicitly Out of Scope for the MVP

-   Full support for every Blender node.
-   Shader Nodes and Compositor Nodes.
-   Arbitrary complex physics or simulation workflows.
-   Advanced time-dependent simulation systems and all associated cache
    or bake workflows.
-   Guaranteed success for every natural-language request.
-   Fully autonomous repair of every possible graph error.
-   Support for every Blender version from the first release.
-   Execution of arbitrary AI-generated Python code.

These exclusions are scope boundaries, not statements that the
capabilities will never be considered.

------------------------------------------------------------------------

## 6. Initial Acceptance Scenarios

The first MVP should be tested against practical requests that
demonstrate graph creation and iterative editing.

### Scenario A --- Procedural Scattering

**User request:**

> Scatter 100 rocks across the selected surface with random scale and
> rotation.

**Expected behavior:** - Interpret the request as a Geometry Nodes
scattering workflow. - Identify the relevant input geometry and
distribution approach. - Plan the required nodes and connections. -
Represent the requested count and randomization parameters. - Validate
the graph before construction. - Build a native, editable Geometry Nodes
graph. - Report the resulting graph and any assumptions.

### Scenario B --- Slope-Based Distribution

**User request:**

> Distribute points on a sloped surface and adjust object scale
> according to the surface slope.

**Expected behavior:** - Identify that the request requires surface
distribution and slope-dependent behavior. - Plan a graph that derives
or evaluates an appropriate slope-related value. - Connect that value to
the scale behavior. - Validate node and socket compatibility. - Build
the graph and explain the key parts.

### Scenario C --- Follow-Up Modification

**User request:**

> Double the number of rocks and make them slightly smaller.

**Expected behavior:** - Inspect the existing graph and identify the
relevant distribution-count and scale controls. - Modify the existing
graph rather than unnecessarily rebuilding it. - Preserve unrelated
nodes and links. - Report the changes made.

The exact node choices for these scenarios must be determined through
runtime capability discovery and validation, rather than assumed to be
permanently available across all Blender versions.

------------------------------------------------------------------------

## 7. High-Level Architecture

The system is organized into distinct components. The AI planning layer
must remain separate from the trusted graph-construction layer.

### 7.1 AI Agent

Responsibilities: - Interpret the user's natural-language request. -
Identify intent, target objects, operations, parameters, and
constraints. - Request graph context or capability information when
needed. - Produce a structured graph plan. - Explain results and
errors. - Propose modifications to an existing graph.

The AI Agent does not directly execute arbitrary code inside Blender.

### 7.2 Universal Graph Intermediate Representation (Graph IR)

Graph IR is the structured, domain-independent representation used to
describe a graph plan.

It should represent concepts such as: - Graph identity and domain. -
Nodes and their logical identifiers. - Node types. - Node properties and
values. - Socket references. - Links between sockets. - Group inputs and
outputs. - Optional layout metadata. - Version and compatibility
information.

The IR must not assume that all node domains share identical node or
socket semantics. Domain-specific details should be represented through
explicit adapter contracts or validated metadata.

The initial implementation will use JSON-compatible data and a formal
schema. The exact schema will be defined in a later technical-design
phase.

### 7.3 Domain Adapters

Domain adapters translate between the universal Graph IR and the actual
node systems exposed by Blender.

Initial adapter: - Geometry Nodes adapter.

Potential future adapters: - Shader Nodes adapter. - Compositor Nodes
adapter.

Each adapter is responsible for domain-specific node discovery, socket
interpretation, compatibility rules, and supported operations.

### 7.4 Runtime Capability Discovery

Node Translator must inspect the running Blender environment to discover
relevant capabilities, including: - Available node types. - Node
identifiers. - Input and output sockets. - Socket types and
identifiers. - Supported node properties. - Relevant version-dependent
differences.

The system should prefer runtime discovery over relying exclusively on a
hard-coded list of nodes and sockets.

### 7.5 Graph Validator

The validator checks a proposed graph before it is applied.

Validation should cover: - Whether referenced node types exist. -
Whether referenced sockets exist on the specified nodes. - Whether links
are compatible with the available socket types and rules. - Whether
required inputs and properties are supplied. - Whether property values
are valid. - Whether references and identifiers are consistent. -
Whether the requested operations are permitted. - Whether the graph plan
is compatible with the active Blender environment.

Validation must return structured errors and warnings that the AI Agent
can use to revise the plan.

### 7.6 Controlled Graph Builder

The builder is trusted software that applies validated graph operations
through the Blender API.

Its responsibilities include: - Creating node groups. - Creating
approved node types. - Setting validated properties and values. -
Creating links. - Configuring group interfaces. - Arranging nodes. -
Applying node groups through supported Blender objects and modifiers. -
Reporting operation results.

The builder must expose a limited set of explicit operations. It must
not accept arbitrary Python source from the AI Agent.

### 7.7 Blender Bridge

The Blender Bridge provides controlled communication with the running
Blender instance.

It is responsible for: - Receiving approved requests. - Inspecting the
active Blender context when permitted. - Returning graph and capability
information. - Passing validated operations to the builder. - Returning
structured results and errors.

The precise transport and process arrangement will be specified during
the technical architecture phase.

### 7.8 MCP Integration

The initial workflow is intended to connect VS Code to Node Translator
through an MCP-compatible interface.

MCP should be treated as an integration and communication layer---not as
the core graph-planning or graph-building engine.

Keeping the core independent of MCP will make it easier to support
additional interfaces in the future.

### 7.9 VS Code Interface

VS Code is the initial user-facing environment.

The first workflow should allow users to: - Submit natural-language
instructions. - Review the agent's interpretation or plan when
appropriate. - Approve operations that require confirmation. - Receive
execution results and error explanations. - Continue with follow-up
instructions.

The exact extension, client, and MCP server responsibilities will be
defined in the technical architecture phase.

------------------------------------------------------------------------

## 8. Core Execution Workflow

The intended high-level workflow is:

1.  The user submits a natural-language request in VS Code.
2.  The AI Agent interprets the request and identifies missing context.
3.  The agent requests relevant Blender graph context or runtime
    capabilities.
4.  The Blender integration returns the requested information.
5.  The agent produces a structured Graph IR plan.
6.  The domain adapter and validator check the plan against the active
    Blender environment.
7.  If validation fails, structured errors are returned for correction.
8.  If validation succeeds, the controlled builder applies the approved
    operations.
9.  Blender returns the execution result and updated graph information.
10. The agent explains the result and remains ready for follow-up
    modifications.

This workflow is a target design. Its exact message formats, process
boundaries, and transport mechanisms remain to be finalized.

------------------------------------------------------------------------

## 9. Safety, Security, and User Control

Security is a foundational requirement.

### 9.1 No Arbitrary AI-Generated Code Execution

The AI Agent must not submit arbitrary Python code for execution in
Blender.

AI output must be constrained to: - Structured Graph IR. - Explicitly
defined operation requests. - Validated values and references.

### 9.2 Allowlisted Operations

The Blender-side builder must expose a defined set of supported
operations. Unknown or unapproved operations must be rejected.

### 9.3 Validation Before Mutation

Graph plans must be validated before changes are applied.

The builder must not trust AI output merely because it is syntactically
valid JSON.

### 9.4 Destructive Changes

Operations that may delete, replace, or substantially alter existing
user work should have clear safeguards. The exact confirmation policy
will be defined during technical design.

### 9.5 Failure Handling and Recovery

A failed operation should produce a structured error. The system should
avoid leaving the graph in an unknown or partially modified state.

The implementation should investigate transactional updates, snapshots,
rollback, or another suitable recovery strategy.

### 9.6 Resource and Context Limits

The system should define limits for graph size, operation count,
execution time, and access to Blender data. It should operate only on
the data and context required for the requested task.

------------------------------------------------------------------------

## 10. Compatibility and Version Strategy

Blender's node APIs and node behavior can vary between versions.

Therefore: - The MVP must explicitly select and document a supported
Blender baseline. - Runtime capability discovery must be used where
practical. - Version-specific behavior should be isolated in adapters or
compatibility modules. - Unsupported versions or capabilities must
produce clear errors. - Compatibility must be tested rather than
assumed.

The exact Blender baseline and support policy are open technical
decisions for the architecture phase.

------------------------------------------------------------------------

## 11. Initial Technology Direction

The following technologies are the current implementation direction, not
an irreversible commitment to every library or provider.

  Area                           Initial direction
  ------------------------------ -----------------------------
  Main implementation language   Python
  Blender integration            Blender Python API
  User workflow                  VS Code
  Integration protocol           MCP
  Structured graph data          JSON-compatible Graph IR
  Schema validation              JSON Schema and/or Pydantic
  Automated tests                pytest
  Version control                Git
  Public repository              GitHub

The AI model provider should remain abstracted behind a provider
interface where practical. This avoids coupling the graph engine to a
single model vendor.

Specific dependencies and versions will be selected during
implementation planning.

------------------------------------------------------------------------

## 12. Testing Strategy

Testing should be divided into layers.

### 12.1 Unit Tests

Test independent components without requiring a running Blender UI,
including: - Graph IR parsing. - Schema validation. - Identifier and
reference checks. - Planning constraints. - Operation allowlists. -
Error formatting.

### 12.2 Blender Integration Tests

Run tests against a supported Blender environment to verify: - Runtime
node discovery. - Node creation. - Socket lookup. - Link creation. -
Property assignment. - Node group interfaces. - Modifier application. -
Graph inspection and modification.

Where practical, Blender background mode should be used for repeatable
automated integration tests.

### 12.3 End-to-End Tests

Test the complete workflow from a VS Code request through planning,
validation, Blender execution, and result reporting.

The three acceptance scenarios in Section 6 should form the initial
end-to-end test set.

------------------------------------------------------------------------

## 13. Repository and Documentation Direction

The public repository should keep product documentation, architecture
decisions, source code, and tests organized and understandable.

The initial repository is expected to grow toward a structure similar
to:

``` text
node-translator/
├── README.md
├── PROJECT_SPECIFICATION.md
├── ARCHITECTURE.md
├── TECHNICAL_DECISIONS.md
├── CONTRIBUTING.md
├── LICENSE
├── .gitignore
├── docs/
├── src/
├── tests/
└── examples/
```

This is a proposed future structure, not a requirement to create every
file immediately.

The license file must be added only after the project's licensing
strategy and dependency compatibility have been reviewed.

------------------------------------------------------------------------

## 14. Development Roadmap

Development will proceed in explicit phases. Each phase should have
defined deliverables and acceptance criteria. The project should not
move to the next phase until the current phase has been reviewed.

1.  **Research and Product Scope** --- define the product vision, MVP
    boundaries, initial scenarios, and core principles.
2.  **Technical Architecture and System Design** --- define process
    boundaries, component contracts, Blender communication, version
    baseline, security boundaries, and repository architecture.
3.  **Natural-Language Understanding and Graph Planning** --- define how
    user requests become structured plans.
4.  **Universal Graph IR Specification** --- define the formal graph
    representation and schema.
5.  **Graph Construction Engine** --- implement controlled graph
    operations.
6.  **Node Discovery and Reusable Node Library** --- discover runtime
    capabilities and build reusable, validated graph patterns.
7.  **Validation and Security** --- implement graph validation,
    permissions, safeguards, and recovery.
8.  **AI and MCP Integration** --- connect the planning layer to the
    Blender integration through the selected interface.
9.  **Feedback and Self-Correction** --- support graph inspection,
    error-driven correction, and iterative editing.
10. **VS Code User Experience** --- refine the user workflow and
    interaction.
11. **Testing and Optimization** --- improve reliability, performance,
    and test coverage.
12. **Documentation and Public Release** --- prepare installation
    instructions, examples, contribution guidance, licensing, and
    release materials.

### Phase 1 Status

**Research and Product Scope: Completed as the initial project
definition.**

### Next Phase

**Phase 2 --- Technical Architecture and System Design.**

Phase 2 must produce a reviewed architecture document and a record of
technical decisions before implementation begins.

------------------------------------------------------------------------

## 15. Initial Limitations and Open Decisions

The following decisions remain open and must not be treated as
finalized:

-   Exact supported Blender version.
-   Exact Blender Bridge process and communication transport.
-   Exact MCP server/client arrangement.
-   Final Graph IR schema.
-   Model provider and model selection.
-   Confirmation rules for destructive operations.
-   Rollback and transaction implementation.
-   Final package structure and dependency versions.
-   Project license and contribution policy.

These topics are intentionally deferred to the relevant design phases.

------------------------------------------------------------------------

## 16. MVP Definition of Done

The MVP will be considered ready for an initial public release when it
can:

1.  Receive an English natural-language request through the intended VS
    Code workflow.
2.  Interpret a supported Geometry Nodes task.
3.  Obtain relevant capabilities from a supported Blender instance.
4.  Produce a valid structured graph plan.
5.  Validate node types, sockets, links, and values.
6.  Construct a native, editable Geometry Nodes graph.
7.  Apply the graph to the intended object when requested.
8.  Inspect and modify an existing supported graph through follow-up
    instructions.
9.  Report failures clearly and protect existing work against unsafe
    partial changes.
10. Pass the defined unit, Blender integration, and end-to-end
    acceptance tests.

------------------------------------------------------------------------

## 17. Long-Term Direction

After the Geometry Nodes MVP is stable, Node Translator may expand
toward: - Shader Nodes. - Compositor Nodes. - More advanced procedural
workflows. - Larger graph understanding and targeted subgraph editing. -
Reusable graph components and templates. - Additional user interfaces
and integrations. - More languages, including Persian.

These are long-term directions, not MVP commitments.

------------------------------------------------------------------------

## 18. Project Principles

The project will be guided by the following principles:

1.  **Native graphs first:** the editable Blender node graph is the
    primary deliverable.
2.  **Structured planning:** AI output must be represented in a defined,
    inspectable format.
3.  **Controlled execution:** trusted software performs Blender
    mutations through approved operations.
4.  **Validation before construction:** graph plans must be checked
    against the active environment.
5.  **Modular architecture:** universal graph concepts must be separated
    from domain-specific behavior.
6.  **Incremental development:** build, test, document, and review one
    phase at a time.
7.  **User control:** users should be able to inspect results and
    understand important changes.
8.  **Honest capability boundaries:** unsupported nodes, versions, or
    workflows must be reported clearly.

------------------------------------------------------------------------

**End of Project Specification --- Version 0.1.0**
