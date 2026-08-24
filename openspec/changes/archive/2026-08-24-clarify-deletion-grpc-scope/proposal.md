## Why

Inter-service communication semantics between User Service and Order Service require explicit boundary definitions. The `OrderService.CheckActiveOrders` gRPC endpoint is designed strictly for user-initiated self-service account deletion pre-checks to avoid orphaning in-flight orders. Administrative bans and suspensions are security enforcements that take immediate effect and operate asynchronously via Kafka event streams, completely bypassing synchronous gRPC validation. Updating the protobuf doc comments and specifications documents this architectural boundary clearly.

## What Changes

- Update protobuf comments in `proto/store/order/v1/order_service.proto` to explicitly document that `CheckActiveOrders` is only used for user-initiated account deletion and is not used for admin bans.
- Update `order-active-check` spec to define the exclusive applicability of pre-deletion order checks to self-service user deletion.

## Capabilities

### New Capabilities
<!-- No new capabilities -->

### Modified Capabilities
- `order-active-check`: Clarify that `CheckActiveOrders` RPC validation is restricted to user self-deletion workflows and explicitly excluded from admin bans.

## Impact

- **Protobuf Definitions**: `proto/store/order/v1/order_service.proto` comments updated. No wire-format or schema breaking changes.
- **Specifications**: Delta specification created for `order-active-check`.
- **Downstream Services**: Guarantees alignment across User Service and Admin Service implementations.
