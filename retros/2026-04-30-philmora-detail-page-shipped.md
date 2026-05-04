---
personal: true
public: true
slug: 2026-04-30-philmora-detail-page-shipped
title: Shipping the public-side wiki
dek: 2026-04-30 — the public side of a personal wiki shipped end-to-end. Retro on what worked, what cost time, and seven footguns logged for any future build that pairs a static-site builder with runtime fetch from a content repo.
date: 2026-04-30
updated: 2026-05-04
category: retros
order: 1
---

# Shipping the public-side wiki — retro

*2026-04-30. End-to-end ship of a public publishing pipeline. Captures the
narrative, the footguns, and the protocol learnings.*

---

## What shipped

A public-facing section of a personal site, rendering a Karpathy-style
LLM wiki that lives in a private source repo. End-to-end:

- **Index page** — catalog of six concepts, rendered from a `wiki.json`
  manifest, with empty-state copy reserved for forthcoming categories.
- **Detail pages** at `/{slug}` for each concept, with markdown rendering
  including GFM tables, prose CSS, and wikilink resolution.
- **Site navigation** updated to surface the new section.
- **Mobile navigation** added — below 720px, inline links collapse to a
  hamburger overlay.
- **Backup commits** taken at three milestones (pre-build, detail page
  shipped, index + nav shipped).

End-to-end verified via direct HTTP requests returning 200.

## How it was built

Cross-laptop, four-rev handoff cycle over two days. One agent on each
machine, a human router moving handoff documents between them. Three
architectural decisions emerged worth promoting to ADRs: dynamic routes
need bound CMS collections (CMS-as-router), nested routes 404 silently
(use flat URLs), runtime fetch requires public repos (mirror is public).

The cross-laptop coordination pattern is itself worth keeping — it has
been promoted to a separate concept ([[concepts/two-claude-protocol]]).

## Footguns logged

These are the silent failures and category errors encountered during the
build. Saving for the next time someone (likely a future agent) builds a
similar system pairing a static-site builder with runtime fetch.

### Footgun 1 — "I created the page in the builder, but slugs 404"

If the dynamic page was created via "+ New Page" with `:slug` typed into
the path, the static site builder creates it as a regular page where
`:slug` is treated as literal URL text. Dynamic-route behavior only kicks
in when the page is created **bound to a CMS collection** at creation time.

**Fix:** delete the unbound page; create via "+ New CMS Page" / "Use as
page" from the collection panel; pick the collection. Path auto-derives
from the collection name; can be edited in the URL field after creation.

### Footgun 2 — "Plugin can't bind a page to a collection"

The builder's MCP plugin can create pages, edit canvas content, and
manage CMS items. **It cannot bind a page to a CMS collection** —
that's a UI-only operation. Plan around this: every CMS-bound dynamic
route requires a human in the loop for ~30 seconds of UI work.

### Footgun 3 — "Plugin can't create user-managed CMS collections"

Same restriction. The plugin can create plugin-managed collections (a
different mode it's not in by default), but cannot reach a user-managed
collection via the plugin. The human creates the collection in UI; the
plugin populates items afterward.

### Footgun 4 — "Default 404 vs component's not-found state look identical"

When the dynamic route is unbound, the site serves its own 404 page.
When the route is bound but the slug doesn't match a CMS item, the site
serves the dynamic page but the component renders its own "Not found"
state. **Visually similar; semantically different.** First indicates a
routing problem; second indicates a slug-lookup or fetch problem.

### Footgun 5 — "Nested static-then-dynamic routes silently fail"

The builder accepts the page creation, the path appears in the project
file, but slugs 404 in browser. No error, no warning. **Test the actual
URL resolution before assuming a route works.** This footgun cost the
most time on this build — the original plan was nested routes; the final
shape had to flatten them.

### Footgun 6 — "Markdown table → pipe-soup paragraph"

Hand-rolled markdown→HTML renderers usually don't handle GFM tables.
Pipes pass through HTML escaping unchanged, lines fall into the paragraph
fallback, result is a flat run of `| header | row |` text instead of a
`<table>`. Add table detection (header line starting with `|`, separator
line with `|---|`, body lines starting with `|`).

### Footgun 7 — "Two screens that say 'Not found' for completely different reasons"

When debugging a 404, ALWAYS check:

1. URL bar — is the slug literal `:slug` because you're on the page
   preview, or is it a real concept slug?
2. Breadcrumb — does it show `:slug` literally, or the actual slug?
3. HTTP response code — the builder's actual 404 won't have your
   component chrome at all; the component's not-found state will have
   breadcrumb, footer, etc.
4. Browser console — fetch failures? CORS errors? 404s from the
   content host?

## What paid off

- **Single source of truth for handoffs.** The handoff doc was revved in
  place, not re-authored each turn. Easier to audit. Honest about the
  trail. Don't squash.
- **Architecture locked before implementation.** Fork-vs-extend, design
  tokens, schema decisions all confirmed before code was written. Saved
  rework.
- **Backup commit before risky work.** Snapshot of the project taken
  before any builder changes. Standard hygiene; matters when something
  goes wrong.

## What cost time

- **Visual prototype was wrong.** The first prototype used guessed design
  tokens (wrong color, wrong serif, wrong sans) instead of measured
  values. The second agent pulled live design tokens via the builder's
  MCP and had to rebuild the prototype from scratch. **Lesson:** when
  one agent produces a visual prototype for a system it can't see
  directly, flag it as "speculative — verify against live values." Or
  ask for the design tokens before producing the prototype.
- **CMS-binding requirement undocumented.** Multiple iterations of
  "create dynamic page → 404 in browser" before the CMS-as-router
  pattern surfaced. **Lesson:** when a dynamic route is in the plan, the
  FIRST thing both agents should agree on is the CMS collection and the
  binding step.
- **Mirror's private status surfaced as a problem only at runtime.** Page
  rendered "Not found" because runtime fetches 404'd against a private
  repo. **Lesson:** trust-boundary docs should explicitly say "the
  mirror is intended to be public; the airlock is the privacy
  mechanism." Both agents should know this from the briefing, not
  learn it during debugging.

## Operational state going forward

For body changes only: push to mirror, runtime fetch picks it up on next
page load. No builder step.

For new entries: push to mirror, then upsert one CMS item (~30 seconds
of UI work) and republish.

For new categories: same as new entries, plus a top-level entry in the
manifest so the index renders the new section.

For schema changes: route through both agents. The renderer component
may need updating in lockstep.

Mirror visibility: stays PUBLIC. Flipping to PRIVATE breaks the public
section end-to-end. If a future content type can't be safely airlocked,
build a separate authenticated path rather than flipping the mirror.

## Related

- [[decisions/work-brain-mirror-public]]
- [[concepts/two-claude-protocol]]

## Sources

- Cross-laptop handoff archive, April 29–30, 2026.
- Production verification on the day of ship.

---

*Created: 2026-04-30. Last updated: 2026-05-04.*
