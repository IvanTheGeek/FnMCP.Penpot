# Development Roadmap
**Version:** 1.0  
**Date:** 2025-11-03  
**Status:** Planning Phase

---

## Overview

This document outlines the development roadmap for FnMCP.Penpot, detailing phases, milestones, deliverables, and success criteria for each stage of implementation.

---

## Project Timeline

### Total Estimated Duration: 12 Weeks

- **Phase 1**: Foundation
- **Phase 2**: MCP Protocol Implementation
- **Phase 3**: Penpot Integration
- **Phase 4**: Event Sourcing Infrastructure
- **Phase 5**: Complete MCP Features
- **Phase 6**: Testing, Documentation, and Polish

---

## Phase 1: Foundation

### Goals
Establish the project foundation with core types, domain model, and repository interface.

### Deliverables

#### Week 1
1. **Project Structure Setup**
   - Create F# solution file
   - Set up all project modules (FnMCP.Core, FnMCP.Domain, FnMCP.Penpot, FnMCP.EventStore, FnMCP.Server)
   - Configure build system and dependencies
   - Set up testing framework (Expecto or xUnit)
   - Configure property-based testing with FsCheck

2. **Core Domain Types**
   - Define primitive types with validation (DesignFileName, UserId, etc.)
   - Implement single-case discriminated unions for type safety
   - Create Result and Option helper modules
   - Build error type hierarchy

3. **Event Model Foundation**
   - Define base event types and metadata
   - Create EventId, StreamId, EventNumber types
   - Implement event serialization/deserialization
   - Build event type registry

#### Week 2
4. **Repository API Interface**
   - Define IEventRepository interface
   - Create RepositoryError types
   - Implement in-memory repository for testing
   - Build repository unit tests

5. **Basic Domain Events**
   - Implement first set of domain events (server lifecycle, connections)
   - Create event builders and validators
   - Build event evolution/upcasting infrastructure

6. **Documentation**
   - Update architecture documentation
   - Create API documentation templates
   - Document core types and their invariants

### Success Criteria
- ✅ All project modules compile successfully
- ✅ Core types prevent illegal states at compile time
- ✅ Repository interface is well-defined and testable
- ✅ Event serialization round-trips correctly
- ✅ >90% test coverage for domain types

### Dependencies
- .NET 8+ SDK
- F# compiler
- NuGet packages: FSharp.Core, Expecto/xUnit, FsCheck

---

## Phase 2: MCP Protocol Implementation (Weeks 3-4)

### Goals
Implement full MCP protocol support including JSON-RPC 2.0, message handling, and transport layers.

### Deliverables

#### Week 3
1. **JSON-RPC 2.0 Implementation**
   - Define JSON-RPC message types (Request, Response, Notification)
   - Implement message serialization using System.Text.Json
   - Build request/response correlation
   - Create error response handling

2. **MCP Message Types**
   - Define all MCP protocol messages per specification
   - Implement initialization handshake
   - Create capability negotiation types
   - Build message validation

3. **stdio Transport**
   - Implement standard input/output transport
   - Create message framing and parsing
   - Build async message pump
   - Handle transport errors and reconnection

#### Week 4
4. **MCP Server Core**
   - Implement server initialization
   - Create request routing and dispatching
   - Build command handler infrastructure
   - Implement server lifecycle management

5. **MCP Client Implementation**
   - Create client connection logic
   - Implement request sending
   - Build response handling
   - Create client-side connection management

6. **Protocol Testing**
   - Build MCP protocol compliance tests
   - Create integration tests for client-server communication
   - Test capability negotiation scenarios
   - Verify error handling paths

### Success Criteria
- ✅ Full JSON-RPC 2.0 compliance
- ✅ Successful MCP handshake between client and server
- ✅ All required MCP message types implemented
- ✅ stdio transport working reliably
- ✅ Protocol compliance test suite passing

### Dependencies
- System.Text.Json
- MCP specification (2025-06-18)

---

## Phase 3: Penpot Integration (Weeks 5-6)

### Goals
Integrate with Penpot API, map Penpot types to domain types, and implement basic MCP tools.

