## ADDED Requirements

### Requirement: Root README Documentation
The repository SHALL contain a comprehensive `README.md` file at the root directory documenting the `store_proto` repository structure, architecture, service contracts, Buf tooling, and client usage patterns.

#### Scenario: Visual badges and project summary
- **WHEN** a developer views `README.md`
- **THEN** it SHALL display version badges for Go (1.26+), Protocol Buffers (v3), Buf CLI (v2), and gRPC Go (v1.71.0), followed by an executive project summary.

#### Scenario: Architecture overview and diagrams
- **WHEN** a developer reads the Architecture Overview section
- **THEN** it SHALL include Mermaid diagrams illustrating the central proto repository architecture, inter-service gRPC communication (`store_user` to `store_order`), and the `OrderStatus` lifecycle state machine differentiating active blocking states from terminal states.

#### Scenario: Service catalog and schema specifications
- **WHEN** a developer reviews the Proto Packages and Service Catalog
- **THEN** it SHALL provide exact definitions and tables for `store.order.v1.OrderService` (`CheckActiveOrders`), `CheckActiveOrdersRequest`, `CheckActiveOrdersResponse`, `ActiveOrderSummary`, and the `OrderStatus` enum.

#### Scenario: Buf tooling and local development workflows
- **WHEN** a developer follows the Local Development and Code Generation guide
- **THEN** it SHALL detail prerequisites (`buf`, `protoc-gen-go`, `protoc-gen-go-grpc`) and explicit commands for `buf lint`, `buf breaking`, and `buf generate`.

#### Scenario: Downstream Go package integration
- **WHEN** a developer integrates generated protobuf stubs into a downstream microservice
- **THEN** `README.md` SHALL demonstrate how to install `github.com/Sall-lah/store_proto` via `go get` and import `github.com/Sall-lah/store_proto/gen/go/store/order/v1` for client and server implementations.
