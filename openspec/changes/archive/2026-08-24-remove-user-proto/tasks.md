## 1. Remove User Protobuf and Generated Stubs

- [x] 1.1 Remove `proto/store/user/` directory
- [x] 1.2 Remove `gen/go/store/user/` directory

## 2. Validation

- [x] 2.1 Run `buf lint` to verify remaining order protobuf schemas lint cleanly
- [x] 2.2 Run `go build ./...` to verify clean Go package compilation

