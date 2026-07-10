# Contributing

go-ruby-reddit is BSD-3-Clause. The code lives at
[github.com/go-ruby-reddit/reddit](https://github.com/go-ruby-reddit/reddit).

## Ground rules

- **CGO-free.** No cgo, ever — the package must build and test on all six
  supported 64-bit targets (`amd64`, `arm64`, `riscv64`, `loong64`, `ppc64le`,
  `s390x`).
- **100 % coverage.** The CI gate enforces 100 % statement coverage, including
  error branches.
- **Backed by go-reddit.** The typed transport, models and errors live in
  [go-reddit](https://github.com/go-reddit/reddit); this repo is only the
  Ruby-idiomatic adapter. Changes to the underlying HTTP surface belong upstream.

## Build & test

```sh
go build ./...
go vet ./...
go test -race -coverprofile=cover.out ./...
go tool cover -func=cover.out | tail -1   # must read 100.0%
```

## Examples

The Ruby example under `examples/` is verified: the Go core it drives is covered
to 100%, and it runs under the rbgo interpreter once the `require "reddit"`
binding is installed. Keep it runnable.
