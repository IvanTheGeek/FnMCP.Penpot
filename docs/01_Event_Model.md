# Event Model Documentation
**Version:** 1.0  
**Date:** 2025-11-03  
**Status:** Planning Phase

---

## Overview

This document details the Event Model for FnMCP.Penpot, following Event Modeling principles established by Adam Dymitruk. The event model captures the complete behavior of the system through commands, events, views, and automation policies.

---

## Event Modeling Fundamentals

### The Four Patterns

1. **Commands (Blue)**: Intentions from users or external systems
2. **Events (Orange)**: Facts about what has happened
3. **Views/Read Models (Green)**: Data projections for queries
4. **Automation (Purple)**: Reactive policies triggered by events
   - Can be implemented as a **Screen** (human-operated interface)
   - Can be implemented as a **Public API** (for external system integration)
   - In this MCP project, automation is primarily reactive policies, though the MCP protocol itself serves as the public API

### Time-Based Organization

Events are organized chronologically, representing the system's behavior over time. Each slice represents a complete user journey or system workflow.

---

## Core Event Slices

### Slice 1: MCP Server Initialization

**Goal**: Start the MCP server and establish readiness to accept connections.

#### Command
```fsharp
type InitializeMCPServer = {
    ServerInfo: ServerInfo
    Capabilities: ServerCapabilities
    TransportType: TransportType  // stdio or HTTP+SSE
}
```

#### Events
```fsharp
type MCPServerInitialized = {
    ServerId: ServerId
    ServerInfo: ServerInfo
    Capabilities: ServerCapabilities
    TransportType: TransportType
    Timestamp: DateTimeOffset
}

type MCPServerStarted = {
    ServerId: ServerId
    ListeningAddress: string option  // For HTTP transport
    Timestamp: DateTimeOffset
}
```

#### Views
- **ServerStatusView**: Current server state, uptime, capabilities

---

### Slice 2: MCP Client Connection

**Goal**: Establish a connection between an MCP client and the server.

#### Command
```fsharp
type ConnectMCPClient = {
    ClientInfo: ClientInfo
    ProtocolVersion: string
}
```

#### Events
```fsharp
type MCPClientConnected = {
    ConnectionId: ConnectionId
    ClientInfo: ClientInfo
    ProtocolVersion: string
    Timestamp: DateTimeOffset
}

type MCPHandshakeCompleted = {
    ConnectionId: ConnectionId
    NegotiatedCapabilities: NegotiatedCapabilities
    Timestamp: DateTimeOffset
}
```

#### Views
- **ActiveConnectionsView**: List of connected clients
- **ClientCapabilitiesView**: Capabilities of each client

---

### Slice 3: Authenticate with Penpot

**Goal**: Authenticate the MCP server with Penpot API to access user resources.

#### Command
```fsharp
type AuthenticateWithPenpot = {
    ConnectionId: ConnectionId
    Credentials: PenpotCredentials  // API token or OAuth
}
```

#### Events
```fsharp
type PenpotAuthenticationInitiated = {
    ConnectionId: ConnectionId
    AuthenticationMethod: AuthMethod
    Timestamp: DateTimeOffset
}

type PenpotAuthenticationSucceeded = {
    ConnectionId: ConnectionId
    AccessToken: AccessToken
    ExpiresAt: DateTimeOffset
    UserId: PenpotUserId
    Timestamp: DateTimeOffset
}

type PenpotAuthenticationFailed = {
    ConnectionId: ConnectionId
    Reason: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **AuthenticationStatusView**: Authentication state per connection
- **PenpotSessionView**: Active Penpot sessions and token expiry

#### Automation
- **TokenRefreshPolicy**: When `PenpotAuthenticationSucceeded.ExpiresAt` approaches, trigger `RefreshPenpotToken`

---

### Slice 4: List Penpot Resources (Files)

**Goal**: Client requests list of available Penpot design files via MCP resources.

#### Command
```fsharp
type ListPenpotResources = {
    ConnectionId: ConnectionId
    ResourceType: ResourceType  // Files, Pages, Components
    Cursor: string option  // For pagination
}
```

#### Events
```fsharp
type PenpotResourcesRequested = {
    ConnectionId: ConnectionId
    ResourceType: ResourceType
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type PenpotResourcesRetrieved = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    Resources: MCPResource list
    NextCursor: string option
    Timestamp: DateTimeOffset
}

type PenpotResourcesRetrievalFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **PenpotResourceCatalogView**: Cached list of available resources
- **ResourceRequestHistoryView**: Log of resource requests

---

### Slice 5: Read Penpot Resource

**Goal**: Retrieve detailed information about a specific Penpot resource.

#### Command
```fsharp
type ReadPenpotResource = {
    ConnectionId: ConnectionId
    ResourceUri: ResourceUri
}
```

#### Events
```fsharp
type PenpotResourceReadRequested = {
    ConnectionId: ConnectionId
    ResourceUri: ResourceUri
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type PenpotResourceContentRetrieved = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    ResourceUri: ResourceUri
    Content: ResourceContent
    Timestamp: DateTimeOffset
}

type PenpotResourceReadFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    ResourceUri: ResourceUri
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **ResourceContentView**: Cached resource contents
- **ResourceMetadataView**: Resource metadata (size, modified date, etc.)

---

### Slice 6: Create Design File

**Goal**: Create a new design file in Penpot via MCP tool invocation.

#### Command
```fsharp
type CreateDesignFile = {
    ConnectionId: ConnectionId
    FileName: string
    ProjectId: PenpotProjectId option
}
```

#### Events
```fsharp
type DesignFileCreationRequested = {
    ConnectionId: ConnectionId
    FileName: string
    ProjectId: PenpotProjectId option
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type DesignFileCreated = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    FileName: string
    ProjectId: PenpotProjectId option
    Timestamp: DateTimeOffset
}

type DesignFileCreationFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileName: string
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **UserFilesView**: List of files created by user
- **FileCreationHistoryView**: Audit log of file creation events

#### Automation
- **ResourceCatalogUpdatePolicy**: When `DesignFileCreated`, trigger update of `PenpotResourceCatalogView`

---

### Slice 7: Add Shape to Page

**Goal**: Add a shape (rectangle, circle, text, etc.) to a page in a design file.

#### Command
```fsharp
type AddShapeToPage = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeType: ShapeType
    Properties: ShapeProperties
}
```

#### Events
```fsharp
type ShapeAdditionRequested = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeType: ShapeType
    Properties: ShapeProperties
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type ShapeAddedToPage = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeId: PenpotShapeId
    ShapeType: ShapeType
    Properties: ShapeProperties
    Timestamp: DateTimeOffset
}

type ShapeAdditionFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    PageId: PenpotPageId
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **PageContentView**: Current shapes and elements on a page
- **ShapeHistoryView**: History of shape operations

---

### Slice 8: Modify Shape Properties

**Goal**: Update properties of an existing shape (color, size, position, etc.).

#### Command
```fsharp
type ModifyShapeProperties = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeId: PenpotShapeId
    PropertyUpdates: PropertyUpdate list
}
```

#### Events
```fsharp
type ShapeModificationRequested = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeId: PenpotShapeId
    PropertyUpdates: PropertyUpdate list
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type ShapePropertiesModified = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    PageId: PenpotPageId
    ShapeId: PenpotShapeId
    AppliedUpdates: PropertyUpdate list
    Timestamp: DateTimeOffset
}

type ShapeModificationFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    ShapeId: PenpotShapeId
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **ShapeStateView**: Current state of each shape
- **ModificationHistoryView**: Audit log of shape modifications

---

### Slice 9: Create Component

**Goal**: Convert shapes into a reusable component.

#### Command
```fsharp
type CreateComponent = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    ComponentName: string
    SourceShapes: PenpotShapeId list
}
```

