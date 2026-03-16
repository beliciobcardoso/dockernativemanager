## ADDED Requirements

### Requirement: User can register multiple Docker contexts
The system SHALL allow users to create and persist multiple Docker contexts, including at least local, TCP/TLS remote, and SSH-based endpoints, with a unique friendly name.

#### Scenario: Create a valid remote context
- **WHEN** the user submits a context form with valid name, endpoint, and authentication fields
- **THEN** the system stores the context and returns it in the context list

#### Scenario: Reject duplicate context name
- **WHEN** the user tries to save a context with a name that already exists
- **THEN** the system rejects the request with a validation error indicating name conflict

### Requirement: User can select an active Docker context
The system SHALL maintain one active context and SHALL execute Docker management operations against that active context unless an explicit context override is provided.

#### Scenario: Switch active context
- **WHEN** the user selects another context as active
- **THEN** subsequent operations are routed to the selected context

#### Scenario: Fallback to default local context
- **WHEN** no explicit context was previously selected in an existing installation
- **THEN** the system initializes and uses `local-default` as active context

### Requirement: System validates connectivity before persisting context
The system SHALL provide a connection test and SHALL block persistence when endpoint or authentication is invalid.

#### Scenario: Successful connection test
- **WHEN** the user triggers connection validation with reachable endpoint and valid credentials
- **THEN** the system reports success and allows saving the context

#### Scenario: Failed connection test
- **WHEN** the user triggers connection validation with unreachable endpoint or invalid credentials
- **THEN** the system reports a structured error and does not save the context

### Requirement: System isolates operations by context
The system SHALL isolate reads and mutations per context and SHALL prevent cross-context side effects.

#### Scenario: List containers for active context only
- **WHEN** the user opens the containers page with context A active
- **THEN** the list contains only containers from context A

#### Scenario: Destructive action targets explicit context
- **WHEN** the user confirms container removal while context B is active
- **THEN** the removal is executed only on context B

### Requirement: System exposes per-context connection status
The system SHALL expose connection status for each context, including connected, disconnected, and last known error.

#### Scenario: Show disconnected status after timeout
- **WHEN** a context heartbeat fails by timeout
- **THEN** that context status changes to disconnected with timeout error detail

#### Scenario: Recover status after reconnection
- **WHEN** connectivity to a previously disconnected context is restored
- **THEN** the context status changes to connected and clears transient error indicators

### Requirement: Sensitive connection data is protected
The system SHALL avoid exposing raw secrets in frontend state payloads and SHALL store sensitive connection material via backend-controlled secure handling.

#### Scenario: Context list hides secret fields
- **WHEN** the frontend requests saved contexts
- **THEN** the response excludes raw passwords, private keys, and certificate private key material

#### Scenario: Secret update without data leak
- **WHEN** a user updates credentials of an existing context
- **THEN** secrets are replaced securely and no raw secret is logged or returned in API responses
