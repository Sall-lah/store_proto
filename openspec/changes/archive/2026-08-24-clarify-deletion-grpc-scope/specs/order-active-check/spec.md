## MODIFIED Requirements

### Requirement: Check Active Orders RPC Endpoint
The `OrderService` protobuf service SHALL expose a `CheckActiveOrders` unary RPC endpoint under `proto/store/order/v1/order_service.proto` accepting `CheckActiveOrdersRequest` and returning `CheckActiveOrdersResponse` exclusively for user-initiated account deletion validation. Administrative bans and suspensions SHALL NOT utilize this RPC.

#### Scenario: Request contains target user identifier
- **WHEN** a client constructs `CheckActiveOrdersRequest` during user-initiated account deletion
- **THEN** it SHALL provide the unique string `user_id` field.

#### Scenario: Response when active orders exist
- **WHEN** the requested user has one or more orders in `PENDING_PAYMENT`, `PAID`, `PROCESSING`, or `SHIPPED` status
- **THEN** `CheckActiveOrdersResponse` SHALL set `has_active_orders` to `true`, `active_order_count` to the count of matching active orders, and populate `active_orders` with summary entries containing `order_id`, `order_number`, `status`, `total_amount`, and `created_at`.

#### Scenario: Response when no active orders exist
- **WHEN** the requested user has no orders or only orders in terminal states (`COMPLETED`, `CANCELLED`, `EXPIRED`)
- **THEN** `CheckActiveOrdersResponse` SHALL set `has_active_orders` to `false`, `active_order_count` to `0`, and `active_orders` to an empty list.

#### Scenario: Admin ban lifecycle exclusion
- **WHEN** an administrator initiates a user ban or account suspension
- **THEN** the User/Admin Service SHALL NOT call `CheckActiveOrders` and SHALL execute the ban immediately, dispatching asynchronous lifecycle events.