#### Events
```fsharp
type ComponentCreationRequested = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    ComponentName: string
    SourceShapes: PenpotShapeId list
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type ComponentCreated = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    ComponentId: PenpotComponentId
    ComponentName: string
    Timestamp: DateTimeOffset
}

type ComponentCreationFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    ComponentName: string
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **ComponentLibraryView**: Available components in each file
- **ComponentUsageView**: Where components are instantiated

---

### Slice 10: Export Design

**Goal**: Export a design file or page in a specific format (PNG, SVG, PDF).

#### Command
```fsharp
type ExportDesign = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId option  // None = export entire file
    Format: ExportFormat  // PNG, SVG, PDF
    Options: ExportOptions
}
```

#### Events
```fsharp
type DesignExportRequested = {
    ConnectionId: ConnectionId
    FileId: PenpotFileId
    PageId: PenpotPageId option
    Format: ExportFormat
    Options: ExportOptions
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type DesignExported = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    PageId: PenpotPageId option
    Format: ExportFormat
    ExportedFilePath: string
    FileSize: int64
    Timestamp: DateTimeOffset
}

type DesignExportFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    FileId: PenpotFileId
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **ExportHistoryView**: Log of export operations
- **ExportedFilesView**: Available exported files

---

### Slice 11: Execute MCP Prompt

**Goal**: Execute a pre-defined prompt template for common Penpot operations.

#### Command
```fsharp
type ExecuteMCPPrompt = {
    ConnectionId: ConnectionId
    PromptName: string
    Arguments: Map<string, string>
}
```

#### Events
```fsharp
type MCPPromptExecutionRequested = {
    ConnectionId: ConnectionId
    PromptName: string
    Arguments: Map<string, string>
    RequestId: RequestId
    Timestamp: DateTimeOffset
}

type MCPPromptExecuted = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    PromptName: string
    Result: PromptResult
    Timestamp: DateTimeOffset
}

type MCPPromptExecutionFailed = {
    ConnectionId: ConnectionId
    RequestId: RequestId
    PromptName: string
    Error: string
    Timestamp: DateTimeOffset
}
```

#### Views
- **AvailablePromptsView**: Catalog of available prompts
- **PromptExecutionHistoryView**: Log of prompt executions

---

### Slice 12: Client Disconnection

**Goal**: Handle graceful or abrupt client disconnection.

#### Command
```fsharp
type DisconnectMCPClient = {
    ConnectionId: ConnectionId
    Reason: DisconnectionReason
}
```

#### Events
```fsharp
type MCPClientDisconnected = {
    ConnectionId: ConnectionId
    Reason: DisconnectionReason
    Timestamp: DateTimeOffset
}

type ConnectionCleanedUp = {
    ConnectionId: ConnectionId
    ReleasedResources: string list
    Timestamp: DateTimeOffset
}
```

#### Views
- **ConnectionHistoryView**: Historical connection records
- **ActiveConnectionsView**: Updated to remove disconnected client

#### Automation
- **SessionCleanupPolicy**: When `MCPClientDisconnected`, trigger cleanup of associated resources

---

## Event Store Schema

### Stream Naming Convention

```
server:<ServerId>                           # Server-level events
connection:<ConnectionId>                   # Connection-level events
penpot-file:<FileId>                       # File-level events
penpot-page:<PageId>                       # Page-level events
penpot-shape:<ShapeId>                     # Shape-level events
penpot-component:<ComponentId>             # Component-level events
mcp-request:<RequestId>                    # Request-level events (for correlation)
```

### Event Metadata

Every event includes:
```fsharp
type EventMetadata = {
    EventId: Guid
    EventType: string
    StreamId: string
    EventNumber: int64
    Timestamp: DateTimeOffset
    CorrelationId: Guid option  // Links related events
    CausationId: Guid option    // Event that caused this event
    UserId: string option
    ConnectionId: ConnectionId option
}
```

---

## Projections (Read Models)

### 1. ServerStatusView

**Purpose**: Current state of the MCP server

**Built From**:
- `MCPServerInitialized`
- `MCPServerStarted`
- `MCPClientConnected`
- `MCPClientDisconnected`

