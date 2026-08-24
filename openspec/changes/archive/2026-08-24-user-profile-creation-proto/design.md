## Context

In the e-commerce store architecture, identity authentication and profile management are decoupled across two microservices:
1. `store_auth`: Handles user registration, OTP generation/verification via email, password management, and JWT issuance.
2. `store_user`: Manages rich user profiles, notification preferences, in-app notifications, and account deletion.

When a customer registers (`POST /api/auth/register`), `store_auth` stores an inactive user record and sends an email OTP. Upon receiving the OTP (`POST /api/auth/verify-otp`), `store_auth` validates the code, activates the user, and must provision a base user profile in `store_user` before issuing tokens and guiding the user through onboarding.

This design introduces the protobuf contract enabling synchronous, strongly typed, idempotent gRPC communication between `store_auth` and `store_user`.

## Goals / Non-Goals

**Goals:**
- Provide a clean, modular Protobuf schema for user profiles and gender identity under `proto/store/user/v1/`.
- Expose the `UserService.CreateUserProfile` gRPC RPC for `store_auth` to provision initial user profiles.
- Support idempotency at the contract level (`is_created` boolean and profile entity return) to make retries safe and deterministic.
- Generate type-safe Go stubs via Buf in `gen/go/store/user/v1/`.

**Non-Goals:**
- Implementing database migrations or service logic in `store_auth` or `store_user` (handled in their respective repositories).
- Asynchronous Kafka event definitions for onboarding notifications (out of scope for this proto contract).
- Exposing client-facing HTTP/REST routes (handled by `store_gateway` and `store_user` REST controllers).

## Decisions

### Decision 1: Modular File Organization (`user_profile.proto` + `user_service.proto`)
- **Choice**: Separate the domain entities (`Gender`, `UserProfile`) in `user_profile.proto` from the service RPCs (`UserService`, `CreateUserProfileRequest/Response`) in `user_service.proto`.
- **Rationale**: Follows the existing modular design used in `proto/store/order/v1/` (`order_status.proto` + `order_service.proto`). Allows downstream services (like notification or order services) to import profile data models without binding to the `UserService` gRPC definition.
- **Alternatives Considered**: Single monolithic file (`user_service.proto`). Rejected because it entangles domain entity types with service method interfaces.

### Decision 2: Idempotent Return Semantics
- **Choice**: Design `CreateUserProfileResponse` to include the `UserProfile` message and an `is_created` boolean flag.
- **Rationale**: If network timeouts or transient retries occur from `store_auth`, `store_user` will find the existing profile and return it with `is_created = false` and gRPC status `OK`. This prevents registration failures during retry spikes.
- **Alternatives Considered**: Returning gRPC `ALREADY_EXISTS` status code. Rejected because it forces calling services to parse error details and treat errors as control flow.

### Decision 3: Standardizing Go Package Namespaces
- **Choice**: Use `option go_package = "github.com/Sall-lah/store_proto/gen/go/store/user/v1;userv1";` matching repository root module in `go.mod`.
- **Rationale**: Consistent with `proto/store/order/v1/` (`orderv1`), ensuring seamless import across Go microservices.

## Risks / Trade-offs

- **[Risk] Downstream schema misalignment between OpenAPI and Protobuf** → **Mitigation**: Field names, types, and enum values in `user_profile.proto` directly mirror the `UserProfile` schema in `store_user`'s OpenAPI 3.1 specification.
- **[Risk] Transient gRPC failure during OTP verification** → **Mitigation**: Idempotent contract allows `store_auth` to retry safely, while `store_user` can keep a fallback `getOrCreate` in `GET /api/users/profile` as defense-in-depth.
