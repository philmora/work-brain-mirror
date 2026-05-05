---
personal: true
public: true
internal: true
slug: airlock-publishing-pipeline
title: Airlock Publishing Pipeline
dek: A reusable pattern for selectively publishing from a private knowledge wiki to a public site without leaking internal context — file-first source, hand-generalized companions, glossary-scanned export, public mirror with runtime fetch.
date: 2026-05-05
updated: 2026-05-05
category: recipes
order: 1
stream: internal
---

# Airlock Publishing Pipeline

*A reusable pattern for selectively publishing from a private knowledge
wiki to a public site without leaking internal context — file-first
source, hand-generalized companions, glossary-scanned export, public
mirror with runtime fetch.*

---

## When to use this

You have a personal or professional knowledge wiki that contains a mix
of internal context (named colleagues, customer references, dollar
figures, internal codenames, tactical notes) and ideas that would
generalize as thought leadership. You want to publish the latter while
keeping the former private. You want the publishing surface to feel
like push-to-live, not republish-on-every-edit. You don't want to run a
server.

## What it is — five components

1. **A private wiki on the source machine.** Cleartext locally,
   encrypted-at-rest on remote backup if needed. Categories organize
   content (concepts, decisions, sources, retros, recipes, reflections).
2. **A `public/` subtree** that mirrors the wiki's category structure
   but contains only **hand-written generalized companions** — never
   copies of the source. Companions strip names, codenames, customer
   references, dollar figures, and tactical political content.
3. **An airlock script** that scans `public/` against a forbidden-term
   glossary on every export. Any pattern hit → HARD FAIL. The glossary
   is the safety net; the writing discipline is the primary gate.
4. **A public mirror repo** that the airlock pushes to. Public visibility
   is required if the renderer fetches at runtime (browser-side fetches
   don't authenticate to GitHub).
5. **A renderer** on the public site that reads a `wiki.json` manifest
   and fetches markdown files from the mirror at runtime. The renderer
   never holds content; the mirror is canonical.

## The airlock script in pseudocode

```bash
#!/bin/bash
set -euo pipefail

PUBLIC=~/wiki/public
GLOSSARY=~/wiki/.export/glossary.txt
STAGING=~/wiki/.export/staging
MIRROR_REMOTE="git@deploy-key-host:user/wiki-mirror.git"

# 1. Scan every .md and .txt in public/ against the glossary
for file in $(find "$PUBLIC" -type f \( -name "*.md" -o -name "*.txt" \)); do
  for pattern in $(grep -v '^#' "$GLOSSARY"); do
    if grep -qiF -- "$pattern" "$file"; then
      echo "FAIL $file matches forbidden term: $pattern"
      exit 2
    fi
  done
done

# 2. Stage public/ to a working directory with .git metadata preserved
rsync -a --delete --exclude='.git' "$PUBLIC/" "$STAGING/"

# 3. Push to mirror
cd "$STAGING"
git add -A
git commit -m "mirror export $(date -u +%Y-%m-%dT%H:%M:%SZ)"
git push origin main
```

Default mode is dry-run; `--push` is the explicit flag that actually
pushes. This forces the human to look at the staged output before it
goes public.

## The forbidden-term glossary

Patterns are case-insensitive substring matches by default; prefix
`re:` for regex.

What to put in it:

- All customer / client names (in your domain)
- Your employer's name (bare and compound forms)
- Acquired / parent / subsidiary brand names
- Named colleagues' full names (full names only — bare first names
  produce false positives on common words)
- Internal project codenames
- Internal system / tool names that don't appear publicly
- Cost figures and scale claims that are commercially sensitive
- Email domains, Slack workspace URLs, internal hostnames
- Any acronym specific to your organization

What NOT to put in it:

- Generic technical terms ("Postgres," "Kubernetes")
- Public product names (LLM model names, well-known frameworks)
- Common first names (will false-positive)

A typical glossary lands around 150–250 patterns after the first month
of use. New patterns get added when a new colleague appears in your
notes or when an audit catches a pattern you missed.

## The public companion writing discipline

For every private wiki entry that has a publish-worthy thesis, write a
**new file** in `public/` with the same slug. Do NOT diff-and-redact
the private version. Diff-and-redact misses voice tells, paragraph
shape, and adjacency clues. Hand-write the companion as if writing for
a public audience that has zero context on your organization.

Heuristic checklist before adding a file to `public/`:

- Could a competitor read this and learn something specific about your
  organization's plans? If yes, edit until no.
- Does it name a colleague? If yes, swap to "the team," "the engineer,"
  "a peer manager." If named-attribution would be load-bearing for the
  thesis, the thesis isn't ready for public yet.
