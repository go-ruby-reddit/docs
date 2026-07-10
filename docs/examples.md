# Examples

The repository ships a runnable Ruby example at
[`examples/reddit_usage.rb`](https://github.com/go-ruby-reddit/reddit/tree/main/examples).
It runs under rbgo once the `require "reddit"` binding is installed; the Go core
it drives is covered to 100%.

## Basic usage

```ruby
require "reddit"

# A descriptive User-Agent is required — Reddit rate-limits generic ones.
# For anything server-side, prefer OAuth (anonymous .json is often blocked).
session = Reddit::Session.new(user_agent: "reddit-gem-example/1.0 (by /u/you)")

# A subreddit listing comes back as an Array of Hashes.
posts = session.subreddit("golang", "hot", 5)
posts.each do |p|
  puts format("%6d  %s  (u/%s)", p["score"], p["title"], p["author"])
end

# The logged-out front page.
front = session.frontpage("best", 5)
puts "front page has #{front.length} posts"

# A post plus its comment tree, as a Hash with "Post" and "Comments" keys.
if posts.any?
  tree = session.comments("golang", posts.first["id"])
  puts %(top comment: #{tree["Comments"].first&.fetch("body", "(none)")})
end
```

## Sorts and limits

```ruby
# subreddit(name, sort, limit) — sort is one of Reddit's listing sorts.
session.subreddit("ruby", "new", 10)
session.subreddit("ruby", "top", 10)

# frontpage(sort, limit) — the logged-out aggregate front page.
session.frontpage("hot", 25)
```

## Dispatch aliases

Every operation has short aliases, useful from `Call` / `method_missing`:

```ruby
session.r("golang", "hot", 25)        # alias of subreddit
session.home("best", 25)              # alias of frontpage
session.post("golang", "abc123")      # alias of comments
```

See [Errors](errors.md) for how failures surface.
