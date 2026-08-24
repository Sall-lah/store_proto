## Why

The initial user profile protobuf schema included several optional and demographic fields (`avatar_url`, `bio`, `gender`, and `date_of_birth`) along with a `Gender` enum that are not required for the core application workflow. Trimming these unused fields simplifies the inter-service gRPC contract, reduces payload size, and keeps the codebase focused strictly on required features.

## What Changes

- **REMOVED**: `Gender` enumeration in `proto/store/user/v1/user_profile.proto`.
- **REMOVED**: `avatar_url`, `bio`, `gender`, and `date_of_birth` fields from `UserProfile` message in `proto/store/user/v1/user_profile.proto`.
- **MODIFIED**: Renumber remaining `UserProfile` message fields (`id = 1`, `user_id = 2`, `full_name = 3`, `phone_number = 4`, `address = 5`, `created_at = 6`, `updated_at = 7`).
- **REMOVED**: `avatar_url` field from `CreateUserProfileRequest` in `proto/store/user/v1/user_service.proto`.
- **MODIFIED**: Regenerate compiled Go protobuf and gRPC stubs in `gen/go/store/user/v1/`.

## Capabilities

### New Capabilities
<!-- No new capabilities -->

### Modified Capabilities
- `user-profile-provisioning`: Updates `UserProfile` entity message and `CreateUserProfileRequest` schema to remove unused fields (`avatar_url`, `bio`, `gender`, `date_of_birth`) and removes the `Gender` enum.

## Impact

- **Protobuf Schemas**: Modifies `proto/store/user/v1/user_profile.proto` and `proto/store/user/v1/user_service.proto`.
- **Generated Code**: Updates `gen/go/store/user/v1/user_profile.pb.go`, `gen/go/store/user/v1/user_service.pb.go`, and `gen/go/store/user/v1/user_service_grpc.pb.go`.
- **Downstream Services**: Simplifies field mappings in `store_auth` and `store_user`.
