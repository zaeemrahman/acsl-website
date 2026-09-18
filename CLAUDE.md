# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file redesign of acslbd.com, the website of Associated Capital Securities Limited (ACSL), a stock brokerage in Chattogram, Bangladesh. Status: draft v2, demo-ready, not launch-ready.

Files:
- `acsl-website.html` — the whole site. Inline CSS, ~25 lines of vanilla JS, no build step, no framework, no image files, no server code.
- `ACSL-website-handoff.pdf` — the full developer handoff (research, design tokens, open client questions, deployment plan). Read it before any non-trivial change.

All page content was taken from the live acslbd.com and rewritten in plain English. Do not invent facts about the company. The one exception already on the page is the Bangla line ("in Chattogram since 1996"), which was written by the author and is unverified.

## Commands

There is no build, lint, or test runner. Verification is manual:

```bash
# Open in a browser (check the 900px / 480px breakpoints and the menu button)
open acsl-website.html
```

The site is light-only as of v3 (19 September 2026). Dark mode was removed on purpose: no world-class brokerage marketing site ships one, and it caused the v1 contrast bug. Do not reintroduce it.

Tag balance was previously checked with a Python tag-balance parser and contrast by calculation. Not yet done: real-browser screenshots, Chrome/Safari/Firefox/Android testing, Lighthouse, screen-reader pass.

## File layout (top to bottom)

1. `<head>`: meta, description, font preconnects, one `<style>` block ordered as tokens, resets, header, hero, facts, services, open, about, branches, help, footer.
2. `<header class="site-header">`: logo, mobile menu button, `<nav id="site-nav">`, header actions.
3. `<main>` with one `<section>` per region, each carrying its anchor id: `#top`, facts strip (no anchor), `#services`, `#open`, `#about`, `#branches`, `#help`.
4. `<footer class="site-footer">`.
5. One inline `<script>` at the end of `<body>` (menu toggle only; accordions and smooth scroll are native).

Search for `<!--` comments to find region boundaries and for `--` to find every colour token.

## Editing rules

- Change only what the request asks. It is one file, so an unrelated edit is easy to make and hard to spot.
- Dark bands (e.g. the "Open a BO account" band) use a fixed hex, `#0B2E2B`, not `--ink`, so they stay independent of the token palette.
- Keep intact: the three Google Fonts links (Newsreader, IBM Plex Sans, Hind Siliguri, each with a system fallback), the `prefers-reduced-motion` guard on `.spark`, `color-scheme: light` on `:root`, the explicit `body` background, and `scroll-margin-top` on sections.
- Colour tokens live on `:root` only. The favicon is an inline SVG data URI in `<head>` that mirrors the logo mark; if the logo changes, update it too.
- The logo is a placeholder inline `<svg class="logo-mark">` that appears twice, header and footer, each marked with an HTML comment. Edit both. When the official logo arrives, replace with an `<img>` at 38px height or embed as a `data:` URI to stay single-file. The official PNG is at `https://www.acslbd.com/wp-content/uploads/2022/03/Logo-top-removebg-preview-removebg-preview.png` but the server blocks automated downloads.
- Copy style: sentence case, plain verbs, no all-caps labels, no arrows appended to links. `--marigold` is the single accent (focus ring, timeline "today", checklist ticks).
- All external links open in a new tab with `rel="noopener"`.
- Keep the file self-contained. The only network requests are the three Google Fonts families.

## Published demo (claude.ai artifact)

The live demo is a claude.ai artifact: `https://claude.ai/artifact/UJXddsT4N9MELZLeyvgicc`. Update it in place, never create a new one:

1. `Artifact read` with that `url` to pull the current published version.
2. Edit the file.
3. Verify tag balance and reason through both themes.
4. `Artifact publish` with `file_path` = the edited file, `url` = the demo URL, `favicon` = 📈.
5. Note what changed in the reply and update the Status row in the handoff summary table if it exists in the doc being maintained.

Publishing without `url` creates a second artifact instead of updating this one.

Artifact hosting rules (CSP-enforced, fail silently): scripts only from cdnjs.cloudflare.com, cdn.jsdelivr.net/npm/, cdn.tailwindcss.com, code.jquery.com; stylesheets only from fonts.googleapis.com; no remote images; no fetch to other sites; no `window.claude` or `window.storage`; file under 16 MB.

## Open items (need the client, not the code)

Unresolved as of 19 September 2026. Answers from ACSL override anything in the handoff or this file.

- Official logo source file.
- Head office address: About page says Agrabad, Contact page says BSCIC Industrial Area, Fauzdarhat. The page uses the Contact address.
- Canonical login URL: page uses `clientportal.acslbd.com` everywhere; old site also used `acslbd.com/portal/login`. Confirm `trade.acslbd.com` is still the trading platform.
- Real password reset process (page currently says use the login page reset or email it@acslbd.com).
- Old site's Account Open page links to a leftover staging site, `maxtechbd.soetex.com`. Tell ACSL.
- Regulatory disclosures a BSEC-regulated TREC holder must display. Nothing was carried over because the old site had none.
- Native-speaker check of the Bangla line, or remove it.
- Whether an iOS app link exists (only Android was found) and whether branch phone numbers are current (old site copyright reads 2022).

## Deployment plan

Static host, zero hosting cost. Rename to `index.html`, commit to a Git repo, create a Cloudflare Pages project with no build command and repository root as output, add `acslbd.com` and `www.acslbd.com` as custom domains, update DNS at the registrar. Leave the `clientportal`, `trade`, and email DNS records untouched; those are separate systems this redesign does not cover.

If the site grows past one page, split by anchor into `index.html`, `about.html`, `branches.html`, `help.html` sharing an external stylesheet.
