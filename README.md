# public/ — the airlock

Everything here is eligible to cross to the personal-laptop mirror
(`work-brain-mirror` repo). Nothing outside this directory is ever shipped.

> Wikilinks (`[[category/slug]]`) appear only as the leading token of list items under `## Related` headings. Do not embed inline. The Framer renderer uses a regex-based resolver that depends on this convention.

## Writing discipline

A file under `public/` is not a filtered copy of a private file. It is a
**hand-authored, generalized companion** — same idea, same structure,
but:

- No customer or payer names
- No dollar figures or lives-covered counts tied to the employer
- No internal product or project codenames
- No colleague names
- No ticket IDs, page IDs, channel IDs, or internal URLs
- No roadmap specifics — talk about the *pattern*, not the plan

If a concept can't be generalized without gutting it, it doesn't belong
here yet. That's a feature, not a bug.

## Two-tier tagging

Every file in `public/` must have frontmatter:

```yaml
---
personal: true        # always — you wrote it, so yes
public: true|false    # true = also goes on the personal website
---
```

- `public: true` → flows to the personal site via Quartz
- `public: false` → stays in the personal mirror but not published

## The safety net

`.export/export.sh` runs a glossary check before anything ships. Any hit
hard-fails the export with a diff of the offending term. The glossary
is maintained in `.export/glossary.txt`.

## Flow

1. Write a generalized companion here
2. `~/work-brain/.export/export.sh` (dry-run) — confirms it's clean
3. When ready, `~/work-brain/.export/export.sh --push` — pushes to mirror
4. Personal laptop pulls on cron; Quartz builds the public-tagged subset
