# ADR-0002: Capability-based authority

- Status: Proposed
- Date: 2026-08-29

## Context

Traditional identity/ACL models frequently create ambient authority: code inherits broad permissions because of the user or administrator under which it executes. This makes least privilege, delegation, sandboxing, and safe agent execution difficult.

## Decision

Native authority is represented by explicit capabilities to resources and operations.

Identity may inform policy and capability issuance, but identity alone does not constitute authority.

The system should support attenuation/delegation of capabilities and should investigate revocation, expiry and quota constraints.

## Consequences

- dependencies and privileges become inspectable;
- applications and AI agents can receive narrowly scoped authority;
- application design must explicitly pass capabilities rather than rely on ambient access;
- capability lifecycle and revocation become core design problems.

## Validation

The Linux reference runtime must demonstrate that a component without a network capability cannot establish arbitrary network communication even when compromised at application level, subject to limitations of the host platform.
