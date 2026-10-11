# OpenNativeRuntime Architecture

## 1. Purpose

OpenNativeRuntime is designed to host web-based user interfaces inside native desktop applications while exposing operating-system capabilities through a controlled runtime API.

The initial implementation targets Linux using GTK 4 and WebKitGTK. The architecture should support future Windows and macOS adapters without coupling the runtime core to a particular windowing system or WebView engine.

This document describes the intended architecture. Interfaces and implementation details may change as prototypes are developed.

## 2. Design principles

### Platform independence

Platform-specific code belongs behind defined interfaces. The runtime core should not depend directly on GTK, WebKitGTK, WebView2, or WKWebView.

### Explicit capabilities

Native functionality is exposed through registered operations with explicit authorization rules. The frontend does not receive unrestricted access to native APIs.

### Minimal trusted computing base

Keep security-sensitive components small and auditable. Avoid making the frontend, extension host, or arbitrary application code responsible for enforcing their own permissions.

### Fault containment

Where practical, isolate optional extensions and other potentially unreliable components in separate processes.

### Observable behavior

Requests, authorization decisions, failures, and lifecycle transitions should be diagnosable without logging secrets or sensitive application data.

### Incremental implementation

Each architectural layer should be validated with tests and working examples before more complex features are introduced.

## 3. Logical components

### 3.1 Application frontend

The frontend consists of HTML, CSS, JavaScript, and optionally TypeScript and a framework such as React.

Responsibilities:

- Render the user interface.
- Handle user interactions.
- Invoke documented runtime APIs.
- Subscribe to runtime events.
- Present errors returned by native services.

The frontend is not a security enforcement authority. A compromised frontend must not be able to grant itself additional native capabilities.

### 3.2 TypeScript SDK

The SDK provides a stable frontend-facing API.

Initial responsibilities:

- Send native requests.
- Correlate requests and responses.
- Represent success and failure consistently.
- Subscribe to events.
- Support cancellation and timeouts where appropriate.
- Expose typed wrappers for registered capabilities.

The SDK is a convenience layer, not a security boundary. Native services must validate every request independently.

### 3.3 Platform adapter

The platform adapter manages the native window and WebView.

Initial Linux responsibilities:

- Initialize GTK.
- Create and destroy windows.
- Create and configure WebKitGTK views.
- Load the application frontend.
- Handle navigation and WebView lifecycle events.
- Integrate the frontend-to-native transport.
- Shut down resources cleanly.

Future adapters will implement equivalent runtime interfaces using the platform's native facilities.

The architecture must account for differences in WebView process models, navigation APIs, storage, permissions, and deployment requirements.

### 3.4 Runtime core

The C++ runtime core coordinates application lifecycle, native requests, permissions, and registered services.

Intended responsibilities:

- Register native capability handlers.
- Validate request envelopes.
- Enforce authorization policies.
- Dispatch authorized operations.
- Serialize results and errors.
- Manage application and window state.
- Track pending requests.
- Handle shutdown and cancellation.
- Provide structured diagnostics.

The core should not depend on a particular frontend framework.

### 3.5 Protocol layer

The protocol defines the messages exchanged between frontend code and native services.

The initial protocol uses versioned JSON messages.

A request conceptually contains:

- Protocol version.
- Request identifier.
- Operation name.
- Arguments.

A response contains:

- Request identifier.
- Success or failure status.
- Result or structured error.

Events use a separate message type.

The implementation must impose message-size limits, validate schemas, reject malformed envelopes, and prevent duplicate or invalid request identifiers from corrupting request tracking.

Protocol evolution should be explicit and testable.

### 3.6 Capability services

Services implement narrowly defined native operations.

Potential services include:

- Application metadata.
- Native file dialogs.
- Scoped filesystem operations.
- Notifications.
- Application preferences.
- Clipboard access.
- Window controls.

Services should not automatically receive unrestricted access to every operating-system resource.

Operations that access sensitive resources must enforce their own input constraints in addition to the runtime's general authorization checks.

