---
name: launch-product
description: Submit or launch the user's own product on FastDrop. Use when the user wants to launch, list, submit, or announce something they built (an app, SaaS, API, MCP server, or AI tool), asks where to launch a side project ("I just shipped X, where do I launch it"), or asks about the status of a FastDrop submission.
---

# Launch a product on FastDrop

FastDrop is a launch board that AI agents search over MCP, so a listing is found from Claude, ChatGPT, Cursor, and other clients, not only on the website. Listing is free. A listing on its own domain goes live automatically once the maker proves they own the domain with a DNS TXT record (it is reviewed after the fact), and goes live verified: do-follow link, DOMAIN VERIFIED badge, eligible for Rising. Listings on shared hosts (vercel.app, github.io, ...) go through a human review queue instead. Treat the DNS record as required for a listing on the maker's own domain: present it as mandatory, never as optional.

## Before submitting

Submitting needs the user's FastDrop account. If a tool call returns an authentication error, ask the user to connect the FastDrop connector and sign in, then retry.

Gather these from the user, their README, or their site. Ask only for what you can't find. Also ask for the maker's X handle and pass it as `xHandle` (`name`, `@name` or an x.com URL): FastDrop tags them on X when the launch is featured, and it is saved to their profile. Skip it if they don't want to share one.

| Field            | Limits                                             |
| ---------------- | -------------------------------------------------- |
| `name`           | 2-80 chars                                         |
| `tagline`        | 10-140 chars, what it does in one line             |
| `description`    | 40-4000 chars                                      |
| `problemSolved`  | 10-300 chars                                       |
| `differentiator` | 10-300 chars, how it differs from the alternatives |
| `url`            | the product's site                                 |
| `pricingModel`   | `free`, `freemium`, or `paid`                      |

Worth adding when available: `mcpUrl` (the product's own MCP endpoint), up to 3 `tags`, `githubUrl`, `docsUrl`, `twitterUrl` (the product's own X account, x.com/twitter.com only; the maker's personal handle goes in `xHandle`), `logoUrl`, and a `makerNote` (the first comment: why they built it, max 1000 chars).

Write the tagline and description in the maker's voice, plainly, without hype words. Show the user the copy before submitting.

## Submit

1. **Dry run first.** Call `submit_product` with `dryRun: true`. It returns every field error, duplicates, the MCP probe result, and the slug. Fix the errors with the user. If any call returns `invalid_arguments`, fix the fields it names and retry; there is no need for a dry run just to see field errors. If it returns `domainVerification`, tell the user now that going live takes one DNS TXT record at that name, so it is no surprise after submitting.
2. **Explain the MCP result.** A live `mcpUrl` that passes the probe gets the verified badge, and its real tools are listed. If the probe fails, say what failed. If the endpoint needs auth, the user adds a read-only probe credential on the FastDrop submit form (fastdrop.dev/app/submit), which FastDrop uses only for `initialize` and `tools/list`. Never ask for the credential in chat.
3. **Submit for real** once the user confirms the copy: the same call without `dryRun`. If it returns `email_not_verified`, relay the message (open the verification link FastDrop emailed, or resend it from fastdrop.dev/app) and submit again once they confirm.
4. **Report the outcome, leading with `nextAction`.** When `stage` is `awaiting_dns` the listing is NOT live yet: in the same reply, show the exact record from `domainVerification.dnsRecord` (type, name, value) and walk the user through publishing it (`nextStep` and `dnsProvider` have provider-specific steps). Never sum this up as "submitted, in review". FastDrop re-checks DNS automatically and the listing goes live as soon as it matches; once the user has saved the record, `check_submission_status` re-checks on the spot. When `stage` is `live` and the result still carries `domainVerification`, the listing is live but unverified: tell the user so, show the same record, and say that publishing it upgrades the listing to verified.
5. **Ask the `askMaker` questions.** When the result has an `askMaker` list, put each question to the user before you finish, in plain words. Typical ones: their X handle if they didn't give one (save it with `update_profile`), whether they'd like to upvote any of the suggested launches (see below), and whether the product is open to acquisition, investment or partnership (shared only with the FastDrop team and paid investor-edition subscribers, never public; save a yes with `set_open_to`, exactly as they said it). Act only on their answers. Mention the optional revenue band, funding stage and team size on `set_open_to` only if the maker asks about investor visibility, and never infer any of it.
6. **Offer a launch deal.** After the launch, the maker may add a deal with `set_deal` (the first deal is reviewed; the link must be on the product domain). Offer it, and set it only when they say yes.

## Get seen

Voting on, reviewing, or commenting on 3 different launches within 7 days gets the user's live launches shown first to other makers in the engagement feed, and moves a submission still in manual review to the front of the queue. Offer this; don't do it unasked. If they want to, call `get_engagement_feed`, show the launches that need attention, and let the user choose which ones to vote on (`toggle_vote`) or review (`post_review`). Votes and reviews must be the user's real opinion: never vote or review on their behalf without asking about each one.

## After a fix to the MCP server

If the listing lost or never got its verified badge, `recheck_mcp` with the slug re-probes immediately (limit 3 per product per hour).
