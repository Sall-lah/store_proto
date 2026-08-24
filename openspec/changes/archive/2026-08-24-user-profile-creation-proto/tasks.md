## 1. Protobuf Schema Definitions

- [x] 1.1 Create `proto/store/user/v1/user_profile.proto` defining `Gender` enum and `UserProfile` message
- [x] 1.2 Create `proto/store/user/v1/user_service.proto` importing `user_profile.proto` and defining `UserService`, `CreateUserProfileRequest`, and `CreateUserProfileResponse`

## 2. Validation and Code Generation

- [x] 2.1 Run `buf lint` to ensure protobuf schemas conform to Buf v2 standard linting
- [x] 2.2 Run `buf generate` to generate Go protobuf and gRPC stubs in `gen/go/store/user/v1/`
- [x] 2.3 Run `go build ./...` to verify generated Go packages compile cleanly

