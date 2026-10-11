# Security Policy

## Project status

OpenNativeRuntime is an early-stage project. Its architecture and security mechanisms are evolving, and it must not be assumed suitable for security-critical or production workloads without an independent assessment.

## Supported versions

Until the project establishes its first stable release, security fixes will generally target the current development version.

Supported release branches and security maintenance periods will be documented here as the project matures.

## Reporting a vulnerability

Please report suspected security vulnerabilities privately rather than publishing exploitable details in a public issue.

Use GitHub's **Report a vulnerability** feature in the repository's Security tab, if enabled.

If private vulnerability reporting is unavailable, contact the maintainers through a verified security contact published by the project.

Please include, where possible:

- Affected commit, version, or component.
- Operating system and relevant dependency versions.
- A description of the security impact.
- Reproduction steps or a proof of concept.
- Any suggested mitigation.

Please avoid including real user data, credentials, or other sensitive information.

## Scope

Areas of particular interest include:

- Unauthorized access to native capabilities.
- Bypasses of permission enforcement.
- Unsafe frontend-to-native message handling.
- Path traversal and filesystem access violations.
- Extension process isolation failures.
- Privilege escalation between runtime components.
- Unsafe handling of origins and navigation.
- Resource exhaustion through unbounded requests.
- Crashes or memory-safety issues in native components.
- Incorrect assumptions about operating-system sandboxing.

## Response process

The maintainers aim to:

1. Acknowledge a valid report within seven calendar days.
2. Assess severity, affected components, and reproducibility.
3. Coordinate remediation and a reasonable disclosure timeline with the reporter.
4. Publish a security advisory when appropriate.
5. Credit the reporter when requested and appropriate.

These are intended response goals, not a guarantee of immediate remediation.

## Disclosure

Please allow the maintainers reasonable time to investigate and address a vulnerability before public disclosure.

The project may coordinate a release, advisory, and mitigation guidance with the reporter.

## Security limitations

OpenNativeRuntime cannot guarantee protection against a fully compromised operating system or against every vulnerability in its WebView engine, native dependencies, or application code.

The runtime's security guarantees must be evaluated against its documented threat model and the specific implementation in use.