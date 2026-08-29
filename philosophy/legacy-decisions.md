# Legacy Design Decisions

This document records historical abstractions that remain useful for compatibility but are not assumed to be ideal native primitives for a new system.

The intent is not to dismiss historical designs. Each solved real constraints and many remain excellent interfaces. The question is whether they should constrain a system designed from first principles today.

| Historical model | Native direction | Rationale |
|---|---|---|
| process as primary unit | component + execution context | make lifecycle, ownership and resources explicit |
| `fork()` + `exec()` | explicit `spawn()` | avoid implicit address-space and authority inheritance |
| signals | typed events / cancellation | explicit semantics and payloads |
| file descriptor as universal handle | typed capability handle | preserve uniform handles without erasing resource types |
| pathname as identity/access route | object identity + human-readable namespace + capability | separate naming from identity and authority |
| `root` / ambient administrator authority | delegated capability bundles | least authority and bounded administration |
| UID/GID as primary authorization | principals plus explicit capabilities | identity and authority are related but distinct |
| everything is a file | everything is a typed resource | avoid escape hatches such as broad ioctl-style interfaces |
| shared mutable memory by default | isolated components + message passing | reduce implicit coupling and concurrency hazards |
| mutable root filesystem | immutable system images | reproducibility, atomic update and rollback |
| in-place package installation | immutable/content-addressed packages | deterministic identity and dependency visibility |
| text logs | structured event stream | machine-readable observability and causality |
| shell text pipeline as system IPC | typed IPC | stable machine interfaces; text pipelines remain useful for humans |
| POSIX as core contract | compatibility subsystem | preserve ecosystem without freezing the native model |

## Evaluation rule

A historical abstraction is not rejected because it is old. Replacement requires a concrete argument covering:

1. the original problem it solved;
2. why that solution became successful;
3. the modern failure mode or limitation;
4. the proposed replacement;
5. compatibility consequences;
6. an experiment capable of disproving the replacement.
