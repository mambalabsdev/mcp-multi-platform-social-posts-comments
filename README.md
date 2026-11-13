# Multi Platform Social Posts and Comments MCP Server

[![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-multi-platform-social-posts-comments/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-multi-platform-social-posts-comments) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-multi-platform-social-posts-comments%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-multi-platform-social-posts-comments&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-multi-platform-social-posts-comments)](https://www.npmjs.com/package/@mambalabsdev/mcp-multi-platform-social-posts-comments) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-multi-platform-social-posts-comments)](https://www.npmjs.com/package/@mambalabsdev/mcp-multi-platform-social-posts-comments) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-multi-platform-social-posts-comments)](https://github.com/mambalabsdev/mcp-multi-platform-social-posts-comments/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-multi-platform-social-posts-comments)

An MCP server for the Mamba Labs Multi Platform Social Posts and Comments actor on Apify. Public posts and comments from 6 platforms, in one row layout.

## Note to current users, 13 November 2026

Reddit changed how its data can be reached: it closed logged-out search about 5 November 2026 and ended its RSS feeds on 13 November 2026. We rebuilt the actor so it keeps returning Reddit data. It now reads the Arctic Shift archive of Reddit posts, lets you choose the route with `reddit_route`, and switches to the other route when the first one fails. Add your own Serper or Brave search key to search all of Reddit by keyword. A blocked run charges only the start fee. Your saved inputs and tasks keep working. Prices do not change.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Give keywords or post addresses. The actor reads public posts and their comments from Bluesky, Hacker News, Mastodon, Lemmy, Reddit, and YouTube, with no login, and returns one row layout for all of them. Reddit is read from the Arctic Shift archive for the subreddits you name, or across all of Reddit with your own Serper or Brave key. A free status row per query and platform says what each returned or why it was blocked. Instagram, X, Facebook, TikTok, and LinkedIn are not covered.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-multi-platform-social-posts-comments": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-multi-platform-social-posts-comments"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `get_social_posts_comments` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "What are people saying about CRM migrations on Hacker News and Bluesky?"
- "Collect the comments on these post addresses into one table."
- "Find recent public posts about cold email deliverability since October."

## Tool

`get_social_posts_comments`. One tool. Example arguments:

```json
{
  "queries": [
    "crm"
  ],
  "platforms": [
    "bluesky",
    "hackernews",
    "lemmy"
  ],
  "max_posts_per_platform": 3,
  "max_comments_per_post": 2
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults.

| Input | Type | Required | Description |
|---|---|---|---|
| `queries` | array | no | Keywords to search on each platform, up to 20. Mastodon reads a query as a hashtag, so crm tools becomes #crmtools. |
| `post_urls` | array | no | Addresses of posts to read comments from, such as your own posts, up to 100. Bluesky, Hacker News, Mastodon, Lemmy, Reddit, and YouTube. |
| `platforms` | array | no | Any of bluesky, hackernews, mastodon, lemmy, reddit, youtube. Default all 6. Used for queries; a post address picks its own platform. |
| `max_posts_per_platform` | number | no | Up to 500. Default 25. YouTube returns its first results page, about 20 videos. |
| `include_comments` | boolean | no | yes or no. Default yes. Each comment is a charged row. |
| `max_comments_per_post` | number | no | Up to 200. Default 20. YouTube returns its first comment page, about 20. |
| `since_date` | string | no | Only posts from this date, such as 2026-10-01. YouTube gives relative dates only, so YouTube rows are not filtered. |
| `mastodon_instance` | string | no | Host name to read hashtags from. Default mastodon.social. |
| `lemmy_instance` | string | no | Host name to search. Default lemmy.world. |
| `reddit_subreddits` | array | no | Subreddits to search for each query on Reddit, such as sales or r/sales, up to 10. Without your own search key, Reddit is searched through the Arctic Shift archive, which needs a subreddit. Leave empty to search all of Reddit with `search_api_key`. |
| `reddit_route` | string | no | `arctic_shift` (default): the Arctic Shift archive, free, needs `reddit_subreddits`. `search_key`: your own Serper or Brave key, searches all of Reddit. `both`: both, deduped. When the chosen route fails or cannot run, the actor uses the other one if it can and says so in `route_note`. |
| `search_provider` | string | no | `serper` or `brave`, the provider of `search_api_key`. Default `serper`. |
| `search_api_key` | string | no | Optional. Your own Serper or Brave Search key, used only to find Reddit posts across all of Reddit. Sent only to that provider, never logged or written to a row. |
| `max_charge_usd` | string | no | Optional. The run stops before a charge that would pass this amount. Summary rows are always returned. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `social-row`, one post or comment | $0.002 | $0.0019 | $0.0018 | $0.0017 |
| `apify-actor-start`, per run start | $0.007 | $0.007 | $0.007 | $0.007 |

Platform status rows and refused input are free, and a blocked run charges only the start fee. Mamba Labs pays the platform costs of the run, Apify proxy traffic included. Search calls made with your own key are billed to you by your provider.

## Full actor documentation

[apify.com/mambalabs/multi-platform-social-posts-comments](https://apify.com/mambalabs/multi-platform-social-posts-comments)

## License

MIT. See [LICENSE](LICENSE).
