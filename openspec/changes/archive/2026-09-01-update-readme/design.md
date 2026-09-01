## Context

The `store_proto` repository contains the centralized Protocol Buffer contracts and generated Go client/server stubs for the e-commerce microservices platform. Currently, the repository lacks a top-level `README.md`. To provide consistency with the rest of the ecosystem (such as `store_order`), a production-grade `README.md` must be created following the provided architectural documentation template.

## Goals / Non-Goals

**Goals:**
- Create a comprehensive `README.md` in `store_proto` following the ecosystem template.
- Include technology badges (Go 1.26+, Protobuf v3, Buf CLI v2, gRPC Go v1.71.0).
- Include an Architecture Overview with Mermaid diagrams illustrating inter-service communication (e.g. `store_user` synchronously calling `store_order` via `OrderService.CheckActiveOrders` during user account deletion, vs asynchronous Kafka events for admin bans).
- Detail the `OrderStatus` lifecycle state machine and highlight active blocking vs terminal states.
- Document the service catalog, request/response schemas, and error behavior.
- Document Buf workspace configuration (`buf.yaml`, `buf.gen.yaml`), linting, breaking change checks, and code generation.
- Provide clear Go import and dependency consumption examples for downstream microservices.

**Non-Goals:**
- Modifying protobuf schemas or Go generated stubs.
- Changing `buf.yaml` or `buf.gen.yaml` configuration.

## Decisions

- **Architecture Representation**: Use Mermaid diagrams to depict the central contract model, showing how `store_proto` generates Go packages consumed by `store_order`, `store_user`, and other microservices.
- **Service Contract Catalog**: Tabulate all RPCs, request/response payloads, field definitions, and validation semantics for `store.order.v1`.
- **Buf v2 Workflow**: Detail the exact CLI toolchain (`buf lint`, `buf breaking`, `buf generate`) along with prerequisite installation instructions for `protoc-gen-go` and `protoc-gen-go-grpc`.

## Risks / Trade-offs

- **Risk**: Protobuf schemas evolve without updating the README catalog.
  - **Mitigation**: Standardize on referencing the schema paths directly in the documentation and document the proto contribution workflow.