**Schema**:
```fsharp
type ServerStatusView = {
    ServerId: ServerId
    Status: ServerStatus  // Starting, Running, Stopping, Stopped
    StartedAt: DateTimeOffset option
    Uptime: TimeSpan
    ActiveConnections: int
    TotalRequestsHandled: int64
    LastActivityAt: DateTimeOffset option
}
```

---

### 2. ActiveConnectionsView

**Purpose**: List of currently connected MCP clients

**Built From**:
- `MCPClientConnected`
- `MCPHandshakeCompleted`
- `MCPClientDisconnected`

**Schema**:
```fsharp
type ActiveConnectionView = {
    ConnectionId: ConnectionId
    ClientInfo: ClientInfo
    ConnectedAt: DateTimeOffset
    LastActivityAt: DateTimeOffset
    RequestCount: int
    NegotiatedCapabilities: NegotiatedCapabilities
}

type ActiveConnectionsView = {
    Connections: Map<ConnectionId, ActiveConnectionView>
}
```

---

### 3. PenpotResourceCatalogView

**Purpose**: Cached catalog of available Penpot resources

**Built From**:
- `PenpotResourcesRetrieved`
- `DesignFileCreated`
- `ComponentCreated`

**Schema**:
```fsharp
type PenpotResourceCatalogView = {
    Files: Map<PenpotFileId, FileResource>
    Pages: Map<PenpotPageId, PageResource>
    Components: Map<PenpotComponentId, ComponentResource>
    LastUpdated: DateTimeOffset
}
```

---

### 4. FileContentView

**Purpose**: Current state of a design file with all its pages and shapes

**Built From**:
- `DesignFileCreated`
- `ShapeAddedToPage`
- `ShapePropertiesModified`
- `ComponentCreated`

**Schema**:
```fsharp
type FileContentView = {
    FileId: PenpotFileId
    FileName: string
    Pages: Map<PenpotPageId, PageContent>
    Components: Map<PenpotComponentId, ComponentDefinition>
    CreatedAt: DateTimeOffset
    LastModifiedAt: DateTimeOffset
}

type PageContent = {
    PageId: PenpotPageId
    PageName: string
    Shapes: Map<PenpotShapeId, ShapeState>
}

type ShapeState = {
    ShapeId: PenpotShapeId
    ShapeType: ShapeType
    Properties: ShapeProperties
    LastModifiedAt: DateTimeOffset
}
```

---

### 5. AuditLogView

**Purpose**: Complete audit trail of all operations

**Built From**: All events

**Schema**:
```fsharp
type AuditLogEntry = {
    EventId: Guid
    EventType: string
    Timestamp: DateTimeOffset
    ConnectionId: ConnectionId option
    UserId: string option
    Action: string
    TargetResource: string option
    Result: OperationResult
    Details: Map<string, string>
}

type AuditLogView = {
    Entries: AuditLogEntry list
}
```

---

## Automation Policies

### 1. TokenRefreshPolicy

**Trigger**: `PenpotAuthenticationSucceeded`

**Logic**: Schedule token refresh 5 minutes before expiry

**Action**: Issue `RefreshPenpotToken` command

---

### 2. ResourceCatalogUpdatePolicy

**Trigger**: 
- `DesignFileCreated`
- `ComponentCreated`

**Logic**: Invalidate and update resource catalog

**Action**: Update `PenpotResourceCatalogView` projection

---

### 3. ConnectionTimeoutPolicy

**Trigger**: Periodic check (every 60 seconds)

**Logic**: Find connections with no activity for >30 minutes

**Action**: Issue `DisconnectMCPClient` command with reason `Timeout`

---

### 4. ErrorNotificationPolicy

**Trigger**: Any event with type ending in `Failed`

**Logic**: Check if error is critical and requires notification

**Action**: Send notification to monitoring system

---

### 5. CacheInvalidationPolicy

**Trigger**: 
- `ShapeAddedToPage`
- `ShapePropertiesModified`

**Logic**: Invalidate cached page content

**Action**: Mark `FileContentView` as stale for specific file

---

