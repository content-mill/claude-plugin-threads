# Threads Scheduler for Claude: Content Mill

Schedule and publish Threads posts from chat: write posts within the 500-character limit, add photos or video, preview each one, and track what's scheduled. Threads scheduler by Content Mill.

A Claude plugin that connects Claude to **Content Mill for Threads**, a hosted MCP server at `https://threads-mcp.contentmill.dev/mcp`. Claude drafts the post, checks it against Threads's limits, shows you a preview, and publishes or schedules it only after you approve that preview. It also lists what is scheduled, published or failed.

This server works with your Threads accounts only. It cannot read or post to any other network. Content Mill has the same plugin for Instagram, X, LinkedIn, Facebook, Threads and Bluesky.

## Requirements

- A Content Mill account at [contentmill.dev](https://contentmill.dev).
- Connect your Threads account. Accounts are connected in the Content Mill web app, not in Claude.
- Claude Code, Cowork or any Claude client that supports plugins and remote MCP servers. There is nothing to install or run locally.

## Install

```
/plugin marketplace add content-mill/claude-plugin-threads
/plugin install content-mill-threads@content-mill-threads
/reload-plugins
```

Then run `/mcp`, pick **content-mill**, and sign in with your Content Mill email and password. That sign-in is the only credential involved; Claude never sees your Threads login.

To use the MCP server without the plugin (no skills), add it directly:

```
claude mcp add --transport http content-mill-threads https://threads-mcp.contentmill.dev/mcp
```

## What you can do

- “Write a Threads post about our new feature, keep it under 500 characters, and save it as a draft.”
- “Add this photo to the post, show me a preview, then schedule it for tomorrow at 9am.”
- “Which Threads posts are scheduled for this week, and did any fail to publish?”

Skills (each is also a slash command):

| Skill | What it does |
|---|---|
| `/content-mill-threads:draft-post` | Write a Threads post and save it as a draft. Never publishes. |
| `/content-mill-threads:publish-post` | Preview, then publish now or schedule, after you approve the preview. |
| `/content-mill-threads:review-posts` | See what is scheduled, published, failed or still a draft; cancel a scheduled post. |

## Threads limits

Claude reads these from the server before writing, so a post is valid before it is sent:

- Up to 500 characters (links and emoji count).
- Text-only posts are allowed.
- Images are JPEG or PNG (GIFs are refused), at least 320 px wide.
- A carousel holds up to 10 photos and videos; video up to 5 minutes.

## Tools

`list_social_accounts`, `get_platform_limits`, `create_draft`, `update_draft`, `delete_post`, `preview_post`, `publish_post`, `list_posts`, `get_post`, `upload_media_from_url`, `create_media_upload`, `finish_media_upload`.

Reads are marked read-only. `update_draft`, `delete_post` and `publish_post` are marked destructive, and only `publish_post` and `upload_media_from_url` reach outside Content Mill.

## How publishing stays safe

- Publishing is always two calls. `preview_post` stores the exact post for 10 minutes; `publish_post` redeems that preview once and publishes exactly that, re-checking it first.
- Assistants can only edit drafts. A scheduled post is removed and recreated through a new preview.
- Every tool call is recorded against your account.
- `confirmed: true` is supplied by the assistant, so the server cannot itself prove a person said yes. The plugin's skills tell Claude to show you the preview and wait.

## Troubleshooting

- **Sign-in does not start or fails:** run `/mcp`, select **content-mill** and choose to authenticate again. Use the email and password of your Content Mill account (or Google). If you have no account, create one at [contentmill.dev](https://contentmill.dev) first; the sign-in page cannot create email accounts.
- **Claude finds no Threads accounts:** connect them in the Content Mill web app (Accounts page), then ask again. Reconnect the account in the web app if Threads asks you to sign in again.
- **A post is refused:** Claude reads Threads's limits before writing and will say which one was broken (length, media). Ask it to adjust the post and preview again.
- **The preview expired or was already used:** a preview works once and for 10 minutes. Ask Claude to preview the post again.
- **Published, but no link yet:** publishing is asynchronous. Ask Claude to check the post again in a minute; a result of PartiallyFailed or Failed comes with the reason for each account.
- **Still stuck:** write to [support@contentmill.dev](mailto:support@contentmill.dev) with the time and what you asked Claude to do.

## Privacy and support

See [PRIVACY.md](./PRIVACY.md) and [contentmill.dev/privacy](https://contentmill.dev/privacy). Questions or problems: [support@contentmill.dev](mailto:support@contentmill.dev).

## License

[MIT](./LICENSE)