### 3.7 Permission system

The permission system determines whether a frontend context can invoke a capability.

The intended model includes:

- Default-deny behavior.
- Explicit capability declarations.
- Per-operation authorization.
- Resource scoping.
- Origin and window-context checks where applicable.
- User consent for selected sensitive operations.
- Auditable authorization decisions.

Permission manifests describe requested privileges; they do not independently establish trust.

The runtime must determine which permissions are actually granted and enforce them on every relevant operation.

### 3.8 Extension host

Optional native extensions should execute in separate processes rather than receiving unrestricted access to the main runtime process.

The extension host is responsible for:

- Starting and stopping extensions.
- Validating extension requests.
- Enforcing extension-specific capabilities.
- Applying timeouts and resource limits.
- Detecting process failure.
- Cleaning up pending requests.
- Recovering from crashes where safe.

Separate processes are not automatically sandboxed. The implementation must apply appropriate operating-system restrictions and verify that those restrictions work.

## 4. Process model

The initial prototype may run the runtime core, native services, and platform adapter in one application process.

The WebView should use the isolation mechanisms supported by its platform.

Optional native extensions should be moved out of process before they are treated as untrusted plugins.

A future deployment may contain:

- Main runtime process.
- WebView process or processes managed by the WebView engine.
- One process per extension or extension group.
- Additional restricted service processes when justified.

The design must distinguish logical components from actual process boundaries.

## 5. Request lifecycle

A native request follows this general sequence:

1. The frontend calls a documented SDK method.
2. The transport receives the request.
3. The runtime validates the message and its size.
4. The runtime determines the requesting context.
5. The authorization policy checks the requested capability and resource scope.
6. The service validates operation-specific arguments.
7. The service executes the authorized operation.
8. The runtime serializes a structured response.
9. The SDK resolves or rejects the corresponding promise.

Failures must not bypass authorization or leave request state indefinitely allocated.

Long-running operations need explicit cancellation and timeout semantics.

## 6. Threading and asynchronous work

UI operations must run on the appropriate platform event loop.

Blocking filesystem work, subprocess management, and other expensive operations should not stall UI rendering.

The implementation must define:

- Which thread owns each platform object.
- How background results return to the UI thread.
- How cancellation propagates.
- How shutdown interacts with pending operations.
- How service callbacks are synchronized.

Thread safety should be designed explicitly rather than assumed from the use of asynchronous APIs.

## 7. Application lifecycle

The runtime should model application lifecycle transitions explicitly.

Relevant states include:

- Starting.
- Initializing platform resources.
- Loading frontend content.
- Ready.
- Shutting down.
- Stopped.
- Failed.

The precise state machine will evolve with the implementation.

Lifecycle transitions should be deterministic and tested, particularly when startup fails or a WebView or extension exits unexpectedly.

## 8. Platform adapter contract

Platform adapters should provide equivalent logical capabilities without pretending that operating systems behave identically.

Differences must be documented and surfaced through explicit capability discovery or unsupported-operation errors.

The core protocol and permission model should remain independent of platform-specific implementation details.

## 9. Initial implementation order

1. Create a GTK window and embed WebKitGTK.
2. Load a local frontend.
3. Implement one native metadata operation.
4. Add request correlation and structured errors.
5. Add authorization checks and tests.
6. Introduce a native file dialog and scoped file access.
7. Establish extension IPC and process supervision.
8. Add additional platform adapters.

## 10. Open design questions

The following decisions require implementation experience:

- Whether the frontend bridge should use JSON messages or a different encoding in later versions.
- How to identify and authenticate frontend contexts across WebView implementations.
- How to handle navigation and multiple windows safely.
- How capabilities should be represented in manifests.
- Which operations require user consent.
- How extension identities and distribution should work.
- Which OS sandboxing mechanisms are practical on each supported platform.
- How to provide reliable updates and reproducible application packaging.

These questions should be resolved through small prototypes, threat analysis, and tests rather than assumptions.