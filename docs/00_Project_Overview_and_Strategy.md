# FnMCP.Penpot: Project Overview and Strategy
**Version:** 1.0  
**Date:** 2025-11-03  
**Status:** Planning Phase

---

## Executive Summary

FnMCP.Penpot is an independent implementation of a Model Context Protocol (MCP) Server and Client designed to provide a fully compliant, functional interface for interacting with Penpot, the open-source design and prototyping platform. This project distinguishes itself through its rigorous application of functional programming principles, event-driven architecture, and type-driven design methodologies.

### Core Objectives
1. **Full MCP Compliance**: Implement a complete MCP server and client adhering to the specification at https://modelcontextprotocol.io/specification/2025-06-18
2. **Penpot Integration**: Provide comprehensive interaction capabilities with the Penpot API
3. **Functional Architecture**: Utilize F# idiomatically with functional programming paradigms
4. **Event-Driven Design**: Implement Event Modeling and Event Sourcing patterns
5. **Type Safety**: Apply Type-Driven Design for robust domain modeling

---

## Architectural Philosophy

### 1. Functional Programming with F# (fsharp)

This project leverages F# as the primary language, embracing:

- **Immutability by default**: All data structures are immutable unless explicitly required otherwise
 - **Pure functions**: Side effects are isolated and made explicit
- **Composition over inheritance**: Building complex behaviors from simple, composable functions
- **Railway-oriented programming**: Using Result/Option types for error handling
- **Algebraic data types**: Modeling domains with discriminated unions and records

### 2. Event Modeling and Event Sourcing

Following **Adam Dymitruk's Event Modeling** methodology and **Martin Dilger's** teachings:

#### Event Modeling Principles

- **Events as first-class citizens**: All state changes are captured as immutable events
- **Time-based reasoning**: Understanding the system through sequences of events
- **Visual modeling**: Using event storming and modeling techniques
- **Four core patterns**:

  1. **Commands**: User intentions that trigger state changes
  2. **Events**: Facts about what happened in the system
  3. **Views/Projections**: Read models derived from events
  4. **Screens/Automation**: Reactive processes triggered by events

#### Event Sourcing Implementation

- **Event Store**: All events are persisted in chronological order
- **Aggregate Roots**: Domain entities that ensure consistency boundaries
- **Event Replay**: Ability to reconstruct state from event history
- **Temporal Queries**: Query system state at any point in time

### 3. Type-Driven Design

Following **Scott Wlaschin's** principles from "Domain Modeling Made Functional":

#### Making Illegal States Unrepresentable

- Use the type system to enforce business rules at compile time
- Model workflows as explicit pipelines of transformations
- Separate validated from unvalidated data using wrapper types

#### Domain Modeling Approach

1. **Understand the Domain**: Collaborate with domain experts to understand Penpot and MCP semantics
2. **Model the Domain in Types**: Create types that represent the domain concepts accurately
3. **Compose Workflows**: Build complete workflows from smaller, composable functions
4. **Handle Errors Explicitly**: Use Result types to make error paths visible

### 4. Documentation and Example Conventions

#### Language and Format Preferences

- **Code Examples**: All examples should favor **F#** to showcase functional programming patterns and type safety
- **Internal Configuration**: Use **YAML** for configuration examples, documentation, and internal system representations
  - YAML is more readable and maintainable for human-edited content
  - Supports comments and better visual structure
- **External System Interactions**: Use **JSON** only when documenting external system interactions
  - MCP Protocol (JSON-RPC 2.0) requires JSON
  - Penpot API uses JSON for requests/responses
  - External integrations that mandate JSON format

#### Rationale

This convention ensures:
- Documentation remains readable and maintainable
- Code examples reflect the project's functional programming philosophy
- Format choices align with their intended use cases (human vs. machine consumption)
- Clear distinction between internal representations and external protocol requirements

---

## System Architecture Overview

### High-Level Components

```
┌─────────────────────────────────────────────────────────────┐
│                    MCP Client Layer                          │
│  (Initiates connections, sends requests, handles responses)  │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ↓
┌─────────────────────────────────────────────────────────────┐
│                 MCP Protocol Handler                         │
│     (JSON-RPC 2.0, Message serialization/deserialization)   │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ↓
┌─────────────────────────────────────────────────────────────┐
│                    MCP Server Core                           │
│        (Command handlers, Event processors, Validators)      │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ↓
┌─────────────────────────────────────────────────────────────┐
│                   Domain Layer                               │
│  (Business logic, Aggregates, Domain events, Workflows)      │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ↓
┌─────────────────────────────────────────────────────────────┐
│                  Penpot API Adapter                          │
│         (HTTP client, API authentication, Rate limiting)     │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ↓
┌─────────────────────────────────────────────────────────────┐
│              Event Store / Repository API                    │
│   (External service for event persistence and retrieval)     │
└─────────────────────────────────────────────────────────────┘
```

