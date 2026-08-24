## MODIFIED Requirements

### Requirement: User Profile Entity Schema
The system SHALL define the protobuf schema for the `UserProfile` entity message within package `store.user.v1`.

#### Scenario: UserProfile message structure
- **WHEN** protobuf stubs are compiled for `UserProfile`
- **THEN** it exposes `id` (string), `user_id` (string), `full_name` (string), optional `phone_number` (string), optional `address` (string), `created_at` (string), and `updated_at` (string).

### Requirement: User Service gRPC CreateUserProfile Contract
The system SHALL define the `UserService` gRPC service interface containing the `CreateUserProfile` RPC in `proto/store/user/v1/user_service.proto`.

#### Scenario: CreateUserProfile RPC signature
- **WHEN** the `UserService` is defined
- **THEN** it contains `rpc CreateUserProfile (CreateUserProfileRequest) returns (CreateUserProfileResponse);`.

#### Scenario: CreateUserProfileRequest payload
- **WHEN** a client constructs a `CreateUserProfileRequest`
- **THEN** it contains required fields `user_id` (string), `full_name` (string), `email` (string), and optional field `phone_number` (string).

#### Scenario: CreateUserProfileResponse payload
- **WHEN** `CreateUserProfileResponse` is returned
- **THEN** it contains the `UserProfile` message, a boolean `is_created` flag, and a human-readable `message` string.

## REMOVED Requirements

### Requirement: Gender Enumeration
**Reason**: Demographic attributes (`gender`, `avatar_url`, `bio`, `date_of_birth`) are not needed by the application.
**Migration**: Consumers should use only essential identity fields (`id`, `user_id`, `full_name`, `phone_number`, `address`).
