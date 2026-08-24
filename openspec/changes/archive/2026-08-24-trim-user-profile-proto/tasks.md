## 1. Protobuf Schema Updates

- [x] 1.1 Update `proto/store/user/v1/user_profile.proto` to remove `Gender` enum and unused fields (`avatar_url`, `bio`, `gender`, `date_of_birth`), renumbering remaining fields 1..7
- [x] 1.2 Update `proto/store/user/v1/user_service.proto` to remove `avatar_url` from `CreateUserProfileRequest`

## 2. Validation and Code Generation

- [x] 2.1 Run `buf lint` to ensure schemas conform to Buf v2 linting
- [x] 2.2 Run `buf generate` to regenerate Go protobuf and gRPC stubs in `gen/go/store/user/v1/`
- [x] 2.3 Run `go build ./...` to verify clean Go package compilation

