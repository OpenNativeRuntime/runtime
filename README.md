# OpenNativeRuntime

**A native application runtime for web-powered desktop applications.**

OpenNativeRuntime is an open-source systems project exploring how to build cross-platform desktop applications using web technologies and native operating-system capabilities, without requiring every application to bundle its own browser engine.

The project aims to combine the flexibility of HTML, CSS, JavaScript, and frameworks such as React with a C++ runtime, system-provided WebViews, explicit native capabilities, and well-defined security boundaries.

> **Project status:** Early design and prototyping. The architecture and capabilities described below are goals, not claims about an already production-ready implementation.

## Prerequisites

- A C++20-compatible compiler.
- CMake 3.25 or newer.
- No GTK or WebKitGTK dependencies are required at this stage.

## Build from Source

Configure the project and generate the build files:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
```

Build the runtime executable:

```bash
cmake --build build --parallel 2
```

Run the prototype:

```bash
./build/onr-runtime
```

Expected output:

```text
OpenNativeRuntime prototype
```

> **Current implementation status:** OpenNativeRuntime currently builds a minimal native C++ executable. Web rendering, native capabilities, process isolation, and other runtime features are planned and have not yet been implemented.


## Why OpenNativeRuntime?

Electron makes it possible to build desktop applications with web technologies, but distributing a browser engine with each application can increase package size and resource requirements.

Using the operating system's WebView offers another approach, but introduces challenges around platform differences, native API access, process isolation, permissions, lifecycle management, and deployment.

OpenNativeRuntime explores a runtime architecture that addresses these concerns explicitly.

## Design goals

- **Native integration:** Use system-provided WebViews rather than requiring every application to bundle a separate browser engine.
- **Cross-platform architecture:** Keep the runtime core independent of platform-specific windowing and rendering implementations.
- **Capability-based APIs:** Expose native functionality through explicit, permission-controlled interfaces.
- **Secure communication:** Validate messages crossing the frontend-to-native boundary.
- **Process isolation:** Support running optional native extensions in separate processes with restricted privileges.
- **Developer experience:** Provide a TypeScript SDK, project templates, development tooling, and reproducible packaging.
- **Incremental development:** Build and validate each subsystem through working applications and automated tests.

## Planned technology stack

| Component | Initial direction |
|---|---|
| Runtime core | C++20 |
| Linux host | GTK 4 |
| Linux WebView | WebKitGTK |
| Frontend | HTML, CSS, JavaScript, TypeScript |
| Frontend framework | React supported through a template |
| Windows WebView | Microsoft WebView2 |
| macOS WebView | WKWebView |
| Build system | CMake |
| Initial protocol | Versioned JSON messages |

Technology choices may evolve as prototypes reveal implementation constraints.

## Architecture

The intended architecture separates the frontend, runtime core, platform adapters, native capability services, and optional extension processes.

```text
Web Application
      |
TypeScript SDK
      |
Validated Request Protocol
      |
C++ Runtime Core
      |
      +-- Capability and Permission Policy
      +-- Native Capability Services
      +-- Lifecycle and Event Management
      +-- Extension Process Management
      |
Platform Adapter
      |
Operating System and System WebView
```

The diagram describes logical responsibilities, not a promise that every component will run in a separate process. Process boundaries will be introduced where they provide meaningful isolation.

See [Architecture](docs/architecture.md) for details.

## Security principles

OpenNativeRuntime is being designed around the following principles:

- Deny native capabilities by default.
- Validate every request at the native boundary.
- Grant the minimum permissions required by an application.
- Scope access to resources wherever practical.
- Treat frontend messages and extension inputs as untrusted.
- Keep security enforcement out of the frontend.
- Use operating-system isolation mechanisms where available.
- Test failure handling, malformed requests, and unauthorized operations.

A permission system inside the runtime is not a substitute for operating-system sandboxing. The security model must account for compromised frontend content, vulnerable WebView engines, malicious extensions, and compromised native components.

See [Threat Model](docs/threat-model.md).

## Development roadmap

1. **Linux prototype:** Create a GTK application embedding WebKitGTK and load a local frontend.
2. **Communication protocol:** Implement typed asynchronous requests, responses, and events.
3. **Capability security:** Introduce manifests, permission checks, and restricted native APIs.
4. **Extension isolation:** Run optional native extensions out of process.
5. **Developer tooling:** Build a CLI, templates, diagnostics, and packaging workflows.
6. **Cross-platform support:** Add Windows and macOS adapters.

Each phase should have a working demonstration and automated tests before the next major phase begins.

## Current status

The project is in its initial design and implementation stage.

Planned capabilities are not necessarily implemented. Platform support, security guarantees, performance, and resource consumption must be established through testing rather than assumed.

## Contributing

Contributions, design discussions, bug reports, and architectural feedback are welcome.

Start with [CONTRIBUTING.md](CONTRIBUTING.md) and the documentation in `docs/`.

## License

OpenNativeRuntime is intended to be distributed under the MIT License. See [LICENSE](LICENSE) for the applicable terms.

---

OpenNativeRuntime is an independent open-source project and is not affiliated with Electron, Tauri, Neutralinojs, GTK, or WebKit.