# ADR-0004: Typed IPC for native services

- Status: Proposed
- Date: 2026-08-29

## Context

Byte streams and textual protocols are flexible, but system interfaces built around ad-hoc parsing make contracts, versioning, authorization, tooling and machine reasoning unnecessarily implicit.

## Decision

Native service IPC will use typed messages with explicit schemas and interaction forms.

Request/response, event, cancellation, failure and streaming should be represented deliberately rather than inferred from arbitrary payload conventions.

The specification does not yet choose a schema language or wire format.

## Consequences

- interfaces become inspectable and toolable;
- capability checks can be associated with operations;
- versioning must be designed explicitly;
- schema evolution becomes a compatibility concern;
- human-oriented text pipelines remain useful but are not the native service contract.

## Validation

Implement two isolated services in the reference runtime with a versioned typed interface, capability-checked operations, cancellation and structured failure reporting. Compare implementation complexity and observability with an equivalent ad-hoc byte/text protocol.
