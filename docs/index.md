# go-ruby-reddit

The pure-Go, Ruby-runtime-independent core of the Ruby **`reddit`** gem — a
reader-oriented client for **Reddit's public API**, shaped so that
[go-embedded-ruby](https://github.com/go-embedded-ruby) (rbgo) can bind it as
`require "reddit"`.

It is a thin, reflective adapter over the typed client in
[go-reddit](https://github.com/go-reddit/reddit). A `Session` exposes the
client's operations through a single dynamic entry point, `Call`, which:

1. maps a Ruby-style snake_case name (e.g. `subreddit`, `frontpage`,
   `comments`) to the corresponding Go method;
2. coerces the arguments — plain values become the subreddit name, sort and
   limit; omitted trailing arguments fall back to sensible defaults;
3. normalises the result into Ruby-shaped data: an **Array** of **Hashes**
   (`[]any` of `map[string]any`) for a listing, a **Hash** for a post-and-comments
   thread.

Nothing here depends on the Ruby runtime, so it is equally usable as a standalone
Go library — a sibling of `go-ruby-regexp` and the rest of the go-ruby-* family.

## Install

```sh
go get github.com/go-ruby-reddit/reddit
```

## At a glance

- **CGO-free** — builds and tests identically on `amd64`, `arm64`, `riscv64`,
  `loong64`, `ppc64le` and `s390x`.
- **100 % test coverage**, race-clean, enforced in CI.
- Backed by [go-reddit](https://github.com/go-reddit/reddit) (the typed client).

See [Usage](usage.md) for the API, [Examples](examples.md) for runnable Ruby, and
[Errors](errors.md) for the error surface.

!!! note "Independent client"
    go-ruby-reddit is an independent, reader-oriented client. It is not
    affiliated with, endorsed by, or sponsored by Reddit, Inc. A descriptive
    `user_agent` is required, and for server-side use OAuth is strongly
    recommended — anonymous `.json` access is often rate-limited or blocked.
