## Why

When a new user completes OTP verification during the registration lifecycle in the Auth Service (`store_auth`), the User Service (`store_user`) must provision an initial user profile record so the customer can smoothly transition into onboarding and profile customization. Establishing a strongly typed, idempotent gRPC contract between `store_auth` and `store_user` eliminates race conditions, guarantees atomic profile baseline initialization, and supports seamless onboarding.

## What Changes

- Define `Gender` enumeration covering user gender identities (`GENDER_UNSPECIFIED`, `GENDER_MALE`, `GENDER_FEMALE`, `GENDER_OTHER`, `GENDER_PREFER_NOT_TO_SAY`) in `proto/store/user/v1/user_profile.proto`.
- Define `UserProfile` entity message structure reflecting the core user profile model.
- Define `UserService` gRPC service contract with `CreateUserProfile` RPC in `proto/store/user/v1/user_service.proto`.
- Define `CreateUserProfileRequest` carrying initial identity fields (`user_id`, `full_name`, `email`, optional `phone_number`, optional `avatar_url`).
- Define `CreateUserProfileResponse` returning the provisioned profile, an `is_created` flag for idempotent retries, and operational status message.

## Capabilities

### New Capabilities
- `user-profile-provisioning`: Defines gRPC service contract and message schemas for creating user profiles upon account activation and OTP verification with built-in idempotency semantics.

### Modified Capabilities
<!-- No existing spec requirements are modified -->

## Impact

- **Protobuf Schemas**: Adds `proto/store/user/v1/user_profile.proto` and `proto/store/user/v1/user_service.proto`.
- **Downstream Services**:
  - `store_user`: Implements the `UserService.CreateUserProfile` gRPC server and idempotency handling.
  - `store_auth`: Implements the gRPC client calling `CreateUserProfile` upon successful OTP confirmation in `POST /api/auth/verify-otp`.
- **Tooling & Stubs**: Uses Buf CLI to compile and generate Go protobuf stubs under `gen/go/store/user/v1/`.
