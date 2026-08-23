## Context

The store backend platform is migrating inter-service communication to gRPC using this central protobuf repository (`store_proto`). The User Service handles user profile lifecycle, while the Order Service manages checkout, fulfillment, and payment state machines. When a user requests account deletion (via the API Gateway with offloaded authentication), the User Service must query the Order Service to verify if the user has any ongoing or active orders before proceeding.

Active orders are defined as any order whose status is not yet settled or completed:
- `PENDING_PAYMENT` (checkout created, awaiting payment)
- `PAID` (payment settled, awaiting fulfillment)
- `PROCESSING` (order being prepared)
- `SHIPPED` (order with courier in transit)

Terminal states where deletion is allowed:
- `COMPLETED`
- `CANCELLED`
- `EXPIRED`

## Goals / Non-Goals

**Goals:**
- Provide a clean, versioned protobuf structure under `proto/store/order/v1/` for order statuses and service RPCs.
- Define a high-performance synchronous gRPC method `OrderService.CheckActiveOrders` returning structured validation status (`has_active_orders`, `active_order_count`, `active_orders` summaries, and explanation message).
- Standardize Buf workspace configuration (`buf.yaml` and `buf.gen.yaml`) with Go code generation plugins (`protoc-gen-go`, `protoc-gen-go-grpc`).
- Facilitate downstream event-driven deletion flow (User Service deletes `user_profile` table record and emits `user.deleted` on Kafka topic `user.events` for Auth Service and Order Service to consume).

**Non-Goals:**
- Implementing the Go/gRPC server handlers in `store_order` or client logic in `store_user` (this repository only houses proto definitions and generated stubs).
- Managing Kafka message schemas or schema registry in this change (focused specifically on gRPC).
- Implementing full CRUD order gRPC endpoints beyond the active check service contract.

## Decisions

### Decision 1: Structured Predicate Response over gRPC Error Status
- **Choice**: Return `CheckActiveOrdersResponse` with `bool has_active_orders` and `repeated ActiveOrderSummary active_orders`.
- **Alternatives Considered**:
  - *Throwing gRPC `FAILED_PRECONDITION`*: Requires client code to catch gRPC status errors and unpack error metadata details.
  - *Returning boolean only*: Loses context on which specific orders are blocking the deletion.
- **Rationale**: A structured response clearly separates transport/infrastructure failures (e.g. `UNAVAILABLE`, `DEADLINE_EXCEEDED`) from valid business outcomes where deletion is disallowed due to active orders.

### Decision 2: Modular Enum and Service Protobuf Files
- **Choice**: Separate `order_status.proto` from `order_service.proto` under `proto/store/order/v1/`.
- **Alternatives Considered**:
  - *Single monolithic `order.proto`*: Makes imports difficult and causes tight coupling as more order endpoints are added.
- **Rationale**: Modular files keep entity definitions reusable across multiple services and simplify incremental evolution.

### Decision 3: Buf CLI for Tooling and Generation
- **Choice**: Use Buf (`buf.yaml` and `buf.gen.yaml`) with standard linting rules (`DEFAULT`) and breaking change detection (`FILE`).
- **Alternatives Considered**:
  - *Custom shell scripts / Makefiles with raw `protoc`*: Prone to environment differences and missing plugin binaries across developer machines.
- **Rationale**: Buf provides consistent generation and linting across teams and CI pipelines.

## Risks / Trade-offs

- **[Risk] Race condition between check and new order creation** → User could initiate an order immediately after `CheckActiveOrders` returns `false` before deletion executes.
  - *Mitigation*: User Service immediately deactivates the user session/profile, and Order Service checkout validates user active status via JWT or identity check.
- **[Risk] Long-running network timeout when querying Order Service** → User deletion API blocks or fails unexpectedly.
  - *Mitigation*: Client in User Service must set a strict gRPC deadline/timeout (e.g., 2 seconds) and fail safely.
- **[Risk] Future order status additions altering active check logic** → New order status enum values might not be properly categorized as active vs terminal.
  - *Mitigation*: Order Service implementation must explicitly whitelist terminal statuses rather than blacklisting active ones.

## Migration Plan

1. Merge proto definitions in `store_proto` and publish Go stub package `github.com/Sall-lah/store_proto/gen/go/store/order/v1`.
2. Update `store_order` to import the generated stubs and implement the `OrderServiceServer.CheckActiveOrders` gRPC handler.
3. Update `store_user` to import the generated client stub and invoke `CheckActiveOrders` in its user deletion handler prior to emitting `user.deleted`.
