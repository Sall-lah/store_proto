# User Profile Provisioning Specification

## Purpose
Defines the gRPC service contract, data models, and idempotency semantics for provisioning user profile records between Auth Service (`store_auth`) and User Service (`store_user`).

## Requirements

### Requirement: User Profile Entity Schema and Gender Enum
The system SHALL define the protobuf schema for `Gender` enumeration and `UserProfile` entity message within package `store.user.v1`.

#### Scenario: Valid Gender enum values
- **WHEN** client or service reads the `Gender` enum definition
- **THEN** it contains `GENDER_UNSPECIFIED = 0`, `GENDER_MALE = 1`, `GENDER_FEMALE = 2`, `GENDER_OTHER = 3`, and `GENDER_PREFER_NOT_TO_SAY = 4`.

#### Scenario: UserProfile message structure
- **WHEN** protobuf stubs are compiled for `UserProfile`
- **THEN** it exposes `id` (string), `user_id` (string), `full_name` (string), optional `phone_number` (string), optional `avatar_url` (string), optional `bio` (string), optional `address` (string), `gender` (Gender), optional `date_of_birth` (string), `created_at` (string), and `updated_at` (string).

### Requirement: User Service gRPC CreateUserProfile Contract
The system SHALL define the `UserService` gRPC service interface containing the `CreateUserProfile` RPC in `proto/store/user/v1/user_service.proto`.

#### Scenario: CreateUserProfile RPC signature
- **WHEN** the `UserService` is defined
- **THEN** it contains `rpc CreateUserProfile (CreateUserProfileRequest) returns (CreateUserProfileResponse);`.

#### Scenario: CreateUserProfileRequest payload
- **WHEN** a client constructs a `CreateUserProfileRequest`
- **THEN** it contains required fields `user_id` (string), `full_name` (string), `email` (string), and optional fields `phone_number` (string) and `avatar_url` (string).

#### Scenario: CreateUserProfileResponse payload
- **WHEN** `CreateUserProfileResponse` is returned
- **THEN** it contains the `UserProfile` message, a boolean `is_created` flag, and a human-readable `message` string.

### Requirement: Idempotent Profile Provisioning Semantics
The `CreateUserProfile` contract SHALL support idempotent replay without returning errors when a profile for the specified `user_id` already exists.

#### Scenario: First-time profile creation
- **WHEN** `CreateUserProfile` is called for a new `user_id`
- **THEN** the response returns `is_created = true` and the newly created `profile`.

#### Scenario: Duplicate or retried profile creation call
- **WHEN** `CreateUserProfile` is called for a `user_id` that already has an existing profile record
- **THEN** the response returns `is_created = false`, the existing `profile`, and a successful gRPC status code (`OK`).
