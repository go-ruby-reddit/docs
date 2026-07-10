# Errors

Every operation returns a value **and an error**. The adapter itself raises two
kinds of failure, and passes a third through unchanged from
[go-reddit](https://github.com/go-reddit/reddit).

## Dispatch errors

`Session#Call` validates the method name and its arguments before it touches the
network. These come back as plain errors with a `reddit:` prefix:

| Condition | Message |
| --- | --- |
| Unknown method name | `reddit: unknown method "…"` |
| `subreddit` / `frontpage` with no name | `reddit: "…" requires a subreddit name` |
| `comments` missing an id | `reddit: "…" requires a subreddit and post id` |

```go
_, err := s.Call(ctx, "nope")
// err: reddit: unknown method "nope"
```

## Transport & API errors

Anything that reaches Reddit — a network failure, a non-2xx HTTP status, a
rate-limit — is surfaced by the underlying go-reddit client and returned
unchanged, so `errors.Is` / `errors.As` against go-reddit's own error values
work as usual:

```go
posts, err := s.Subreddit(ctx, "golang", "hot", 25)
if err != nil {
	// network / HTTP / rate-limit error from go-reddit
	return err
}
```

!!! tip "Avoiding rate limits"
    Reddit rate-limits generic User-Agents and often blocks anonymous `.json`
    access. Always set a descriptive `WithUserAgent(...)`, and prefer
    `WithOAuth(...)` / `WithOAuthScript(...)` for server-side use.

## In Ruby (under rbgo)

Once the `require "reddit"` binding ships, each of the above surfaces as a Ruby
exception raised from the failing call, so ordinary `begin/rescue` applies:

```ruby
begin
  session.subreddit("golang", "hot", 25)
rescue => e
  warn "reddit call failed: #{e.message}"
end
```
