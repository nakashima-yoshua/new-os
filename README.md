# new-os

> What would we build if POSIX compatibility were not a design constraint?

**new-os** is an experimental, capability-oriented computing model and operating-system architecture.

The project starts from first principles rather than from Unix compatibility. Its goal is not to rewrite Linux, but to investigate whether a smaller and more explicit set of primitives can produce systems that are safer, easier to understand, easier to operate, and better suited to modern applications and AI agents.

## Status

**Specification v0.1 — exploratory.**

There is no production-ready operating system yet. The initial deliverable is a public specification, followed by a reference runtime implemented on top of Linux. A native kernel is intentionally deferred until the model has been validated.

## Core model

The native model is built around five concepts:

1. **Component** — an isolated execution and lifecycle unit.
2. **Capability** — explicit authority to operate on a resource.
3. **Resource** — a typed object such as storage, network, memory, device, timer, or service.
4. **Message / Event** — typed interaction between components and resources.
5. **State** — observable system state changed through explicit transitions.

A typical interaction is:

```text
Component
   |
Capability
   |
   v
Message / Action
   |
   v
Resource
   |
   v
State Transition
   |
   v
Event
```

## Design principle

The primary rule is:

> **Make authority, dependencies, ownership, resources, lifecycle, and state transitions explicit.**

The architecture avoids ambient authority, hidden shared state, implicit resource consumption, and global mutable configuration where practical.

## Architecture direction

```text
+--------------------------------------+
| Applications                         |
| Native / Linux compatibility / AI   |
+--------------------------------------+
| Component Runtime                    |
| lifecycle / capabilities / budgets  |
+--------------------------------------+
| System Services                      |
| storage / network / UI / devices    |
+--------------------------------------+
| Typed IPC + Capability Layer         |
+--------------------------------------+
| Minimal Kernel                       |
| scheduling / memory / IPC / timer   |
+--------------------------------------+
| Hardware                             |
+--------------------------------------+
```

The long-term direction is a capability-based microkernel architecture with user-space system services and drivers where feasible.

## Compatibility

POSIX and Linux compatibility are valuable, but they are not native design constraints. Compatibility should be provided by an isolated subsystem rather than by shaping the core model around historical APIs.

## Development strategy

The project deliberately proceeds from semantics to implementation:

```text
Specification
    -> Linux reference runtime
    -> real application experiments
    -> native runtime
    -> microkernel prototype
    -> hardware
```

The first reference runtime should demonstrate:

- explicit component lifecycle
- capability delegation
- typed IPC
- resource budgets
- immutable packages
- structured events

Linux mechanisms such as namespaces, cgroups, seccomp, Landlock, Unix sockets, containers, or WASM may be used as implementation tools. Linux is an experimental platform, not the architecture being copied.

## Documentation

- [Design principles](philosophy/principles.md)
- [Non-goals](philosophy/non-goals.md)
- [Legacy design decisions](philosophy/legacy-decisions.md)
- [Core model specification](spec/core-model.md)
- [Architecture](spec/architecture.md)
- [ADR-0001: POSIX is a compatibility layer](adr/0001-posix-compatibility-layer.md)
- [ADR-0002: Capability-based authority](adr/0002-capability-based-authority.md)
- [ADR-0003: No native fork](adr/0003-no-native-fork.md)
- [ADR-0004: Typed IPC](adr/0004-typed-ipc.md)

## License

Apache License 2.0. See [LICENSE](LICENSE).

## Contributions

The project is intentionally at an early design stage. Criticism of assumptions and alternative designs are welcome. Design changes should explain the problem being solved, trade-offs, compatibility impact, and how the proposal can be experimentally validated.
