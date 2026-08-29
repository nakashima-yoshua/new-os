# Architecture — Specification v0.1

## Logical architecture

```text
+--------------------------------------------------+
| Applications                                     |
| Native Components / Linux Compatibility / Agents |
+--------------------------------------------------+
| Component Runtime                                |
| lifecycle / delegation / budgets / supervision  |
+--------------------------------------------------+
| System Services                                  |
| storage / network / identity / UI / update      |
| observability / device services                 |
+--------------------------------------------------+
| Typed IPC / Capability Enforcement               |
+--------------------------------------------------+
| Minimal Trusted Kernel                           |
| scheduler / virtual memory / IPC / capabilities |
| interrupts / timers                             |
+--------------------------------------------------+
| Hardware                                         |
+--------------------------------------------------+
```

This is a target decomposition, not yet an implementation commitment.

## Trusted computing base

The long-term architecture SHOULD minimize privileged code. Where hardware permits, drivers and higher-level system services SHOULD execute outside the kernel and receive only the device/memory capabilities required for their function.

IOMMU and similar hardware isolation SHOULD be used where available.

A service or driver failure SHOULD be restartable without requiring whole-system failure whenever its state model permits recovery.

## System software

System images SHOULD be immutable. Updates SHOULD construct or obtain a new image, verify it, atomically switch the active target, and preserve a rollback path.

Applications SHOULD similarly be represented as immutable packages with explicit manifests.

## Configuration

Configuration SHOULD be represented as desired state where practical. A reconciliation mechanism MAY converge current state toward desired state rather than requiring imperative edits followed by manually coordinated restarts.

## Compatibility architecture

Linux/POSIX applications SHOULD execute through a compatibility boundary:

```text
Native model
   |
   +-- Native Components
   |
   +-- Linux/POSIX Compatibility Subsystem
   |
   +-- Virtualized compatibility environments (optional)
```

Compatibility code SHOULD translate historical semantics into native capabilities/resources without forcing those semantics into the kernel API.

## AI agent interaction

AI agents are ordinary components, not privileged operating-system authorities.

An agent SHOULD receive a bounded set of capabilities appropriate to its task. Example:

```text
Intent: organize documents
Capabilities:
  - read: documents collection
  - write: staging collection
  - rename: documents collection
Denied:
  - delete
  - arbitrary network access
```

Every action remains subject to normal capability checks and structured audit events.

## Reference implementation strategy

Before implementing a kernel, build a Linux-hosted reference runtime capable of expressing the semantic model.

Candidate host mechanisms include:

- namespaces;
- cgroups;
- seccomp;
- Landlock;
- Unix sockets;
- containers;
- WASM runtimes.

Selection is an implementation decision and does not define the specification.

### Initial CLI experiment

A possible developer experience is:

```text
os-run app \
  --grant storage:/documents:read \
  --grant network:api.example.com \
  --memory 512M \
  --cpu 10%
```

The experiment succeeds only if the runtime can demonstrate that authority and resource boundaries are enforceable, inspectable, and understandable in real applications.

## Validation sequence

1. Define semantics and invariants.
2. Implement the smallest Linux reference runtime.
3. Run real workloads.
4. Measure usability, security boundaries, overhead, and failure recovery.
5. Revise or reject abstractions that do not survive experiments.
6. Only then define a native runtime/kernel interface.

The project should prefer falsifiable experiments over architectural completeness.
