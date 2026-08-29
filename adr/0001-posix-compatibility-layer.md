# ADR-0001: POSIX is a compatibility layer

- Status: Proposed
- Date: 2026-08-29

## Context

POSIX provides an enormous software ecosystem and remains valuable for compatibility. However, APIs such as fork/exec, signals, path-oriented authority, file-descriptor conventions, and ambient process state impose historical semantics on new system design.

## Decision

POSIX will not define the native new-os execution or authority model.

POSIX/Linux compatibility should be implemented through a compatibility subsystem that translates legacy operations into native Component, Capability, Resource, Message and State semantics.

## Consequences

Positive:

- native interfaces can evolve without POSIX constraints;
- historical applications remain a viable migration path;
- compatibility complexity is isolated.

Negative:

- translation has performance and semantic costs;
- some POSIX behavior may be difficult to reproduce exactly;
- compatibility becomes a substantial subsystem in its own right.

## Validation

A reference runtime should demonstrate at least one non-trivial Linux/POSIX application operating through a bounded compatibility environment without granting unrestricted native authority.
