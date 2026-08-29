# new-os

> What would we build if POSIX compatibility were not a native design constraint?

**new-os** is an experimental systems-research project that synthesizes established ideas from capability security, microkernels, typed component systems, immutable infrastructure, structured lifecycle management, and Rust-oriented resource safety.

The project does **not** claim that these mechanisms are individually new. Its research goal is to test whether they become more coherent and operationally useful when authority, ownership, lifecycle, resource budgets, typed interaction, and observable state transitions share one explicit native model.

POSIX/Linux is treated as a compatibility target rather than the semantic center of that model.

## Status

**Specification v0.1 — exploratory.**

There is no production-ready operating system yet. The initial deliverable is a public specification, followed by a Rust-based reference runtime implemented on top of Linux. A native kernel is intentionally deferred until the semantic model has survived real workloads and comparison with prior systems.

## Core model

The current model is organized around five concepts:

1. **Component** — an isolated execution, authority, lifecycle, and accounting unit.
2. **Capability** — explicit authority to operate on a resource.
3. **Resource** — a typed object such as storage, network, memory, device, timer, or service.
4. **Message / Event** — typed interaction and observable occurrence.
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

## Research hypotheses

The project currently focuses on three hypotheses rather than a broad claim of OS novelty.

### 1. One lifetime structure can govern execution, authority, resources, and budgets

A component hierarchy should not only describe process ancestry. Capability delegation, resource reservations, cancellation, failure propagation, and accounting should follow the same explicit ownership/lifetime structure.

Candidate invariant:

> A child cannot outlive, retain more authority than, or continue consuming resources reserved by an ancestor unless an explicit transfer operation changes ownership.

### 2. Typed capability interfaces can form the native application boundary

Applications should express external dependencies as typed resource interfaces plus explicit delegated capabilities rather than relying on ambient namespaces and implicit authority.

Candidate invariant:

> An application's external authority is derivable from its declared imports plus explicitly delegated runtime capabilities.

### 3. POSIX compatibility should not manufacture native authority

Legacy applications may run through a compatibility subsystem, but POSIX emulation must remain bounded by authority granted by the native host model.

Candidate invariant:

> Compatibility code may translate legacy operations, but it cannot create access to resources that were not granted by the native model.

## Design principle

The primary rule is:

> **Make authority, dependencies, ownership, resources, lifecycle, and state transitions explicit.**

The architecture attempts to minimize ambient authority, hidden shared state, implicit resource consumption, and global mutable configuration where practical.

This direction has substantial prior art. The project must justify any claimed improvement through comparison and experiment rather than by assuming older abstractions are inferior.

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

The long-term direction currently favors a capability-based microkernel architecture with user-space system services and drivers where feasible. This is a hypothesis to validate, not a fixed implementation commitment.

## Prior art

The project explicitly builds on and must be compared with work including:

- KeyKOS, EROS, Coyotos, seL4, Capsicum and other capability systems;
- L4-family systems, QNX, MINIX 3, Genode and other microkernel/service architectures;
- Singularity and other language-safe/typed OS research;
- Theseus, Redox, Asterinas and other Rust-oriented OS work;
- WebAssembly Component Model and WASI;
- Nix/NixOS, Guix and OSTree;
- structured concurrency and supervision-tree models;
- contemporary agent sandbox and agent-OS runtimes.

See [Novelty Landscape and Prior Art](research/novelty-landscape.md) for the current assessment.

## Compatibility

POSIX and Linux compatibility remain important because of the existing software ecosystem. They are treated as compatibility semantics rather than assumptions that automatically define the native API.

## Development strategy

The project deliberately proceeds from semantics to implementation:

```text
Specification
    -> Rust/Linux reference runtime
    -> real application experiments
    -> model revision
    -> native runtime
    -> microkernel prototype, only if justified
```

The first reference runtime should demonstrate:

- explicit component lifecycle;
- capability delegation;
- typed IPC;
- resource budgets;
- structured events;
- bounded POSIX/Linux compatibility experiments.

Linux mechanisms such as namespaces, cgroups, seccomp, Landlock, Unix sockets, containers, or WASM may be used as implementation tools. The purpose is to test the semantic model before paying the cost of a kernel implementation.

## Documentation

- [Design principles](philosophy/principles.md)
- [Non-goals](philosophy/non-goals.md)
- [Legacy design decisions](philosophy/legacy-decisions.md)
- [Core model specification](spec/core-model.md)
- [Architecture](spec/architecture.md)
- [Novelty landscape and prior art](research/novelty-landscape.md)
- [ADR-0001: POSIX is a compatibility layer](adr/0001-posix-compatibility-layer.md)
- [ADR-0002: Capability-based authority](adr/0002-capability-based-authority.md)
- [ADR-0003: No native fork](adr/0003-no-native-fork.md)
- [ADR-0004: Typed IPC](adr/0004-typed-ipc.md)

## License

Apache License 2.0. See [LICENSE](LICENSE).

## Contributions

The project is intentionally at an early design stage. Criticism, prior-art references, counterexamples, and alternative designs are especially welcome.

A design proposal should explain:

1. the problem being solved;
2. the closest prior systems or papers;
3. the exact semantic difference;
4. trade-offs and compatibility impact;
5. a falsifiable hypothesis or proof obligation;
6. an experiment that could cause the proposal to be revised or rejected.
