# Novelty Landscape and Prior Art

Status: research note, August 2026

This note evaluates the current design against prior capability systems, microkernels, language-safe operating systems, component models, immutable systems, and agent runtimes.

## Conclusion

Most individual mechanisms proposed by this project are **not novel by themselves**.

Capability-based authority, microkernels, user-space drivers, explicit process creation, typed IPC, isolated components, immutable/content-addressed software, atomic rollback, declarative configuration, structured concurrency, language-enforced resource ownership, and sandboxed AI agents all have substantial prior art.

The project should therefore avoid presenting these mechanisms as inventions.

The strongest research direction is narrower:

> Can authority, ownership, lifecycle, resource budgets, typed interaction, and observable state transitions be expressed as one coherent native computing model, while POSIX remains an explicit compatibility subsystem rather than the semantic center of the design?

The project is best treated as a **systems-integration and semantic-coherence experiment** until stronger evidence supports a narrower novelty claim.

## Classification

| Idea | Assessment | Representative prior art |
|---|---|---|
| POSIX as compatibility rather than native semantic center | Established | Plan 9, Singularity, research OSes, compatibility personalities/subsystems |
| Capability-based authority / removal of ambient root authority | Established | KeyKOS, EROS, Coyotos, seL4, Capsicum, Genode |
| Component as execution + authority + lifecycle + accounting unit | Established combination | Singularity SIPs, Fuchsia jobs/components, container and actor runtimes |
| Explicit `spawn` instead of native `fork` | Established | non-Unix OSes, microkernels, WASI runtimes |
| Structured parent/child lifecycle | Established | structured concurrency, Erlang/OTP supervision, job/process hierarchies |
| Typed resources | Established | object-capability systems, Fuchsia handles, WASI interfaces |
| Typed IPC | Established | Singularity contracts, IDL-based microkernels, WebAssembly Component Model/WIT |
| Message passing before shared mutable memory | Established | CSP, actors, microkernels, Singularity, WASM components |
| Resource budgets as first-class | Established combination | cgroups, containers, schedulers, quotas, Fuchsia jobs |
| Immutable/content-addressed system and packages | Established | Nix/NixOS, Guix, OSTree, container images |
| Atomic update and rollback | Established | NixOS, OSTree, A/B update systems |
| Desired-state configuration | Established | NixOS, Kubernetes controllers, configuration-management systems |
| Structured events / causal observability | Established combination | tracing, audit systems, distributed tracing, event sourcing |
| User-space services/drivers over a small kernel | Established | L4/seL4, QNX, MINIX 3, Genode |
| AI agents as bounded ordinary components | Established combination, active area | agent sandboxes and agent-OS runtimes |
| Rust ownership/lifetimes generalized to OS resources | Differentiation remains possible | Theseus, Redox, Asterinas, Rust affine-resource research |
| Linux-hosted semantic runtime before a kernel | Established engineering strategy | sandbox runtimes, WASI hosts, language/runtime prototypes |

## Important lineages

### Capability systems

Capability security has a long history. KeyKOS and EROS developed object-capability operating-system models; Coyotos continued that lineage. seL4 combines a small microkernel with capability-based access control and formal verification. Capsicum retrofits capability mode into FreeBSD and deliberately removes access to ambient global namespaces.

Implication: capability security is prior art. The project needs precise semantics for delegation, attenuation, revocation, expiry, ownership, bootstrap authority, administration, and recovery.

References:

- https://docs.sel4.systems/Tutorials/capabilities.html
- https://sel4.systems/Info/Docs/seL4-manual-latest.pdf
- https://man.freebsd.org/cgi/man.cgi?query=capsicum&sektion=4
- http://www.eros-os.org/

### Language-safe and typed operating systems

Microsoft Research's Singularity explored software-isolated processes, contract-based channels, and manifest-described programs. Theseus explicitly uses Rust's affine type system as an OS design mechanism. Asterinas uses Rust safety boundaries while preserving Linux ABI compatibility.

Implication: "Rust ideas applied to OS design" is already established. A potentially stronger question is whether cross-component authority delegation and component lifetime can obey ownership-like invariants that are mechanically checkable.

References:

- https://www.microsoft.com/en-us/research/publication/singularity-rethinking-the-software-stack/
- https://github.com/theseus-os/Theseus
- https://asterinas.github.io/

### WASI and the WebAssembly Component Model

The WebAssembly Component Model and WIT define typed imported/exported interfaces. WASI exposes capabilities supplied by the host. This overlaps directly with typed resources, typed interaction, explicit dependencies, and bounded authority.

Implication: WASI/Component Model should be a major comparison target, not merely an implementation option.

References:

- https://component-model.bytecodealliance.org/design/interfaces.html
- https://component-model.bytecodealliance.org/design/components.html
- https://github.com/WebAssembly/WASI/blob/main/docs/Capabilities.md

### Immutable and declarative systems

Nix/NixOS and OSTree already provide strong precedents for immutable/content-addressed software, declarative configuration, atomic deployment, and rollback.

Implication: these are established mechanisms. The useful question is whether they become simpler or safer when tied to the same authority/ownership/lifecycle model as runtime execution.

References:

- https://nixos.org/guides/how-nix-works/
- https://wiki.nixos.org/wiki/Overview_of_the_NixOS_Linux_distribution
- https://ostreedev.github.io/ostree/introduction/
- https://ostreedev.github.io/ostree/atomic-upgrades/

## Recommended research hypotheses

### Hypothesis A — One lifetime tree governs execution, authority, resources, and budgets

A component tree should not be only a process hierarchy. Delegated capabilities, resource reservations, cancellation, failure propagation, and accounting should derive from the same ownership/lifetime structure.

Candidate invariant:

> A child cannot outlive, retain more authority than, or continue consuming resources reserved by an ancestor unless an explicit transfer operation changes ownership.

This is significantly more precise than "a modern capability OS" and can be tested on Linux.

### Hypothesis B — Typed capability interfaces are the native application ABI

Rather than starting from a syscall list, application dependencies are described as typed imported resource interfaces plus capabilities authorizing concrete instances or operations.

Candidate invariant:

> An application's external authority is derivable from its declared imports plus explicitly delegated runtime capabilities.

This must be compared directly with WASI Component Model, seL4 capability spaces, Fuchsia-style handles, and service-oriented microkernel designs.

### Hypothesis C — POSIX compatibility does not create native authority

Legacy applications may execute in a compatibility subsystem, but compatibility semantics must not manufacture authority unavailable in the native model.

Candidate invariant:

> POSIX emulation may translate operations, but it cannot create access to resources not granted by the native host.

## Recommended positioning

Avoid:

- "a completely new operating-system model";
- "the first OS for AI agents";
- "capabilities replace outdated Unix security";
- "Rust ownership applied to an OS is new";
- "typed IPC, immutable systems, or no-fork semantics are novel".

Prefer:

> This project is an experimental synthesis of established systems ideas. Its research goal is to test whether authority, ownership, lifecycle, resource accounting, typed interaction, and state transitions can share one coherent explicit model.

And:

> POSIX is treated as a compatibility target rather than a native design constraint. The replacement model is validated in a Linux-hosted reference runtime before any commitment to a native kernel.

## Research discipline

Before claiming novelty for a design proposal, document:

1. the closest prior systems and papers;
2. the exact semantic difference;
3. a falsifiable hypothesis;
4. an experiment or proof obligation;
5. failure conditions that would cause revision or abandonment.

The burden of proof is on the new abstraction, not on the legacy abstraction it intends to replace.
