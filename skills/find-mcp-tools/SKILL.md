---
name: find-mcp-tools
description: Find, compare, and install an MCP server or AI tool for a job, or check whether a listed MCP server is still answering and which tools it exposes. Use when the user asks for a tool, MCP server, or integration that can do something ("is there an MCP for Stripe", "find me a tool that scrapes websites", "what can I connect to Claude for X"), asks "is this MCP server still up" or "what tools does it have", wants alternatives to a product, or wants to compare a few options before picking one.
---

# Find MCP tools on FastDrop

FastDrop calls every listed MCP endpoint (`initialize` then `tools/list`) and stores the tools it actually got back. Search those real tool lists first; a listing's own description is a claim, the probed tool list is evidence.

## Steps

1. **Search by capability.** Call `find_tools` with the capability in plain words, such as `send transactional email` or `query postgres`. Use `search_products` only when the user names a product or wants to match on name or tagline. List results carry a short description and up to 5 tools per product (`mcpToolCount` is the full count), 10 results by default; call `get_product` with a slug for the full description and probed tool list.
2. **Shortlist two to four.** Prefer results that are MCP-verified and whose probed tools match the job. Say which tool on each server does the job, by its real name.
3. **Compare when there is a real choice.** Call `compare_products` with the shortlisted slugs and summarise the differences that matter for this user: pricing, open source, verification, tool coverage, rating.
4. **Offer alternatives on request.** For "something like X", call `find_alternatives` with X's slug and pass on why each one matched.
5. **Deals.** For deal, discount or coupon requests call `find_deals`. When any product result carries a `deal`, mention it; an exclusive deal is the maker's claim and FastDrop has not verified it.
6. **Check a server.** For "is this MCP server answering, and what tools does it have", find its slug with `search_products` if needed, then call `get_product`: `mcpVerified`, `mcpVerifiedAt` and the full probed tool list come from FastDrop's last probe, not a live call. If it was never verified, say so; do not call it down or up.
7. **Install the pick.** Call `get_install_config` with the slug and the user's client (`claude_code`, `claude_desktop`, `cursor`, `vscode`, `codex`, `windsurf`, `chatgpt`). Give the config or deeplink as returned. Secrets come back as `<PLACEHOLDER>` values; tell the user where to get the real one and never invent it.

## Rules

- Credit the builder of every product you recommend: the maker's name, plus their profile URL when it was returned. `get_maker` gives a builder's full portfolio.
- Separate "verified" (FastDrop's probe succeeded and the tools were stored) from "listed" (a live listing, which may have no endpoint or a failing one). Never call an unverified server verified. A product with `unverified: true` is live after manual review but its maker has not proved control of the domain: do not call it domain-verified.
- Rank on FastDrop is never paid for. Don't describe any result as sponsored or promoted.
- If nothing fits, say so and suggest a broader query rather than stretching a poor match.