### Component Descriptions

#### 1. MCP Client Layer
- Initiates connections to MCP servers
- Sends MCP protocol messages
- Handles responses and updates client state
- Manages connection lifecycle

#### 2. MCP Protocol Handler
- Implements JSON-RPC 2.0 protocol
- Serializes/deserializes MCP messages
- Validates protocol compliance
- Handles transport-level concerns

#### 3. MCP Server Core
- Processes incoming MCP requests
- Routes commands to appropriate handlers
- Orchestrates domain workflows
- Generates and publishes events

#### 4. Domain Layer
- Contains pure business logic
- Defines aggregates and entities
- Implements domain events
- Enforces business rules through types

#### 5. Penpot API Adapter
- Wraps Penpot HTTP API calls
- Handles authentication and authorization
- Manages rate limiting and retries
- Transforms Penpot responses to domain types

#### 6. Event Store / Repository API
- **External service** (separate project)
- Persists events in append-only fashion
- Provides event retrieval and querying
- Supports event replay and projections

---

## MCP Protocol Overview

### What is MCP?

The **Model Context Protocol (MCP)** is a protocol for communication between AI systems and external context providers. It enables:
- Standardized communication patterns
- Resource discovery and access
- Tool invocation
- Prompt management
- Sampling and completion requests

### MCP Specification Compliance (2025-06-18)

Our implementation will support:

#### Core Protocol Features
1. **Initialization**: Handshake and capability negotiation
2. **Resources**: Expose Penpot design resources (files, pages, components)
3. **Tools**: Provide operations for manipulating Penpot designs
4. **Prompts**: Offer pre-defined prompts for common Penpot tasks
5. **Sampling**: Support AI model sampling requests
6. **Logging**: Comprehensive logging of all MCP interactions

#### Transport Layers
- **stdio**: Standard input/output communication
- **HTTP with SSE**: HTTP-based transport with Server-Sent Events

#### Message Types
- **Requests**: Client-initiated messages expecting responses
- **Responses**: Server replies to requests
- **Notifications**: One-way messages that don't expect responses

---

## Penpot API Integration

### Understanding Penpot

**Penpot** is an open-source design and prototyping platform for cross-domain teams. Key concepts:

- **Files**: Top-level design documents
- **Pages**: Containers within files holding design elements
- **Shapes**: Basic design primitives (rectangles, circles, paths, text)
- **Components**: Reusable design elements
- **Libraries**: Collections of shared components
- **Prototypes**: Interactive flows between pages/frames

### API Capabilities