### Deliverables

#### Week 5
1. **Penpot HTTP Client**
   - Create HTTP client with authentication
   - Implement API token handling
   - Build rate limiting and retry logic
   - Create connection pooling

2. **Penpot API Wrappers**
   - Wrap file management endpoints
   - Wrap page operations endpoints
   - Wrap shape manipulation endpoints
   - Implement error translation to domain errors

3. **Type Mapping Layer**
   - Map Penpot file types to domain types
   - Map Penpot shape types to domain types
   - Create conversion functions with validation
   - Handle API version differences

#### Week 6
4. **MCP Tools for Penpot**
   - Implement "Create Design File" tool
   - Implement "Add Shape to Page" tool
   - Implement "Modify Shape Properties" tool
   - Implement "Export Design" tool

5. **MCP Resources for Penpot**
   - Expose Penpot files as MCP resources
   - Expose pages as nested resources
   - Implement resource URI scheme
   - Build resource content retrieval

6. **Integration Testing**
   - Test against real Penpot API (or mock)
   - Verify type conversions
   - Test error scenarios
   - Validate API rate limiting

### Success Criteria
- ✅ Successful authentication with Penpot API
- ✅ All basic CRUD operations working
- ✅ Type-safe conversion between Penpot and domain types
- ✅ MCP tools callable and functional
- ✅ Resources discoverable via MCP

### Dependencies
- Penpot API access (test account)
- HTTP client library
- Penpot API documentation

---

## Phase 4: Event Sourcing Infrastructure (Weeks 7-8)

### Goals
Implement event store, projections, and automation policies.

### Deliverables

#### Week 7
1. **Event Store Implementation**
   - Implement persistent event store (file-based or embedded DB)
   - Create stream-based event organization
   - Implement optimistic concurrency control
   - Build event append and read operations

2. **Projection Infrastructure**
   - Create projection builder framework
   - Implement projection update mechanism
   - Build projection state management
   - Create projection rebuild capability

3. **Core Projections**
   - Implement ServerStatusView
   - Implement ActiveConnectionsView
   - Implement PenpotResourceCatalogView
   - Implement FileContentView

#### Week 8
4. **Event Subscription System**
   - Create event subscription infrastructure
   - Implement subscription management
   - Build event broadcasting
   - Handle subscription errors

5. **Automation Policies**
   - Implement TokenRefreshPolicy
   - Implement ResourceCatalogUpdatePolicy
   - Implement ConnectionTimeoutPolicy
   - Implement CacheInvalidationPolicy

6. **Event Store Testing**
   - Test event persistence and retrieval
   - Test concurrent writes
   - Test projection rebuilding
   - Test subscription reliability

### Success Criteria
- ✅ Events persisted reliably
- ✅ Projections update correctly from events
- ✅ Subscriptions deliver events reliably
- ✅ Automation policies trigger correctly
- ✅ Concurrency handled properly

### Dependencies
- Storage mechanism (file system or embedded DB like LiteDB)
- Event serialization library

---

## Phase 5: Complete MCP Features (Weeks 9-10)

### Goals
Implement remaining MCP features: prompts, sampling, logging, and HTTP+SSE transport.

### Deliverables

#### Week 9
1. **MCP Prompts System**
   - Define prompt templates
   - Implement prompt discovery
   - Create prompt execution engine
   - Build prompt argument validation

2. **MCP Sampling Support**
   - Implement sampling request handling
   - Create sampling result types
   - Build sampling integration points
   - Test sampling workflows

3. **HTTP+SSE Transport**
   - Implement HTTP server
   - Create Server-Sent Events handler
   - Build HTTP request routing
   - Implement CORS and security headers

#### Week 10
4. **Comprehensive Logging**
   - Implement structured logging
   - Create log levels and filtering
   - Build log aggregation
   - Integrate with MCP logging protocol

5. **Additional MCP Tools**
   - Implement "Create Component" tool
   - Implement "List Resources" tool
   - Implement "Search Designs" tool
   - Add any remaining Penpot operations

