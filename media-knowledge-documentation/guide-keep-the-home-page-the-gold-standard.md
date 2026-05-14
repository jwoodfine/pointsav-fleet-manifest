---
schema: foundry-doc-v1
title: "GUIDE — Keep the documentation.pointsav.com home page the gold standard"
slug: GUIDE-keep-the-home-page-the-gold-standard
audience: vendor-internal
status: live
last_edited: 2026-05-14
editor: pointsav-engineering
bcsc_class: no-disclosure-implication
language_protocol: PROSE-GUIDE
cites:
  - ni-51-102
  - osc-sn-51-721
---

This guide is for an operator — a PointSav engineering session or a manually-acting administrator — who needs to maintain the home page of `documentation.pointsav.com` as a Wikipedia-class encyclopedic surface as the corpus grows. It covers the format invariants the home page must preserve, the featured-topic rotation procedure, and the anti-patterns that degrade the encyclopedic register.

It is the operational complement to [[knowledge-wiki-home-page-design]] (the public-facing design intent article).

## 1. Where the home page lives in the substrate

The home page is rendered by the `app-mediakit-knowledge` crate's `index()` handler at `pointsav-monorepo/app-mediakit-knowledge/src/server.rs`. The handler reads three sources at request time:

1. `<content-dir>/index.md` — the home lede (rendered Markdown body)
2. `<content-dir>/featured-topic.yaml` — the featured-pin slug plus optional `since:` and `note:` fields
3. The category subdirectories — populated from `last_edited:` frontmatter on each article by the category-bucket function

The handler composes: site header → lede (rendered from `index.md`) → featured-pin panel (when `featured-topic.yaml` resolves a valid slug) → "Browse by category" grid (all 10 ratified categories) → "Recent additions" feed (top 5 by `last_edited` desc) → site footer.

