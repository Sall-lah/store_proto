## Why

When a user requests account deletion in the User Service, the system must ensure that the user does not have any active, in-flight, or pending orders in the Order Service. Without this guard, account deletion could result in orphaned orders, payment reconciliation failures, and undeliverable shipments. Establishing a standardized gRPC contract between User Service and Order Service provides a synchronous, strongly-typed pre-deletion check.

## What Changes

- Introduce Buf workspace configuration (`buf.yaml`, `buf.gen.yaml`) for proto linting, breaking change detection, and code generation stubs.
- Define `OrderStatus` enumeration covering full lifecycle states (`PENDING_PAYMENT`, `PAID`, `PROCESSING`, `SHIPPED`, `COMPLETED`, `CANCELLED`, `EXPIRED`).
- Define `OrderService` gRPC service contract with `CheckActiveOrders` RPC endpoint in `proto/store/order/v1/order_service.proto`.
- Define `CheckActiveOrdersRequest` and structured `CheckActiveOrdersResponse` payloads to return active order status, counts, order summaries, and human-readable messages.

## Capabilities

### New Capabilities
- `order-active-check`: Defines gRPC service contract and message schemas for evaluating whether a user has active or in-flight orders blocking account deletion.
- `proto-workspace-config`: Sets up Buf workspace layout, code generation rules, and protobuf directory conventions for `store_proto`.

### Modified Capabilities
<!-- No existing specs to modify -->

## Impact

- **Protobuf Schemas**: Adds `proto/store/order/v1/order_status.proto` and `proto/store/order/v1/order_service.proto`.
- **Downstream Services**:
  - `store_order`: Implements the `OrderService.CheckActiveOrders` gRPC server.
  - `store_user`: Implements the gRPC client to call `CheckActiveOrders` before proceeding with `user_profile` deletion and Kafka `user.deleted` event dispatch.
- **Dependencies & Tooling**: Uses Buf CLI for building and generating Go stubs.
