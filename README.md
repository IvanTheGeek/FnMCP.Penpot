# FnMCP.Penpot

**A Fully Compliant MCP Server and Client for Penpot**

FnMCP.Penpot is an independent implementation of the Model Context Protocol (MCP) designed to provide seamless integration with Penpot, the open-source design and prototyping platform. This project demonstrates the power of functional programming, event-driven architecture, and type-driven design.

---

## Project Status

**Current Phase:** Planning  
**Version:** 0.1.0-alpha  
**Last Updated:** 2025-11-03

---

## Overview

This project implements a complete MCP server and client that enables AI systems and other applications to interact with Penpot designs through a standardized protocol. Built with F# and leveraging functional programming principles, it showcases:

- **Full MCP Compliance**: Adheres to MCP specification 2025-06-18
- **Event Sourcing**: Complete audit trail of all operations
- **Type-Driven Design**: Compile-time guarantees preventing illegal states
- **Functional Architecture**: Pure functions, immutability, and composability

---

## Core Technologies

- **Language**: F# (.NET 8+)
- **Architecture**: Event Modeling, Event Sourcing, CQRS
- **Design Philosophy**: Type-Driven Design (Scott Wlaschin), Functional Programming
- **External Integration**: Penpot API, MCP Protocol (JSON-RPC 2.0)

---

## Documentation

Comprehensive planning and architecture documentation is available in the `docs/` directory:

### Planning Documents

1. **[Project Overview and Strategy](docs/00_Project_Overview_and_Strategy.md)**
   - Executive summary and core objectives
   - Architectural philosophy and design principles
   - System architecture overview
   - MCP and Penpot integration strategy
   - Repository API design
   - Type-Driven Design examples
   - Success criteria and risk mitigation

2. **[Event Model](docs/01_Event_Model.md)**
   - Event Modeling fundamentals
   - 12 core event slices covering system behavior
   - Event store schema and stream naming
   - Projections (read models)
   - Automation policies
   - Event versioning strategy
   - Testing strategies

3. **[Development Roadmap](docs/02_Development_Roadmap.md)**
   - 12-week implementation timeline
   - 6 development phases with detailed deliverables
   - Technical milestones
   - Risk management
   - Quality gates and success metrics
   - Technology stack specification

---

## Key Features (Planned)

### MCP Protocol Support
- ✅ Initialization and capability negotiation
- ✅ Resource discovery and access
- ✅ Tool invocation
- ✅ Prompt management
- ✅ Sampling support
- ✅ Comprehensive logging
- ✅ Multiple transport layers (stdio, HTTP+SSE)

### Penpot Integration
- ✅ File management (create, read, update, delete)
- ✅ Page operations
- ✅ Shape manipulation (add, modify, delete)
- ✅ Component system
- ✅ Export functionality
- ✅ Authentication and authorization

### Event Sourcing
- ✅ Complete event history
- ✅ Event replay and temporal queries
- ✅ Projections for read models
- ✅ Automation policies
- ✅ Audit trail

---

## Project Structure (Planned)

```
FnMCP.Penpot/
├── docs/                        # Project documentation
├── src/
│   ├── FnMCP.Core/             # Core MCP protocol implementation
│   ├── FnMCP.Domain/           # Domain layer (types, events, workflows)
│   ├── FnMCP.Penpot/           # Penpot API integration
│   ├── FnMCP.EventStore/       # Event sourcing infrastructure
│   └── FnMCP.Server/           # Runnable MCP server
├── tests/
│   ├── FnMCP.Core.Tests/
│   ├── FnMCP.Domain.Tests/
│   ├── FnMCP.Penpot.Tests/
│   └── FnMCP.Integration.Tests/
├── AI_Interactions/            # AI session logs
└── README.md
```

---

## Development Principles

### 1. Functional Programming
- Immutability by default
- Pure functions with explicit side effects
- Composition over inheritance
- Railway-oriented programming for error handling

### 2. Event Modeling
- Events as first-class citizens
- Time-based reasoning about system behavior
- Commands, Events, Views, and Automation patterns

### 3. Type-Driven Design
- Make illegal states unrepresentable
- Use the type system to enforce business rules
- Separate validated from unvalidated data

### 4. Event Sourcing
- Append-only event store
- Event replay for state reconstruction
- Temporal queries
- Complete audit trail

---

## Getting Started

### Prerequisites

- .NET 8+ SDK
- F# compiler
- Git
- (Optional) JetBrains Rider or VS Code with Ionide

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/FnMCP.Penpot.git
cd FnMCP.Penpot

# Build the solution (once implementation begins)
dotnet build

# Run tests (once implementation begins)
dotnet test
```

---

## Roadmap

### Phase 1: Foundation (Weeks 1-2)
- Project structure setup
- Core domain types
- Repository API interface

### Phase 2: MCP Protocol (Weeks 3-4)
- JSON-RPC 2.0 implementation
- MCP message types
- Transport layers

### Phase 3: Penpot Integration (Weeks 5-6)
- HTTP client and API wrappers
- Type mapping layer
- MCP tools and resources

### Phase 4: Event Sourcing (Weeks 7-8)
- Event store implementation
- Projections
- Automation policies

### Phase 5: Complete Features (Weeks 9-10)
- Prompts and sampling
- HTTP+SSE transport
- Comprehensive logging

### Phase 6: Polish (Weeks 11-12)
- Testing and documentation
- Performance optimization
- Deployment preparation

See [Development Roadmap](docs/02_Development_Roadmap.md) for detailed timeline.

---

## References

### MCP Protocol
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [MCP Specification 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18)

### Penpot
- [Penpot Plugins API Documentation](https://penpot-plugins-api-doc.pages.dev/)
- [Penpot Help](https://help.penpot.app/plugins/getting-started/)

### Learning Resources
- [F# for Fun and Profit](https://fsharpforfunandprofit.com/) - Scott Wlaschin
- [Event Modeling](https://eventmodeling.org/) - Adam Dymitruk
- Scott Wlaschin: "Domain Modeling Made Functional"
- Martin Dilger: "Understanding Eventsourcing"

---

## Contributing

This project is currently in the planning phase. Contribution guidelines will be established once implementation begins.

---

## License

See [LICENSE](LICENSE) file for details.

---

## Project Team

This is an independent implementation of MCP for Penpot, developed with a focus on functional programming excellence and architectural best practices.

---

**Note**: This project is currently in the planning phase. Implementation will begin following the roadmap outlined in the documentation.