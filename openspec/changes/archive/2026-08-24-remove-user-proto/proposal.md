## Why

The platform architecture does not require a synchronous gRPC call (`UserService.CreateUserProfile`) to provision user profile records. Removing the unused `store.user.v1` protobuf definitions and generated Go stubs keeps `store_proto` lean and avoids maintaining obsolete contracts.

## What Changes

- **REMOVED**: `proto/store/user/v1/user_profile.proto` and `proto/store/user/v1/user_service.proto`.
- **REMOVED**: Generated Go stubs in `gen/go/store/user/`.
- **REMOVED**: `user-profile-provisioning` capability specification in `openspec/specs/user-profile-provisioning/`.

## Capabilities

### New Capabilities
<!-- No new capabilities -->

### Modified Capabilities
- `user-profile-provisioning`: Fully removed and deprecated as gRPC profile creation is no longer utilized.

## Impact

- **Protobuf Schemas**: Deletes `proto/store/user/` directory entirely.
- **Generated Code**: Deletes `gen/go/store/user/` directory.
- **Remaining Scope**: Retains `store.order.v1` (`OrderService` and `OrderStatus`).
