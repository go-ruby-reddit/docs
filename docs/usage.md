# Usage

## Ruby (under rbgo)

Once rbgo ships the `require "reddit"` binding, the gem is used like this:

```ruby
require "reddit"

# A descriptive User-Agent is required — Reddit rate-limits generic ones.
session = Reddit::Session.new(user_agent: "app/1.0 (by /u/you)")

# A subreddit listing comes back as an Array of Hashes.
posts = session.subreddit("golang", "hot", 25)
posts.each { |p| puts "#{p['score']}  #{p['title']}" }

# The logged-out front page.
front = session.frontpage("best", 25)

# A post plus its comment tree comes back as a Hash.
tree = session.comments("golang", posts.first["id"])
```

## Go

The same surface is available directly, without Ruby:

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-ruby-reddit/reddit"
)

func main() {
	ctx := context.Background()
	s := reddit.NewSession(reddit.WithUserAgent("app/1.0 (by /u/you)"))

	posts, err := s.Subreddit(ctx, "golang", "hot", 25) // []any of map[string]any
	if err != nil {
		panic(err)
	}
	front, _ := s.Frontpage(ctx, "best", 25)
	tree, _ := s.Comments(ctx, "golang", "abc123") // map[string]any
	_ = front
	_ = tree

	fmt.Println(posts[0].(map[string]any)["title"])

	// Dynamic dispatch — what the rbgo binding drives:
	got, _ := s.Call(ctx, "subreddit", "golang", "hot", 25)
	_ = got
}
```

`Session.Client()` returns the underlying fully-typed `*godreddit.Client`.

## Operations

| Ruby / dispatch name | Aliases | Arguments | Result |
| --- | --- | --- | --- |
| `subreddit` | `r` | name, sort, limit | Array of Hashes |
| `frontpage` | `front`, `home` | sort, limit | Array of Hashes |
| `comments` | `post` | subreddit, id | Hash |

`Call` accepts either the primary name or any listed alias.

## Options

Re-exported from [go-reddit](https://github.com/go-reddit/reddit), passed to
`NewSession`:

| Option | Purpose |
| --- | --- |
| `WithUserAgent(ua)` | Set the required descriptive User-Agent. |
| `WithOAuth(...)` | Authenticate with an OAuth token (recommended server-side). |
| `WithOAuthScript(...)` | Script-app OAuth (client id/secret + user credentials). |
| `WithHTTPClient(c)` | Supply a custom `*http.Client` (timeouts, proxy, transport). |
| `WithBaseURL(u)` | Override the API base URL (testing, mirrors). |

## Argument coercion

Arguments are plain values: a subreddit name and sort are strings, a limit is an
integer. Omitted trailing arguments fall back to defaults (a default sort and
limit). Results are JSON-normalised — a listing becomes an Array of Hashes, a
thread a single Hash, and scalars stay scalars.
