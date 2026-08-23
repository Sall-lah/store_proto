## ADDED Requirements

### Requirement: Order Status Lifecycle Enumeration
The protobuf schema SHALL define an `OrderStatus` enum under `proto/store/order/v1/order_status.proto` capturing all possible states in the order lifecycle.

#### Scenario: OrderStatus enum values
- **WHEN** the enum `OrderStatus` is compiled
- **THEN** it MUST define `ORDER_STATUS_UNSPECIFIED = 0`, `ORDER_STATUS_PENDING_PAYMENT = 1`, `ORDER_STATUS_PAID = 2`, `ORDER_STATUS_PROCESSING = 3`, `ORDER_STATUS_SHIPPED = 4`, `ORDER_STATUS_COMPLETED = 5`, `ORDER_STATUS_CANCELLED = 6`, and `ORDER_STATUS_EXPIRED = 7`.

### Requirement: Check Active Orders RPC Endpoint
The `OrderService` protobuf service SHALL expose a `CheckActiveOrders` unary RPC endpoint under `proto/store/order/v1/order_service.proto` accepting `CheckActiveOrdersRequest` and returning `CheckActiveOrdersResponse`.

#### Scenario: Request contains target user identifier
- **WHEN** a client constructs `CheckActiveOrdersRequest`
- **THEN** it SHALL provide the unique string `user_id` field.

#### Scenario: Response when active orders exist
- **WHEN** the requested user has one or more orders in `PENDING_PAYMENT`, `PAID`, `PROCESSING`, or `SHIPPED` status
- **THEN** `CheckActiveOrdersResponse` SHALL set `has_active_orders` to `true`, `active_order_count` to the count of matching active orders, and populate `active_orders` with summary entries containing `order_id`, `order_number`, `status`, `total_amount`, and `created_at`.

#### Scenario: Response when no active orders exist
- **WHEN** the requested user has no orders or only orders in terminal states (`COMPLETED`, `CANCELLED`, `EXPIRED`)
- **THEN** `CheckActiveOrdersResponse` SHALL set `has_active_orders` to `false`, `active_order_count` to `0`, and `active_orders` to an empty list.
