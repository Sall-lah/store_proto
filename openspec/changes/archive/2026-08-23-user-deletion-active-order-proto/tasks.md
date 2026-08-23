## 1. Workspace Configuration & Setup

- [x] 1.1 Create `buf.yaml` configuring Buf workspace module and lint rules
- [x] 1.2 Create `buf.gen.yaml` configuring Go and gRPC stub generation
- [x] 1.3 Initialize `go.mod` for Go module `github.com/Sall-lah/store_proto`

## 2. Order Protobuf Definitions

- [x] 2.1 Create `proto/store/order/v1/order_status.proto` with `OrderStatus` enum covering all lifecycle states
- [x] 2.2 Create `proto/store/order/v1/order_service.proto` with `OrderService`, `CheckActiveOrders` RPC, and associated request/response messages

## 3. Validation & Generation

- [x] 3.1 Run `buf lint` and `buf build` to verify proto correctness and compliance
- [x] 3.2 Run `buf generate` to generate Go client/server stubs under `gen/go/store/order/v1`