6. **Error Handling and Resilience**
   - Implement circuit breakers
   - Add retry policies
   - Build fallback mechanisms
   - Create error reporting

### Success Criteria
- ✅ All MCP features implemented per specification
- ✅ HTTP+SSE transport working
- ✅ Prompts discoverable and executable
- ✅ Comprehensive logging in place
- ✅ System resilient to failures

### Dependencies
- HTTP server library (Giraffe or Suave)
- SSE library
- Logging library (Serilog)

---

## Phase 6: Testing, Documentation, and Polish (Weeks 11-12)

### Goals
Comprehensive testing, complete documentation, performance optimization, and deployment preparation.

### Deliverables

#### Week 11
1. **Comprehensive Test Suite**
   - Complete unit test coverage (>80%)
   - Build integration test suite
   - Create end-to-end test scenarios
   - Implement property-based tests for core logic

2. **Performance Testing**
   - Load testing with multiple clients
   - Event throughput testing
   - Memory profiling
   - Identify and fix bottlenecks

3. **Security Audit**
   - Review authentication handling
   - Audit token storage
   - Check input validation
   - Review error message information disclosure

#### Week 12
4. **Complete Documentation**
   - API reference documentation
   - User guide and tutorials
   - Deployment guide
   - Troubleshooting guide
   - Architecture decision records

5. **Examples and Samples**
   - Create sample client applications
   - Build example workflows
   - Create demo scripts
   - Record demonstration videos

6. **Deployment Preparation**
   - Create Docker container
   - Build deployment scripts
   - Write configuration documentation
   - Create monitoring dashboard templates

### Success Criteria
- ✅ >80% test coverage
- ✅ All documentation complete and reviewed
- ✅ Performance meets requirements (1000+ events/sec)
- ✅ Security audit passed
- ✅ Ready for production deployment

### Dependencies
- Docker
- CI/CD pipeline tools
- Documentation hosting

---

## Technical Milestones

### Milestone 1: "Hello MCP" (End of Week 2)
- Basic server starts and accepts connections
- Simple echo-style MCP interaction working
- Core types and repository interface defined

### Milestone 2: "Protocol Complete" (End of Week 4)
- Full MCP protocol implemented
- Client-server communication working
- All message types supported

### Milestone 3: "Penpot Connected" (End of Week 6)
- Integration with Penpot API working
- Basic design operations functional via MCP tools
- Resources discoverable

### Milestone 4: "Event Sourced" (End of Week 8)
- All operations stored as events
- Projections building correctly
- Automation policies active

### Milestone 5: "Feature Complete" (End of Week 10)
- All MCP features implemented
- All planned Penpot operations supported
- System resilient and reliable

### Milestone 6: "Production Ready" (End of Week 12)
- Fully tested and documented
- Deployed and monitored
- Ready for real-world use

---

## Risk Management

### Technical Risks

#### Risk 1: MCP Specification Complexity
**Probability**: Medium  
**Impact**: High  
**Mitigation**:
- Study specification thoroughly before coding
- Build compliance test suite early
- Iterate on implementation with tests

#### Risk 2: Penpot API Limitations
**Probability**: Medium  
**Impact**: Medium  
**Mitigation**:
- Research API capabilities early
- Identify limitations upfront
- Design workarounds or alternatives
- Maintain anti-corruption layer

#### Risk 3: Event Store Performance
**Probability**: Low  
**Impact**: High  
**Mitigation**:
- Performance test early and often
- Implement snapshotting if needed
- Optimize projection updates
- Consider caching strategies

#### Risk 4: F# Learning Curve
**Probability**: Low  
**Impact**: Medium  
**Mitigation**:
- Team training on F# idioms
- Code reviews focused on best practices
- Reference materials readily available
- Pair programming for complex areas

### Schedule Risks

#### Risk 5: Underestimated Complexity
**Probability**: Medium  
**Impact**: High  
**Mitigation**:
- Buffer time built into estimates
- Regular progress reviews
- Prioritize core features
- Be prepared to defer nice-to-have features

