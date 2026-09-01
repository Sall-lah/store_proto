## Why

The `store_proto` repository serves as the central contract repository defining Protocol Buffer schemas and generated Go gRPC stubs across all store microservices (such as `store_order` and `store_user`). Currently, the repository lacks a comprehensive `README.md` explaining the service contracts, Buf CLI build workflows, inter-service architecture, and downstream module integration. Adding a structured, production-grade README matching the ecosystem documentation standard ensures seamless onboarding, consistent gRPC contract governance, and clear developer workflows.

## What Changes

- Add a comprehensive `README.md` at the repository root structured with standard shields/badges, architecture overview, proto service catalogs, Buf configuration details, local generation workflows, and client consumption guides.
- Document the `OrderService` (`CheckActiveOrders`) and `OrderStatus` contracts along with the account deletion lifecycle state machine and gRPC validation guarantees.
- Document Buf v2 workspace linting, breaking change detection, and Go code generation steps.

## Capabilities

### New Capabilities
- `repository-documentation`: Comprehensive repository documentation and gRPC contract catalog covering Buf tooling, code generation, and inter-service integration patterns for `store_proto`.

### Modified Capabilities
<!-- No requirement changes to existing specs -->

## Impact

- **Documentation**: New `README.md` in repository root.
- **Code/Protobuf**: No changes to existing `.proto` definitions or generated code.
- **Dependencies**: No new external dependencies.
