## Context

During architecture evaluation, it was determined that `store_auth` does not need to invoke `store_user` synchronously over gRPC during OTP verification to create a user profile. As a result, the `store.user.v1` protobuf definitions and generated Go code are obsolete.

## Goals / Non-Goals

**Goals:**
- Remove all files under `proto/store/user/`.
- Remove all generated Go files under `gen/go/store/user/`.
- Remove the main capability spec at `openspec/specs/user-profile-provisioning/`.
- Verify remaining workspace integrity with `buf lint` and `go build ./...`.

**Non-Goals:**
- Modifying `proto/store/order/v1/` or its generated stubs.

## Decisions

### Decision 1: Complete Removal of `store.user.v1` Package
- **Choice**: Permanently remove `proto/store/user/` and `gen/go/store/user/` rather than keeping stubbed or deprecated protos.
- **Rationale**: Keeps repository clean and avoids confusing downstream developers with dead code or unused stubs.

## Risks / Trade-offs

- **[Risk] Downstream build breakage if `store_user` stubs were imported elsewhere** → **Mitigation**: Verify that `store_proto` compiles cleanly (`go build ./...`) and downstream repositories manage their own internal models.