#### Risk 6: External Dependencies
**Probability**: Medium  
**Impact**: Medium  
**Mitigation**:
- Identify critical dependencies early
- Have backup plans for unavailable services
- Mock external services for testing
- Maintain flexible architecture

---

## Quality Gates

Each phase must pass these gates before proceeding:

### Code Quality
- ✅ No compiler warnings
- ✅ All tests passing
- ✅ Code review completed
- ✅ Style guidelines followed

### Functionality
- ✅ All phase deliverables complete
- ✅ Integration tests passing
- ✅ No known critical bugs

### Documentation
- ✅ API documentation updated
- ✅ Architecture decisions recorded
- ✅ Code comments adequate

### Performance
- ✅ No performance regressions
- ✅ Resource usage acceptable
- ✅ Response times within targets

---

## Team Structure (Recommended)

### Roles
1. **Technical Lead**: Architecture decisions, code reviews, mentoring
2. **F# Developer(s)**: Core implementation, domain modeling
3. **Integration Specialist**: Penpot API integration, external systems
4. **QA Engineer**: Testing, automation, quality assurance
5. **Technical Writer**: Documentation, examples, tutorials

### Communication
- Daily standups (15 minutes)
- Weekly progress reviews
- Bi-weekly retrospectives
- Ad-hoc pair programming sessions

---

## Success Metrics

### Technical Metrics
- **Test Coverage**: >80%
- **Build Time**: <5 minutes
- **Event Throughput**: >1000 events/second
- **API Response Time**: <200ms (p95)
- **Memory Usage**: <500MB under normal load

### Quality Metrics
- **Bug Density**: <1 bug per 1000 lines of code
- **Code Duplication**: <5%
- **Cyclomatic Complexity**: <10 per function
- **Documentation Coverage**: 100% of public APIs

### Project Metrics
- **On-Time Delivery**: Milestones hit within 1 week of target
- **Scope Creep**: <10% deviation from original plan
- **Team Satisfaction**: >4/5 on retrospectives

---

## Post-Launch Roadmap (Future Phases)

### Phase 7: Advanced Features (Months 4-5)
- Advanced Penpot operations (animations, prototypes)
- Real-time collaboration support
- Advanced component libraries
- Template system

### Phase 8: Performance & Scale (Month 6)
- Horizontal scaling capabilities
- Distributed event store
- Advanced caching strategies
- Load balancing

### Phase 9: Ecosystem Integration (Month 7-8)
- Integration with design tools
- Plugin system for extensibility
- Webhook support
- API versioning

### Phase 10: Enterprise Features (Month 9-12)
- Multi-tenancy support
- Advanced security features
- Audit and compliance reporting
- Enterprise deployment options

---

## Appendix: Technology Stack

### Core Technologies
- **Language**: F# (.NET 8+)
- **Runtime**: .NET 8+ Runtime
- **Build System**: dotnet CLI, FAKE (optional)

### Libraries and Frameworks
- **JSON**: System.Text.Json
- **HTTP Client**: HttpClient
- **HTTP Server**: Giraffe or Suave
- **Logging**: Serilog
- **Testing**: Expecto or xUnit + FsCheck
- **Event Store**: LiteDB or file-based (Phase 4 decision)

### Development Tools
- **IDE**: JetBrains Rider or VS Code with Ionide
- **Version Control**: Git
- **CI/CD**: GitHub Actions or GitLab CI
- **Containerization**: Docker

### Infrastructure
- **Deployment**: Docker containers
- **Monitoring**: Prometheus + Grafana (optional)
- **Logging Aggregation**: Seq or ELK stack (optional)

---

## Conclusion

This roadmap provides a structured approach to delivering FnMCP.Penpot over 12 weeks. By breaking the work into clear phases with defined deliverables and success criteria, we ensure steady progress toward a production-ready MCP server and client for Penpot.

The roadmap balances ambition with pragmatism, focusing on core features first while leaving room for advanced capabilities in future phases. Regular quality gates and risk mitigation strategies help ensure successful delivery.

---

**Document Status**: Initial Draft  
**Next Review**: End of Phase 1  
**Maintained By**: IvanTheGeek
