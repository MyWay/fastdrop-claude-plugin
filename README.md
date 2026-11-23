# FastDrop for Claude

Find MCP servers that actually work, and launch your own product where AI agents look for tools.

[FastDrop](https://fastdrop.dev) is a launch board for agent-ready software. It calls every listed MCP endpoint (`initialize`, then `tools/list`) and publishes the tools it actually got back, so you can tell a server that works from one that is only listed. This plugin connects Claude to FastDrop's MCP server and adds three skills that tell Claude how to use it.

## What's included

- **FastDrop connector** (`.mcp.json`): the remote MCP server at `https://fastdrop.dev/mcp`.
- **find-mcp-tools**: ask for a tool for a job ("is there an MCP server that can query Postgres?"). Claude searches the probed tool lists, compares the best options, credits each maker, and gives you ready-to-paste install config for Claude Code, Claude Desktop, Cursor, VS Code, Codex, Windsurf, or ChatGPT.
- **launch-product**: ask Claude to launch what you built. It drafts the listing with you, runs a dry run that shows every field error and the MCP probe result, and submits it once you confirm, guiding you through the DNS record that takes your listing live without waiting for review.
- **launch-day**: ask how your launch is doing. Claude reports which AI agents surfaced your product and from which clients, your rank and votes, drafts replies to unanswered comments for your approval, and pulls your badge, share links, and announcement copy.

## What's new in 1.3.0

- launch-product asks for your X handle before submitting (`xHandle`), offers a launch deal (`set_deal`) after launch, and asks whether the product is open to acquisition, investment or partnership only when you want to say so.
- The DNS TXT record is the required step that takes a listing live as verified. A live listing that still shows the record is unverified until you add it.
- find-mcp-tools uses `find_deals` for deal and discount requests.
- When a tool returns `invalid_arguments`, Claude fixes the named fields and retries.
- Product follows are retired; you can still follow makers and tags.

## Use it

Searching works without an account, and listing a product is free. Submitting, voting, reviewing, and launch stats need a FastDrop account: connect the FastDrop connector and sign in through FastDrop's OAuth flow when Claude asks. Then try:

- "Find me an MCP server that can send transactional email, and how do I install it in Cursor?"
- "Launch my project on FastDrop" (with the repo or site at hand)
- "How is my FastDrop launch doing today?"

Claude asks before it submits a product, posts a comment, votes, or reviews.

## Data

The plugin itself runs no code and stores nothing. Claude sends your requests to FastDrop's MCP server at `fastdrop.dev`, and nowhere else. FastDrop logs each MCP request's tool name, result, timing, AI client name, and IP address for 30 days, and does not log the arguments you send or the results it returns. Products you submit, comments, votes, and reviews are stored in your FastDrop account and shown publicly on fastdrop.dev once approved. Full details: [privacy policy](https://fastdrop.dev/privacy) and [terms](https://fastdrop.dev/terms).

## Support

Contact: [fastdrop.dev/contact](https://fastdrop.dev/contact). Developer docs: [fastdrop.dev/developers](https://fastdrop.dev/developers).

## License

MIT