The deployment instance runs the binary as a systemd service (`local-knowledge.service`) bound to `127.0.0.1:9090`, fronted by an nginx vhost serving `documentation.pointsav.com` over HTTPS (Let's Encrypt certificate). See `guide-operate-knowledge-wiki.md` for day-2 operations detail.

## 2. Format invariants — hard rules

| Element | Rule |
|---|---|
| Welcome banner | One sentence plus structural statistics. No adjectives of self-description. |
| Featured-pin lead | Body-register paraphrase, not advertising copy; closes with "→ Read" or "(Full article...)" — never "Click here", "Learn more", or button-styled calls-to-action. |
| Featured-pin length | Target 200–280 character paraphrase of the article lead. |
| Category cards | Always render all 10 categories, even when empty. Empty cards show "0 articles — in preparation" — not hidden, not collapsed. |
| Category order | architecture / substrate / patterns / systems / services / applications / governance / infrastructure / reference / design-system. Never alphabetised, never reordered. |
| Recent additions | Top 5 by `last_edited` descending. Never more, never fewer. Always rendered when the corpus has at least one article. |
| Footer | Licence plus source repository link. No advertising, no newsletter signup, no social buttons. |

Any change that violates one of these is a regression. The reader who has internalised the muscle memory will notice; the reader who has not will encounter a degraded encyclopedic register.

## 3. Featured-pin rotation procedure

The featured-pin is rotated by editing `featured-topic.yaml` at `content-wiki-documentation/` repo root. This is content-wiki-documentation Root scope. Execute:

1. **Identify the candidate article.** A slug from any of the 10 ratified categories. The candidate must have a substantive lead paragraph that paraphrases cleanly — the engine's lead extraction reads the first 200–300 characters of the body after the frontmatter block. If the lead is thin or opens with a table, the rendered featured panel will be visually degraded.

2. **Verify the slug resolves.** From `content-wiki-documentation/`, run:
   ```
   find . -name "<candidate-slug>.md" -not -path "*/.*"
   ```
   The file must exist in one of the 10 category subdirectories. If the result is empty, the engine's render will suppress the featured panel rather than error.

3. **Edit `featured-topic.yaml`.** Replace the `slug:` field. Optional: update `since:` to today's date. The `note:` field is engine-ignored but useful for editors reading the file later.

4. **Commit via the staging-tier helper.** From `content-wiki-documentation/`:
   ```
   git add featured-topic.yaml
   ~/Foundry/bin/commit-as-next.sh "rotate featured pin: <slug> — <one-line rationale>"
   ```
   Author identity alternates per the workspace commit-toggle pattern.

5. **Stage 6 promotion** pushes the commit to canonical `pointsav/content-wiki-documentation`. The deployed instance is reloaded to pick up the new content; the next visit to `documentation.pointsav.com` renders the new pin.

**Cadence:** weekly is the current target. Daily is aspirational but requires sustained editorial labour. Skipping a week is acceptable; rotating mid-week to react to a substantive new article is also acceptable.

## 4. Anti-patterns

### 4.1 Adding a "Get started" call-to-action to the home page

A well-meaning operator may notice that `documentation.pointsav.com` lacks an obvious onramp for a first-time reader and propose adding a "Get started" panel above the lede. Do not. This is the documentation aesthetic of product marketing. The reader-to-contributor onramp is the "Other areas" footer block linking to source repositories and the contribution-onboarding articles — that is the encyclopedic register equivalent. See [[wiki-provider-landscape]] for the structural analysis of why product-marketing aesthetics degrade reference register.

### 4.2 Reducing the category grid to "active" categories only

A well-meaning operator may notice that some categories have few articles and propose hiding empty ones. Do not. The visible "0 articles — in preparation" placeholder is editorially load-bearing — it signals the platform's intended scope at launch and gives contributors a visible target. Hiding empty categories collapses the platform into "what exists" rather than "what is intended", which is the wrong scope signal for both engineering and financial-community audiences.

### 4.3 Adding promotional copy to the welcome banner

A well-meaning operator may propose extending the welcome banner from a structural statement to one containing characterisations like "the leading platform documentation." Do not. Adjectives of self-description destroy encyclopedic register. The welcome banner is one sentence plus structural statistics and no adjectives — that absence is load-bearing.

### 4.4 Reordering the category grid by article count

A well-meaning operator may propose ordering the category grid descending by article count so that the largest categories surface first. Do not. The 10-category order is ratified and encodes a structural argument — architecture is foundational; substrate contains the mechanism concepts; patterns are the recurring design shapes; systems are the OS layer; services are the autonomous runtime components; applications are user-facing; governance is the formal-decision layer; infrastructure is the deployment substrate; reference is dictionary-grade; design-system is the visual primitive library. Reordering by count loses that argument.

### 4.5 Featuring the same category for consecutive weeks

Rotating the featured-pin three consecutive weeks within a single category signals "we have one thing to talk about." Do not. Diversity across the 10 categories is editorial signal that the corpus has breadth. Acceptable: architecture → governance → services → reference → patterns → architecture. Unacceptable: three consecutive picks from any single category.

### 4.6 Removing the "Recent additions" section when the corpus is sparse

If the corpus has fewer than five articles, the engine returns however many it has. Do not add a special case to suppress the section when the count is low — the engine's behaviour is correct. "1 article added recently" is accurate and appropriate.

## 5. When the operator's discretion ends — ratification required

These changes are not the operator's unilateral call.

| Change | Ratification required |
|---|---|
| Modify the 10-category set (add, remove, or rename a category) | Operator presence plus staging-tier session |
| Add a new home-page slot (e.g., "Open editorial questions", "Random article") | Staging-tier session (substrate scope) |
| Change the featured-pin format (e.g., add an image, change the closer text) | Staging-tier session (substrate scope) |
| Modify the welcome banner to include new statistics or framing | Staging-tier session (BCSC scope — statistics that are forward-looking trigger [ni-51-102] considerations) |
| Change the recent-additions ordering (recency vs. editorial review gate) | Staging-tier session (architectural decision; affects the engine handler) |

## 6. What does not require ratification

The operator can:

- Rotate the featured-pin to any existing article slug at any cadence (per §3 above)
- Rewrite the lede text in `index.md` for editorial improvement within the format invariants of §2
- Update the `since:` and `note:` fields in `featured-topic.yaml`
- Add or improve articles in any category — the home page picks up the count and the recent-additions feed automatically
- Edit the footer text for accuracy (licence year, source repository paths) within the format invariants

These are routine operational changes. Commit via the staging-tier helper; Stage 6 promotes; the deployment picks up.

## 7. Open editorial item

The recent-additions section ranks by `last_edited`. A planned future iteration may switch to `content_reviewed_on` — a separate frontmatter field denoting last substantive editorial review. Until that switch lands in the engine, recent-additions ranks by recency. A cosmetic-only edit will surface to recent-additions; this is acceptable degradation pending the engine update.
