# Contributing to OpenNativeRuntime

Thank you for your interest in contributing to OpenNativeRuntime.

The project explores a cross-platform native application runtime built around C++, system WebViews, explicit native capabilities, and well-defined security boundaries.

During the early stages, architectural clarity, small prototypes, and reliable tests are more valuable than adding features quickly.

## Before contributing

Please:

1. Read the project [README](README.md).
2. Review [Architecture](docs/architecture.md).
3. Review the [Threat Model](docs/threat-model.md) before changing security-sensitive components.
4. Search existing issues and pull requests to avoid duplicate work.

For substantial architectural changes, open an issue or design discussion before starting implementation.

## Ways to contribute

Contributions may include:

- C++ runtime implementation.
- GTK and WebKitGTK integration.
- Protocol design and validation.
- Permission enforcement and security testing.
- Frontend SDK development.
- Build system improvements.
- Documentation and architecture reviews.
- Reproducible build and packaging support.
- Cross-platform compatibility research.

## Development principles

- Prefer small, focused changes.
- Keep platform-specific code behind explicit interfaces.
- Avoid adding dependencies without a clear justification.
- Do not expose unrestricted native functionality to frontend code.
- Treat external messages and extension inputs as untrusted.
- Document security assumptions and failure behavior.
- Add tests for new behavior and regression fixes.
- Avoid claiming cross-platform support before it has been tested.

## Code style

### C++

- Use the C++ standard configured by the project.
- Prefer RAII and deterministic resource management.
- Avoid unnecessary raw ownership and global mutable state.
- Use clear interfaces and explicit error handling.
- Keep platform-specific headers out of platform-independent public interfaces where practical.
- Document concurrency assumptions and object lifetimes.

### TypeScript

- Prefer explicit types for public APIs.
- Represent errors consistently.
- Document asynchronous behavior.
- Avoid APIs that silently grant additional native privileges.

### Documentation

- Use Markdown.
- Keep examples consistent with the actual implementation.
- Mark planned features as planned.
- Explain security limitations and platform-specific behavior.

## Testing

Before submitting a pull request:

- Build the affected targets.
- Run relevant unit and integration tests.
- Add regression tests for bug fixes.
- Verify malformed input and error paths where relevant.
- Document tests that could not be run.

Security-sensitive changes should include tests for denied access, invalid messages, and unexpected failures.

## Pull request guidelines

A pull request should:

- Explain the problem being solved.
- Describe the implementation and any design trade-offs.
- Include relevant tests.
- Update documentation when behavior or APIs change.
- Identify platform limitations.
- Call out security implications.

Keep unrelated refactoring separate whenever practical.

## Security vulnerabilities

Do not disclose exploitable vulnerabilities in public issues before coordinating responsible disclosure. Follow [SECURITY.md](SECURITY.md).

## Licensing

By contributing, you agree that your contributions are submitted under the project's MIT License, subject to applicable law and any explicit written agreement with the project.

Please ensure you have the right to contribute all submitted code, documentation, and other materials.

## Community expectations

Be respectful, constructive, and open to technical disagreement. Critique implementations and ideas rather than people.

The objective is to build a maintainable runtime with explicit engineering and security guarantees, not to win framework comparisons.