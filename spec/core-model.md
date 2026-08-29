# Core Computing Model — Specification v0.1

Status: **Exploratory / non-normative where unspecified**

## 1. Purpose

The model describes computation in terms of explicit subjects, authority, resources, interactions, and state transitions. It is intended to span kernel-level resources and higher-level services without requiring all layers to share the same implementation.

## 2. Component

A **Component** is an isolated unit of execution, authority, resource accounting, and lifecycle.

A component has, at minimum:

- stable runtime identity;
- lifecycle state;
- zero or more capabilities;
- a resource budget;
- communication endpoints;
- parent/ownership relationship unless explicitly detached by policy.

Initial lifecycle:

```text
Created -> Starting -> Running -> Stopping -> Terminated
                    \-> Failed
```

Creation SHOULD be explicit rather than based on cloning a running component.

Conceptual operation:

```text
spawn(package, capabilities, budget, arguments) -> ComponentHandle
```

## 3. Resource

A **Resource** is a typed object exposed through a defined interface.

Examples:

```text
Storage.Object
Storage.Volume
Network.Endpoint
Memory.Region
Device.Camera
Display.Surface
Timer
Service.Endpoint
```

A resource has an identity independent from a human-readable path or alias.

## 4. Capability

A **Capability** is an unforgeable handle representing authority over a resource or operation.

Conceptually:

```text
Capability {
    resource
    operations
    constraints
    delegation-policy
}
```

Possible constraints include:

- read-only / write-only / operation subset;
- endpoint or namespace restriction;
- expiration time;
- resource quota;
- delegation depth;
- transaction scope.

Capabilities SHOULD follow least-authority principles.

## 5. Message

A **Message** is a typed interaction sent through an authorized endpoint.

Messages SHOULD have explicit schemas. Request/response, event notification, cancellation, failure, and streaming are distinct interaction forms rather than conventions encoded in arbitrary byte strings.

Conceptual example:

```text
Storage.Read {
    object: ObjectCapability,
    range: Range
} -> Bytes
```

## 6. State

**State** is the observable condition of a resource, component, or service.

State mutation SHOULD occur through explicit operations and SHOULD produce observable events where security and performance permit.

Conceptual transition:

```text
(Current State, Authorized Action) -> (New State, Event)
```

## 7. Event

An **Event** records an occurrence relevant to state, lifecycle, security, resource consumption, or interaction.

Events SHOULD support:

- event type;
- timestamp;
- source identity;
- correlation/trace identity;
- causal relationship where available;
- structured payload.

## 8. Resource Budget

A component MAY receive explicit budgets for finite resources.

Initial resource classes include:

- CPU time/share;
- memory;
- persistent storage;
- I/O operations/bandwidth;
- network bandwidth;
- GPU resources;
- energy where supported.

Exceeding a budget MUST have defined semantics rather than relying on accidental system exhaustion.

## 9. Naming, identity and authority

The model separates three concerns:

```text
Name != Identity != Authority
```

A human-readable path MAY resolve to a resource identity, but possession of the name MUST NOT by itself imply authority to access that resource.

## 10. Shared memory

Shared memory MAY exist, but access MUST be explicitly granted as a capability. Shared mutable memory is not assumed to be the default communication mechanism between isolated components.

## 11. Persistence and transactions

Persistent services SHOULD expose transactional state-change operations where consistency requires them. Atomicity SHOULD be provided at the semantic operation boundary rather than reconstructed from unrelated file operations whenever practical.

## 12. Open questions

Specification v0.1 intentionally leaves unresolved:

- exact capability representation and revocation mechanism;
- IPC wire format and schema language;
- global vs local resource identity;
- persistence model and content-addressing details;
- scheduling policy;
- detached/background component semantics;
- distributed capability semantics;
- GUI composition model;
- package manifest format;
- compatibility subsystem ABI.

These should be resolved by ADRs and experiments rather than assumed prematurely.
