---
personal: true
public: true
slug: work-brain-mirror-public
title: Why the public mirror is public
dek: The repo backing this site is PUBLIC, not private. Browser-side runtime fetch requires it; the airlock script and the forbidden-term glossary are the actual privacy boundary, not the wrapper repo's visibility setting.
date: 2026-04-30
updated: 2026-05-04
category: decisions
order: 1
---

# Why the public mirror is public

*ADR · 2026-04-30. Updated 2026-05-04 for publication.*

---

## Context

The mirror repo behind this site was originally created as a PRIVATE GitHub
repo. The reasoning at the time was defense-in-depth: even though airlocked
content has been hand-generalized and scanned against a forbidden-term
glossary, keeping the mirror private was assumed to add one more layer.

When the site shipped on 2026-04-30, the detail pages rendered "Not found"
on every slug. Diagnosis: the renderer fetches `wiki.json` and the matching
markdown file from `raw.githubusercontent.com` at runtime, browser-side.
**Private GitHub repos return 404 to unauthenticated requests** — the
browser has no auth, so the fetch fails.

Three options at that point: (1) make the mirror public, (2) proxy the
fetch through an authenticated server, (3) abandon runtime fetch and bake
content into the site at build time. Option 1 was what already worked for
a sibling content section on the same site (always-public). Option 2
introduces a server, secret management, and a new failure surface. Option 3
reverts to manually republishing on every body change — defeats the
file-first wiki model where pushing to a repo is the publish action.

## Decision

**Flip the mirror from PRIVATE to PUBLIC. Treat the airlock script and
the forbidden-term glossary as the privacy boundary.**

The mirror's "private" status was security theater — it added zero
protection beyond what the airlock already provides. By the time content
reaches the mirror, it has been:

1. Hand-written as a generalized public companion (not a copy of the
   source — original voice, no internal context, no named colleagues
   except where appropriate for thought leadership)
2. Scanned by an export script against a forbidden-term glossary that
   catches PII, customer names, dollar figures, internal project codenames,
   and tactical political content
3. Reviewed before push

If the airlock fails, private-vs-public on the mirror is irrelevant — the
content is already wrong by the time it lands. If the airlock holds, the
mirror's visibility is downstream of a clean output. Same trust model as
any always-public sibling repo on the same site.

## Alternatives considered

| Option | Why not |
|---|---|
| **Authenticated server proxy** | New server, new secret, new failure mode. Introduces a serverside component to a static site that didn't have one. Operational cost not worth a security gain that doesn't exist (private mirror + airlock ≠ stronger than public mirror + airlock if the airlock works). |
| **Bake content at build time** | Defeats the runtime-fetch model. Every body edit becomes a republish. Loses the "push and it appears" UX that makes a file-first wiki worth running. |
| **Use a private CDN with a CORS-allowed key embedded in the page** | Embedding any auth in a public page means the auth is public. Same threat model as a public repo, more moving parts. |
| **Keep mirror private, accept manual content workflow** | Considered briefly; rejected because it would mean the published section is a stale snapshot rather than a live mirror. The whole point of the pipeline is push-to-live. |

## Consequences

### Positive

- **Runtime fetch works.** End-to-end pipeline functional.
- **Trust boundary is clearly named.** The airlock is the gate, not the
  wrapper. Future "should we make this public?" decisions have a clear
  principle.
- **Same model as sibling content sections.** No special-case in the
  operator's mental model.

### Negative / accepted limitations

- **Mirror is now indexable by search engines.** Anyone can find the repo
  and read its history. Mitigation: don't put anything in the mirror that
  wouldn't survive site discovery; the airlock's job is exactly this.
- **Visibility flips become breaking changes.** If we ever flip the mirror
  back to PRIVATE (e.g., for a retro phase where content is being
  rewritten), the public site will show "Not found" on every slug.
  Coordinate visibility flips deliberately.
- **The airlock now bears the full privacy load.** Any bug or gap in the
  export script or forbidden-term glossary is the failure mode. The
  glossary needs to be maintained as new categories of content graduate
  to public — the patterns that catch one category may not catch another.

## Operational protocol

The mirror stays public. If a content type ever needs to ship to the
public site that the airlock can't safely scan, build a separate
authenticated path for it rather than flipping the mirror's visibility.

If a forbidden-term glossary update is needed (new patterns, new
categories of sensitive content), update the export script first and
re-run the airlock against the entire public tree before the next push.

## Related

- [[concepts/two-claude-protocol]] — the cross-laptop coordination pattern that makes this trust model legible

## Sources

- The build that made this decision necessary: a public publishing
  pipeline shipped end-to-end in April 2026.

---

*Created: 2026-04-30.*
