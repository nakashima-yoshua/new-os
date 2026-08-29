# Design Principles

These principles are design choices and research hypotheses, not claims that the underlying mechanisms are new. Many have strong precedents in capability systems, microkernels, language-safe operating systems, structured concurrency, immutable infrastructure, and typed component models. See [Novelty Landscape and Prior Art](../research/novelty-landscape.md).

## 1. Explicit over ambient

Authority, dependencies, ownership, resource consumption, lifecycle, and state transitions SHOULD be explicit and inspectable.

The research question is whether these concerns can share one coherent model without imposing unacceptable complexity or overhead.

## 2. Capability-based authority

Possession of an unforgeable capability, rather than global ambient administrator authority, SHOULD be the native basis for resource access.

Capabilities SHOULD be narrow, delegable, attenuable, and optionally time- or resource-bounded. Revocation semantics remain an open design problem.

Capability security itself is established prior art; this project must specify how capabilities interact with ownership, component lifetime, resource budgets, recovery, and compatibility boundaries.

## 3. Typed resources instead of one universal byte-oriented abstraction

The system SHOULD model storage, network endpoints, devices, timers, memory regions, services, and other objects as typed resources with explicit interfaces.

Uniformity SHOULD come from common resource, capability, and interaction semantics rather than requiring every resource to expose the same read/write/control surface.

## 4. Explicit component lifecycle

Execution SHOULD be represented by components with explicit parents, children, resources, capabilities, budgets, and termination semantics.

The stronger hypothesis is that delegated authority and resource reservations can follow the same ownership/lifetime structure as execution.

## 5. Message passing before shared mutable state

Component isolation and typed message passing SHOULD be the default communication model.

Shared memory MAY be available as an explicit capability when performance requires it. The design must demonstrate practical zero-copy or high-throughput paths rather than assuming message passing is universally sufficient.

## 6. Transactional state change

Where practical, persistent state changes SHOULD be atomic and observable as explicit transitions. Applications should not need to reconstruct transactional semantics from unrelated low-level operations when the underlying semantic operation is transactional.

This is an established principle in databases and persistent systems; the open question is where the OS/runtime boundary should expose it.

## 7. Immutable system and packages

System software and application packages SHOULD be immutable and content-identifiable where practical. Updates SHOULD be atomic and rollback SHOULD be a normal operation.

Nix/NixOS, Guix, OSTree, container systems, and related work are explicit prior art. The project should reuse their lessons rather than re-invent these mechanisms.

## 8. Structured observability

Events SHOULD be structured data with identity, time, origin, cause, and correlation information where practical. Observability SHOULD be machine-readable and tied to component/resource identity.

The research question is whether lifecycle, authority delegation, resource accounting, and state transitions can expose useful causal relationships without excessive cost.

## 9. Resource budgets are first-class

CPU, memory, storage, I/O, network, GPU, energy, and similar finite resources SHOULD be explicitly allocatable and inspectable.

The proposed differentiator is not quotas themselves, but binding resource budgets to component ownership and capability delegation semantics.

## 10. Compatibility is isolated

Historical compatibility MUST NOT silently define the native semantic model. POSIX/Linux compatibility SHOULD live behind an explicit compatibility boundary.

This is not a claim that POSIX is obsolete. The project must demonstrate that its replacement model is useful enough to justify the translation cost.

## 11. AI is an unprivileged workload

Probabilistic AI SHOULD NOT be required for kernel correctness. AI agents SHOULD execute as ordinary components with bounded capabilities, budgets, lifecycle, and auditability.

Agent sandboxing and agent-oriented runtimes already exist. AI is therefore treated primarily as a demanding workload for validating the general security and resource model.

## 12. Rust is both implementation language and comparison point

The reference runtime SHOULD be implemented in Rust.

Rust's ownership, borrowing, lifetime, affine-resource, and type-safety mechanisms motivate experiments in making OS-level resource ownership and authority transfer more explicit. Existing Rust OS work such as Theseus, Redox, and Asterinas must be treated as prior art.

The project should investigate whether ownership-like invariants can remain meaningful across component boundaries rather than assuming that using Rust alone provides system-level safety.

## 13. Validate semantics before building a kernel

The project SHOULD validate its computing model through a Rust/Linux reference runtime and real workloads before committing to a native kernel architecture.

Each major abstraction SHOULD be accompanied by prior-art comparison, a falsifiable hypothesis, and a test capable of demonstrating that the abstraction should be revised or abandoned.
