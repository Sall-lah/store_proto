## Context

The initial `user_profile.proto` and `user_service.proto` definitions contained demographic and profile fields (`avatar_url`, `bio`, `gender`, `date_of_birth`, and enum `Gender`) that are not part of the active business requirements for the store platform. Trimming these fields ensures that the protobuf contracts remain minimal, clear, and easy to maintain across microservices.

## Goals / Non-Goals

**Goals:**
- Remove `Gender` enum from `proto/store/user/v1/user_profile.proto`.
- Remove `avatar_url`, `bio`, `gender`, and `date_of_birth` from `UserProfile` in `proto/store/user/v1/user_profile.proto`.
- Renumber remaining fields in `UserProfile` sequentially (`id = 1`, `user_id = 2`, `full_name = 3`, `phone_number = 4`, `address = 5`, `created_at = 6`, `updated_at = 7`).
- Remove `avatar_url` from `CreateUserProfileRequest` in `proto/store/user/v1/user_service.proto`.
- Re-run `buf lint`, `buf generate`, and `go build ./...` to produce updated Go stubs.

**Non-Goals:**
- Modifying other proto packages (e.g. `store.order.v1`).
- Adding new gRPC RPC endpoints.

## Decisions

### Decision 1: Sequential Tag Renumbering vs Reserved Tags
- **Choice**: Renumber fields in `UserProfile` sequentially from 1 to 7 without preserving deprecated field numbers with `reserved`.
- **Rationale**: The `store.user.v1` proto package is newly introduced and not yet deployed to live production traffic. Sequential numbering provides the cleanest schema.
- **Alternatives Considered**: Using `reserved 5, 6, 8, 9;`. Rejected as unnecessary overhead at this stage of development.

### Decision 2: Preserving Modular Two-File Layout
- **Choice**: Keep `user_profile.proto` (entity model) and `user_service.proto` (service contract) separated.
- **Rationale**: Maintains architectural consistency with `proto/store/order/v1/` and allows downstream modules to import `UserProfile` without importing the gRPC service definition.

## Risks / Trade-offs

- **[Risk] Breaking changes for existing local generated stubs** → **Mitigation**: Run `buf generate` and `go build ./...` in the same change to keep the Go package stubs strictly synchronized with the `.proto` files.
