## ADDED Requirements

### Requirement: Buf Workspace Configuration
The repository SHALL define `buf.yaml` at the root directory configuring the protobuf module and linting rules.

#### Scenario: Module path and lint settings
- **WHEN** `buf lint` or `buf build` is executed
- **THEN** it SHALL parse `buf.yaml` configured with `version: v2` (or standard Buf configuration) and standard linting checks.

### Requirement: Protobuf Code Generation Configuration
The repository SHALL define `buf.gen.yaml` configuring code generation plugins for Go and Go gRPC stubs.

#### Scenario: Go code generation
- **WHEN** `buf generate` is executed
- **THEN** it SHALL generate Go protobuf models and gRPC client/server stubs targeting `gen/go/` mapped to the `github.com/Sall-lah/store_proto/gen/go` module path.
