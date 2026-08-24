## 1. Protobuf Comments & Linting

- [x] 1.1 Update doc comments in `proto/store/order/v1/order_service.proto` to clarify that `CheckActiveOrders` is strictly for user-initiated account deletion and not used for admin bans.
- [x] 1.2 Run `buf lint` to verify proto schema syntax and comments pass all lint checks.
