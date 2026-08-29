# ADR-0003: No native fork

- Status: Proposed
- Date: 2026-08-29

## Context

`fork()` elegantly matched early Unix process and memory models, but cloning a running execution context also implicitly clones substantial process state and authority. Modern multithreaded runtimes, accelerators, large address spaces, sandboxes and structured lifecycle management make these semantics increasingly complex.

## Decision

The native component model will use explicit creation rather than fork semantics.

Conceptually:

```text
spawn(package, capabilities, budget, arguments) -> ComponentHandle
```

Capabilities and resource budgets must be explicitly selected or derived by a defined policy.

Fork semantics may be emulated inside the POSIX compatibility subsystem.

## Consequences

- lifecycle and authority inheritance become explicit;
- native runtimes need a clear component-start protocol;
- direct porting of fork-dependent software requires compatibility support.

## Validation

Implement process/component creation in the reference runtime and evaluate representative server, CLI and worker workloads without native fork semantics.