## Event Versioning Strategy

### Event Evolution

As the system evolves, events may need to change. Strategy:

1. **Additive Changes**: Add optional fields to events
   ```fsharp
   type DesignFileCreated = {
       FileId: PenpotFileId
       FileName: string
       // New optional field added in v2
       Tags: string list option
   }
   ```

2. **Breaking Changes**: Create new event version
   ```fsharp
   type DesignFileCreatedV1 = { ... }
   type DesignFileCreatedV2 = { ... }
   
   // Upcasting function
   let upcastV1ToV2 (v1: DesignFileCreatedV1) : DesignFileCreatedV2 = ...
   ```

3. **Event Type Naming**: Include version in critical events
   ```fsharp
   [<EventType("DesignFileCreated.v2")>]
   type DesignFileCreated = { ... }
   ```

---

## Event Processing Patterns

### Command Handler Pattern

```fsharp
type CommandHandler<'Command, 'Event> = 
    'Command -> Async<Result<'Event list, CommandError>>

// Example
let handleCreateDesignFile 
    (penpotApi: IPenpotApi)
    (command: CreateDesignFile)
    : Async<Result<DomainEvent list, CommandError>> =
    async {
        // 1. Validate command
        match validateCreateDesignFile command with
        | Error validationErrors -> 
            return Error (ValidationError validationErrors)
        | Ok validCommand ->
            
            // 2. Call external API
            let! apiResult = penpotApi.CreateFile(validCommand.FileName, validCommand.ProjectId)
            match apiResult with
            | Error apiError -> 
                return Error (ExternalApiError apiError)
            | Ok penpotFileId ->
                
                // 3. Emit events
                let events = [
                    DesignFileCreationRequested {
                        ConnectionId = command.ConnectionId
                        FileName = command.FileName
                        ProjectId = command.ProjectId
                        RequestId = RequestId.generate()
                        Timestamp = DateTimeOffset.UtcNow
                    }
                    DesignFileCreated {
                        ConnectionId = command.ConnectionId
                        RequestId = RequestId.generate()
                        FileId = penpotFileId
                        FileName = command.FileName
                        ProjectId = command.ProjectId
                        Timestamp = DateTimeOffset.UtcNow
                    }
                ]
                return Ok events
    }
```

### Event Handler Pattern

```fsharp
type EventHandler<'Event> = 
    'Event -> Async<unit>

// Example: Update projection
let handleDesignFileCreated 
    (updateView: FileContentView -> Async<unit>)
    (event: DesignFileCreated)
    : Async<unit> =
    async {
        let view = {
            FileId = event.FileId
            FileName = event.FileName
            Pages = Map.empty
            Components = Map.empty
            CreatedAt = event.Timestamp
            LastModifiedAt = event.Timestamp
        }
        do! updateView view
    }
```

---

## Testing Event Models

### Property-Based Testing for Events

```fsharp
// Test: Event serialization round-trip
let ``Event serialization is reversible`` (event: DomainEvent) =
    let json = serialize event
    let deserialized = deserialize json
    deserialized = event

// Test: Projection is deterministic
let ``Projection produces same result for same events`` (events: DomainEvent list) =
    let projection1 = buildProjection events
    let projection2 = buildProjection events
    projection1 = projection2

// Test: Event ordering matters
let ``Event order affects projection state`` (events: DomainEvent list) =
    let projection1 = buildProjection events
    let projection2 = buildProjection (List.rev events)
    // Should be different for non-commutative events
    projection1 <> projection2
```

---

## Conclusion

This event model provides a complete blueprint for the FnMCP.Penpot system's behavior. By capturing all state changes as events, we achieve:

1. **Complete Audit Trail**: Every action is recorded
2. **Temporal Queries**: Query system state at any point in time
3. **Debugging**: Replay events to reproduce issues
4. **Scalability**: Read models can be rebuilt independently
5. **Flexibility**: New projections can be added without changing events

---

**Document Status**: Initial Draft  
**Next Review**: After domain model finalization  
**Maintained By**: IvanTheGeek
