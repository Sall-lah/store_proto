## Context

In our microservices architecture, account lifecycle events are categorized into:
1. **User Self-Service Lifecycle**: Actions initiated by end-users (e.g., self-service account deletion) where data loss or orphaned orders must be prevented synchronously before execution.
2. **Administrative Security Enforcement**: Actions initiated by administrators (e.g., fraud prevention, user bans/suspensions) where immediate revocation is required, and any remaining order lifecycle adjustments occur asynchronously via Kafka event streams.

Previously, protobuf and capability specifications generally referenced "account lifecycle transitions" without explicitly stating that admin bans bypass the gRPC `CheckActiveOrders` RPC. This change updates documentation and specs to formalize this boundary.

## Goals / Non-Goals

**Goals:**
- Update `proto/store/order/v1/order_service.proto` comments to explicitly document that `CheckActiveOrders` is strictly for user-initiated deletion and not admin bans.
- Update `order-active-check` capability specifications to document the exclusion of admin bans from gRPC pre-flight checking.

**Non-Goals:**
- Changing protobuf field IDs, types, message structures, or RPC signatures.
- Modifying order state machine enums (`OrderStatus`).
- Implementing Kafka consumers or handlers in service codebases.

## Decisions

### Decision 1: Explicit Documentation over Interface Mutation
- **Choice**: Clarify semantics via protobuf documentation comments and OpenSpec specifications without altering RPC message signatures.
- **Rationale**: The current protobuf interface (`CheckActiveOrdersRequest`/`CheckActiveOrdersResponse`) is syntactically sound and already deployed. Clarifying the usage contract in comments and specs prevents misapplication in admin workflows while avoiding unnecessary binary/schema breaking changes.

## Risks / Trade-offs

- **[Risk] Developer misinterprets user deletion handler patterns for admin ban endpoints** → Admin ban endpoints in User Service might block bans if active orders exist.
  - *Mitigation*: Proto doc comments and OpenSpec specifications clearly document that admin bans must bypass gRPC and rely on asynchronous events.