- Does the structure of the prose echo the structure of an internal
  document (sprint review, OKR doc, postmortem)? If yes, restructure.
  Public readers will infer intent from form.
- Does it contain a number that's commercially sensitive? Replace with
  qualitative language ("a meaningful fraction," "the dominant share")
  or with a public benchmark ("comparable to industry norms").

## The public manifest (`wiki.json`)

Single JSON file at the mirror root. Schema:

```json
{
  "_categories": {
    "concepts":  { "display": "Concepts",  "order": 1 },
    "decisions": { "display": "Decisions", "order": 2 },
    "sources":   { "display": "Sources",   "order": 3 },
    "retros":    { "display": "Retros",    "order": 4 }
  },
  "_streams": {
    "recipes":     { "display": "Recipes (internal)" },
    "reflections": { "display": "Reflections (internal)" }
  },
  "concepts": [ { "slug": "...", "title": "...", "dek": "...", "date": "...", "updated": "...", "category": "concepts", "order": 1 } ],
  "decisions": [ ... ],
  "sources": [ ... ],
  "retros": [ ... ],
  "recipes": [ ... ],
  "reflections": [ ... ]
}
```

The renderer iterates `_categories` keys only — anything in `_streams`
is mirror-only consumption (not rendered publicly). This is how you
selectively publish: things in `_categories` go to the public site;
things in `_streams` are available to other consumers (a second agent
on a different machine, a personal AI assistant, a future you) but
never reach the public site.

## The renderer (runtime fetch model)

Component pseudocode:

```typescript
// Detail page bound to /wiki/:slug
async function fetchEntry(slug: string) {
  const wiki = await fetch("https://raw.githubusercontent.com/USER/wiki-mirror/main/wiki.json").then(r => r.json());
  const item = findInCategories(wiki, slug);  // search _categories arrays only
  if (!item) return { state: "not-found" };
  const md = await fetch(`https://raw.githubusercontent.com/USER/wiki-mirror/main/${item.category}/${slug}.md`).then(r => r.text());
  return { state: "ok", item, md };
}
```

Why this beats build-time generation:

- Body changes flow through a single `git push` to the mirror. No
  republish step on the rendering side.
- Schema-level changes (new categories, new fields) require a renderer
  update, but those are rare; routine writes don't touch the renderer.
- The renderer can be a tiny single-page component. No build pipeline,
  no static-site generator, no CMS as a content store.

Tradeoffs:

- Mirror must be public (browser fetches can't authenticate). The
  airlock IS the privacy boundary.
- Runtime fetch latency adds ~100ms per page load. Cacheable;
  acceptable for low-traffic personal sites.
- If the mirror is unreachable, the page renders empty. Add a fallback
  state to the component.

## Operational protocol

| Mode | What changed | What runs |
|---|---|---|
| Body edit | Existing entry's `.md` | Airlock + push only |
| New entry | New `.md` + manifest update | Airlock + push, then upsert in any downstream systems (CMS, search index, etc.) |
| New category | New directory + `_categories` block | Airlock + push, plus renderer-side work to surface the new category |
| Schema change | `wiki.json` shape or frontmatter | Airlock + push, plus renderer update in lockstep — coordinate before pushing |

## Adapting for your use

The pattern is renderer-agnostic. Replace the runtime-fetch component
with whatever your publishing surface supports — Framer, Astro,
Next.js, plain HTML. The contract is `wiki.json` + `{category}/{slug}.md`
files in the mirror. The airlock and the public companion writing
discipline are the load-bearing pieces; the renderer is downstream.

If you don't have a private wiki yet, the pattern works in reverse:
write your public companions first as a thought-leadership practice,
then expand into private wiki content as it accumulates.

## Why this works

- **Single source of truth on each side.** Private wiki for internal
  use; mirror for public consumption. Neither tries to be both.
- **The hand-generalized companion is the discipline.** No automation
  that "redacts" — automation gives a false sense of safety. A human
  rewriting in public voice catches what a regex misses.
- **Push-to-live UX.** Body edits don't require leaving your editor.
- **Auditable trail.** Every push to the mirror is a git commit. If a
  bad airlock slips, the diff is visible and reverting is one command.

## Related

- [[concepts/two-claude-protocol]] — the cross-laptop coordination
  pattern this recipe is the publishing layer for
- [[decisions/work-brain-mirror-public]] — why the mirror is public
- [[decisions/cms-as-router-only]] — the Framer-specific routing
  pattern when you choose Framer as the renderer

## Sources

- A working implementation shipped April–May 2026, validated across
  two ship cycles. Adapted from the same author's existing essays
  publishing pattern.

---

*Created: 2026-05-05.*