The Penpot API (reference: https://penpot-plugins-api-doc.pages.dev/) provides:

1. **File Management**: Create, read, update, delete design files
2. **Page Operations**: Manage pages within files
3. **Shape Manipulation**: Create and modify design elements
4. **Component System**: Define and use reusable components
5. **Export Functions**: Export designs in various formats
6. **Collaboration**: Share and collaborate on designs

### Mapping Penpot to MCP

#### Resources
- Penpot files → MCP resources
- Pages → Nested resources
- Components → Discoverable resources

#### Tools
- Create shape → MCP tool
- Modify properties → MCP tool
- Export design → MCP tool
- Component operations → MCP tools

#### Prompts
- "Create a button component" → MCP prompt
- "Generate a color palette" → MCP prompt
- "Layout elements in a grid" → MCP prompt

---

## Event Modeling Approach

### Event Model Structure

#### Command Events
Commands represent user intentions or system triggers:
- `CreateDesignFile`
- `AddShapeToPage`
- `ModifyShapeProperties`
- `ExportDesignToFormat`
- `CreateComponent`

#### Domain Events
Events represent facts that have occurred:
- `DesignFileCreated`
- `ShapeAddedToPage`
- `ShapePropertiesModified`
- `DesignExported`
- `ComponentCreated`

#### Read Models (Projections)
Views derived from event streams:
- `DesignFileView`: Current state of a design file
- `ComponentLibraryView`: Available components
- `UserActivityView`: History of user actions
- `MCPResourceView`: MCP-compatible resource listings

### Event Modeling Workflow

1. **Identify Slices**: Break down the system into vertical slices (e.g., "Create Design File" slice)
2. **Model Commands**: Define what users want to do
3. **Model Events**: Define what happens as a result
4. **Define Views**: Determine what users need to see
5. **Add Automation**: Identify reactive processes

### Example: "Create Design File" Slice

```
[User] → (CreateDesignFile Command)
           ↓
     [Command Handler]
           ↓
     [Validation & Business Rules]
           ↓
     (DesignFileCreated Event) → [Event Store]
           ↓
     [Projection Builder]
           ↓
     [DesignFileView Read Model]
           ↓
     [MCP Response] → [User]
```

---

## Repository API Design

### Purpose
A separate project will provide event storage and retrieval. This API must be:

- **Reusable**: Usable by other projects beyond FnMCP.Penpot
- **Simple**: Easy to integrate and understand
- **Scalable**: Handle high event volumes
- **Reliable**: Ensure event persistence guarantees

### Proposed Repository API Interface

```fsharp
// Core types
type EventId = EventId of Guid
type StreamId = StreamId of string
type EventNumber = EventNumber of int64
type EventMetadata = { Timestamp: DateTimeOffset; UserId: string option; CorrelationId: Guid option }

type StoredEvent<'Event> = {
    EventId: EventId
    StreamId: StreamId
    EventNumber: EventNumber
    Event: 'Event
    Metadata: EventMetadata
}

// Repository interface
type IEventRepository<'Event> = 
    abstract member AppendToStream: 
        StreamId -> 'Event list -> EventMetadata -> Async<Result<EventNumber, RepositoryError>>
    
    abstract member ReadStream: 
        StreamId -> EventNumber option -> Async<Result<StoredEvent<'Event> list, RepositoryError>>
    
    abstract member ReadAllEvents: 
        EventNumber option -> int -> Async<Result<StoredEvent<'Event> list, RepositoryError>>
    
    abstract member SubscribeToStream: 
        StreamId -> (StoredEvent<'Event> -> Async<unit>) -> Async<IDisposable>

type RepositoryError =
    | StreamNotFound of StreamId
    | ConcurrencyConflict of StreamId * ExpectedVersion: EventNumber * ActualVersion: EventNumber
    | SerializationError of string
    | ConnectionError of string
```

### Repository Features

1. **Append-Only**: Events are never modified or deleted
2. **Stream-Based**: Events grouped by aggregate/stream
3. **Optimistic Concurrency**: Version checking on append
4. **Subscription Support**: Real-time event notifications
5. **Global Event Log**: Query all events across streams

---

## Type-Driven Design Examples

### Domain Types

```fsharp
// Primitive types with validation
type DesignFileName = private DesignFileName of string
module DesignFileName =
    let create (name: string) : Result<DesignFileName, string> =
        if String.IsNullOrWhiteSpace(name) then
            Error "Design file name cannot be empty"
        elif name.Length > 255 then
            Error "Design file name too long"
        else
            Ok (DesignFileName name)
    
    let value (DesignFileName name) = name

// Making illegal states unrepresentable
type ShapeColor =
    | RGB of r: byte * g: byte * b: byte
    | RGBA of r: byte * g: byte * b: byte * a: float
    | HexColor of string

type ShapePosition = {
    X: float
    Y: float
}

type Shape =
    | Rectangle of width: float * height: float * position: ShapePosition * color: ShapeColor
    | Circle of radius: float * center: ShapePosition * color: ShapeColor
    | Text of content: string * position: ShapePosition * fontSize: int

// Workflow types
type UnvalidatedDesignFileRequest = {
    RawName: string
    RawOwner: string
}

type ValidatedDesignFileRequest = private {
    Name: DesignFileName
    Owner: UserId
}

type CreateDesignFileWorkflow = 
    UnvalidatedDesignFileRequest -> Async<Result<DesignFileId, CreateDesignFileError>>

type CreateDesignFileError =
    | ValidationError of string list
    | PenpotApiError of string
    | RepositoryError of string
```

### Workflow Implementation Pattern

```fsharp
// Railway-oriented programming
let createDesignFileWorkflow 
    (validateRequest: UnvalidatedDesignFileRequest -> Result<ValidatedDesignFileRequest, string list>)
    (callPenpotApi: ValidatedDesignFileRequest -> Async<Result<PenpotFileId, string>>)
    (storeEvent: DesignFileCreated -> Async<Result<unit, string>>)
    (request: UnvalidatedDesignFileRequest) 
    : Async<Result<DesignFileId, CreateDesignFileError>> =
    
    async {
        // Step 1: Validate
        match validateRequest request with
        | Error errors -> return Error (ValidationError errors)
        | Ok validRequest ->
            
            // Step 2: Call Penpot API
            let! penpotResult = callPenpotApi validRequest
            match penpotResult with
            | Error err -> return Error (PenpotApiError err)
            | Ok penpotFileId ->
                
                // Step 3: Create and store event
                let event = { 
                    FileId = DesignFileId.fromPenpotId penpotFileId
                    Name = validRequest.Name
                    Owner = validRequest.Owner
                    Timestamp = DateTimeOffset.UtcNow 
                }
                let! storeResult = storeEvent event
                match storeResult with
                | Error err -> return Error (RepositoryError err)
                | Ok () -> return Ok event.FileId
    }
```

---

## Project Structure (Proposed)

```
FnMCP.Penpot/
├── docs/                              # Project documentation
│   ├── 00_Project_Overview_and_Strategy.md
│   ├── 01_Event_Model.md
│   ├── 02_Domain_Model.md
│   ├── 03_MCP_Implementation.md
│   ├── 04_Penpot_Integration.md
│   └── 05_Development_Roadmap.md
│
├── src/
│   ├── FnMCP.Core/                    # Core MCP protocol implementation
│   │   ├── Protocol/                  # JSON-RPC, message types
│   │   ├── Server/                    # MCP server implementation
│   │   ├── Client/                    # MCP client implementation
│   │   └── Transport/                 # stdio, HTTP+SSE transports
│   │
│   ├── FnMCP.Domain/                  # Domain layer
│   │   ├── Types/                     # Domain types and primitives
│   │   ├── Events/                    # Domain events
│   │   ├── Commands/                  # Command definitions
│   │   ├── Aggregates/                # Aggregate roots
│   │   └── Workflows/                 # Business workflows
│   │
│   ├── FnMCP.Penpot/                  # Penpot API integration
│   │   ├── Api/                       # HTTP client, authentication
│   │   ├── Mapping/                   # Penpot types ↔ Domain types
│   │   └── Tools/                     # MCP tools for Penpot operations
│   │
│   ├── FnMCP.EventStore/              # Event sourcing infrastructure
│   │   ├── Repository/                # Repository implementation
│   │   ├── Projections/               # Event projections
│   │   └── Subscriptions/             # Event subscriptions
│   │
│   └── FnMCP.Server/                  # Runnable MCP server application
│       └── Program.fs
│
├── tests/
│   ├── FnMCP.Core.Tests/
│   ├── FnMCP.Domain.Tests/
│   ├── FnMCP.Penpot.Tests/
│   └── FnMCP.Integration.Tests/
│
├── AI_Interactions/                   # AI session logs
├── README.md
└── FnMCP.Penpot.sln
```

---

## Key Design Decisions

### 1. Language and Paradigm
- **F#** for functional programming capabilities
- **Idiomatic F#**: Use discriminated unions, pattern matching, computation expressions
- **Railway-oriented programming** for error handling

### 2. Architecture Pattern
- **Event Sourcing** as the core architectural pattern
- **CQRS** (Command Query Responsibility Segregation) for read/write separation
- **Hexagonal/Ports & Adapters** for external integrations

### 3. External Dependencies
- **Repository API**: Separate project for event storage
- **Penpot API**: External HTTP API
- **MCP Transport**: stdio or HTTP+SSE

### 4. Testing Strategy
- **Property-based testing** with FsCheck
- **Unit tests** for pure functions
- **Integration tests** for API interactions
- **Contract tests** for MCP protocol compliance

---

## Development Principles

### 1. Make Illegal States Unrepresentable
Use the type system to prevent invalid states at compile time.

### 2. Push Side Effects to the Edges
Keep the core domain pure and push IO to the boundaries.

### 3. Compose Small Functions
Build complex behaviors from simple, testable functions.

### 4. Explicit Error Handling
Use Result and Option types instead of exceptions.

### 5. Immutability by Default
All data structures are immutable unless explicitly needed.

### 6. Test-Driven Development
Write tests first to drive design decisions.

### 7. Documentation as Code
Keep documentation close to code and updated.

---

## Success Criteria

### Technical Success
1. ✅ Full MCP protocol compliance (all required features)
2. ✅ Comprehensive Penpot API coverage
3. ✅ Event sourcing implementation with complete audit trail
4. ✅ Type-safe domain model with compile-time guarantees
5. ✅ >80% test coverage
6. ✅ Performance: Handle 1000+ events/second

### Architectural Success
1. ✅ Clean separation of concerns
2. ✅ Reusable repository API
3. ✅ Extensible design for future enhancements
4. ✅ Minimal external dependencies
5. ✅ Clear, maintainable code

### Documentation Success
1. ✅ Complete API documentation
2. ✅ Comprehensive examples
3. ✅ Architecture decision records
4. ✅ Event model documentation
5. ✅ Deployment guide

---

## Risks and Mitigation

### Risk 1: MCP Specification Changes
- **Impact**: High
- **Mitigation**: Abstract protocol handling, version negotiation, backward compatibility

### Risk 2: Penpot API Instability
- **Impact**: Medium
- **Mitigation**: Anti-corruption layer, API versioning, graceful degradation

### Risk 3: Repository API Evolution
- **Impact**: Medium
- **Mitigation**: Well-defined interface contract, versioned API, migration paths

### Risk 4: Performance Issues with Event Sourcing
- **Impact**: Medium
- **Mitigation**: Snapshot strategies, caching, optimized projections

### Risk 5: Complexity of Functional Paradigm
- **Impact**: Low
- **Mitigation**: Team training, code reviews, pair programming, comprehensive examples

---

## Next Steps

### Phase 1: Foundation
1. Set up F# project structure
2. Define core domain types
3. Implement basic event model
4. Create repository API interface

### Phase 2: MCP Protocol
1. Implement JSON-RPC 2.0 handler
2. Build MCP message types
3. Create stdio transport
4. Implement basic server/client

### Phase 3: Penpot Integration
1. Penpot API client
2. Type mappings
3. Basic MCP tools for Penpot
4. Authentication and authorization

### Phase 4: Event Sourcing
1. Repository implementation
2. Event store integration
3. Projection system
4. Snapshot mechanism

### Phase 5: Complete MCP Features
1. Resources implementation
2. Prompts system
3. Sampling support
4. Logging and monitoring

### Phase 6: Testing and Documentation
1. Comprehensive test suite
2. Integration tests
3. Documentation
4. Examples and tutorials

---

## References

### MCP Protocol
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [MCP Specification 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18)

### Penpot
- [Penpot Plugins API Documentation](https://penpot-plugins-api-doc.pages.dev/)
- [Penpot Help - Plugins Getting Started](https://help.penpot.app/plugins/getting-started/)

### Event Modeling and Event Sourcing
- [Event Modeling](https://eventmodeling.org/) - Adam Dymitruk
- Martin Dilger: "Understanding Eventsourcing: Planning and Implementing Scalable Systems with Eventmodeling and Eventsourcing"
- [Event Sourcing Basics](https://martinfowler.com/eaaDev/EventSourcing.html) - Martin Fowler

### Functional Programming and F#
- [F# for Fun and Profit](https://fsharpforfunandprofit.com/) - Scott Wlaschin
- Scott Wlaschin: "Domain Modeling Made Functional"
- [F# Language Reference](https://learn.microsoft.com/en-us/dotnet/fsharp/)

### Reference Implementations (for learning only)
- [penpot/penpot-mcp](https://github.com/penpot/penpot-mcp)
- [Penpot Plugins Samples](https://github.com/penpot/penpot-plugins-samples)
- [IvanTheGeek/penpot-mcp_API_documentation](https://github.com/IvanTheGeek/penpot-mcp_API_documentation)
- [IvanTheGeek/FnPenpotMCP](https://github.com/IvanTheGeek/FnPenpotMCP)

---

## Conclusion

FnMCP.Penpot represents a sophisticated approach to building an MCP server and client for Penpot, combining modern architectural patterns with functional programming excellence. By leveraging Event Modeling, Event Sourcing, and Type-Driven Design, we create a system that is:

- **Robust**: Type safety and immutability prevent entire classes of bugs
- **Maintainable**: Pure functions and clear separation of concerns
- **Scalable**: Event sourcing enables horizontal scaling and temporal queries
- **Auditable**: Complete event history provides full audit trail
- **Extensible**: Clean architecture allows easy addition of new features

This document serves as the foundation for the project's evolution, establishing the vision, principles, and roadmap that will guide development.

---

**Document Status**: Initial Draft  
**Next Review**: After Phase 1 completion  
**Maintained By**: IvanTheGeek
