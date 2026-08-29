# Design Principles

## 1. Explicit over ambient

Authority, dependencies, ownership, resource consumption, lifecycle, and state transitions SHOULD be explicit and inspectable.

## 2. Capability-based authority

Possession of an unforgeable capability, rather than a global identity or administrator status, SHOULD be the native basis for authority.

Capabilities SHOULD be narrow, delegable, revocable where practical, and optionally time- or resource-bounded.

## 3. Typed resources instead of everything-is-a-file

The system SHOULD model storage, network endpoints, devices, timers, memory regions, services, and other objects as typed resources with explicit interfaces.

Uniformity SHOULD come from common resource and messaging semantics rather than forcing every object through read/write/ioctl semantics.

## 4. Explicit component lifecycle

Execution SHOULD be represented by components with explicit parents, children, resources, capabilities, and termination semantics.

Unstructured orphan execution SHOULD not be the default.

## 5. Message passing before shared mutable state

Component isolation and typed message passing SHOULD be the default communication model.

Shared memory MAY be available as an explicit capability when performance requires it.

## 6. Transactional state change

Where practical, persistent state changes SHOULD be atomic and observable as explicit transitions. Applications should not need to reconstruct transactional semantics from sequences of unrelated filesystem operations.

## 7. Immutable system and packages

System software and application packages SHOULD be immutable and content-identifiable where practical. Updates SHOULD be atomic and rollback SHOULD be a normal operation rather than a recovery technique of last resort.

## 8. Structured observability

Events SHOULD be structured data with identity, time, origin, cause, and correlation information. Machine-readable observability is a system primitive, not an optional logging convention.

## 9. Resource budgets are first-class

CPU, memory, storage, I/O, network, GPU, energy, and similar finite resources SHOULD be explicitly allocatable and inspectable.

## 10. Compatibility is isolated

Historical compatibility MUST NOT silently become the native semantic model. POSIX/Linux compatibility SHOULD live in a compatibility subsystem.

## 11. AI is not trusted kernel logic

Probabilistic AI SHOULD NOT be required for kernel correctness. The OS SHOULD instead expose explicit, inspectable and bounded capabilities that allow AI agents to operate safely.

## 12. Validate semantics before building a kernel

The project SHOULD validate its computing model through a reference runtime and real workloads before committing to a native kernel architecture.
