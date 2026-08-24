## REMOVED Requirements

### Requirement: User Profile Entity Schema
**Reason**: Synchronous gRPC user profile creation is deprecated and removed from platform requirements.
**Migration**: No proto replacement needed. Profile lifecycle is managed within `store_user`.

### Requirement: User Service gRPC CreateUserProfile Contract
**Reason**: `UserService.CreateUserProfile` RPC is not needed by `store_auth`.
**Migration**: `store_auth` does not make gRPC calls to provision user profiles.

### Requirement: Idempotent Profile Provisioning Semantics
**Reason**: Associated with deprecated `CreateUserProfile` RPC.
**Migration**: None.
