# Wheel Call design

## Overview

Wheel Call is a browser game for predicting the hamster's next Squeekchain round against an Oracle using local play points. The existing interface resembles a race-track tote board: a dot-style title and numeric readouts, a live wheel, a call slip, a scoreboard, tickets and a round tape. The static source is `index.html`; its stylesheet and script remain unchanged by this update.

This assignment adds six social metadata tags in `index.html:9` and the share artwork `og.png`. The `dist/` export mirrors `index.html`, `favicon.svg` and `og.png` byte for byte. Documentation records the existing design rather than introducing new interface rules.

## Colors

The canonical tokens are the hex values in `index.html:20`.

| Token | Value | Role |
| --- | --- | --- |
| `--board` | `#10131c` | Page, share-card background and theme color |
| `--panel` | `#171c29` | Panels, scoreboard and chips |
| `--panel-2` | `#1e2435` | Band controls and progress track |
| `--line` | `#2a3147` | Borders, separators and board pattern |
| `--fg` | `#ece8dc` | Primary text and share-card description |
| `--muted` | `#8e95aa` | Labels and secondary text |
| `--amber` | `#f2b33d` | Title, primary action, focus and share-card title |
| `--tile`, `--run` | `#5b86ff` | Run band and chart marks |
| `--nap` | `#7a82a0` | Nap band |
| `--trot` | `#4fb8a4` | Trot band |
| `--zoom` | `#f2b33d` | Zoomies band |
| `--win` | `#7fd492` | Wins and live status |
| `--loss` | `#ec7262` | Losses and feed errors |

The opaque 1200×630 PNG uses the board, amber and foreground colors, with antialiasing at letter edges. Its generated dot-matrix title reads `WHEEL CALL`; the description reads `Call the Squeekchain hamster's next round. Beat the Oracle.` It needs no fonts at runtime. Calculated WCAG contrast for the exact solid image colors is 9.97:1 for amber/navy and 15.14:1 for light text/navy. These calculations do not describe every antialiased edge pixel or disabled game state.

## Typography

Fonts are declared in `index.html:25` and requested from Google Fonts in `index.html:17`. Existing system and monospace fallbacks remain available when external fonts fail.

| Role | Family and fallback | Implemented treatment |
| --- | --- | --- |
| Body and actions | `Bricolage Grotesque`, `Avenir Next`, `Segoe UI`, system sans | Body 15px / 1.45; action 15px / 800 |
| Brand and key numbers | `Doto`, `IBM Plex Mono`, system monospace | Brand `clamp(38px,7vw,64px)` / 900 / .9; .02em spacing |
| Timers, labels and data | `IBM Plex Mono`, system monospace | Labels 11px with .12em spacing and uppercase; data use tabular numbers |

Other existing sizes include 12, 13, 14, 16 and 17px for captions/controls; 26px odds, 28px round identifiers, 30px scoreboard values and `clamp(40px,6vw,56px)` work totals. CSS requests weights 400, 500, 600, 800 and 900 across three families. The browser probe reports computed family names; it does not independently prove every supplied font weight. The share card contains rasterized lettering, not a new webfont dependency.

## Layout

`index.html:32` sets safe-area-aware page padding with a 16px inline minimum and 20px/40px block padding. The browser's existing default body margin also remains. `.wrap` (`index.html:40`) centers content at a maximum 1180px width with 18px section gaps. Panels use 18px padding. Band controls use 10px gaps; stake controls use 8px gaps and can wrap.

The top grid (`index.html:58`) has two columns of 1.05fr and 1fr; the bottom grid has equal columns. Both stack at 880px. The live wheel and data stack at 520px. The scoreboard changes from four columns to two at 700px. Band choices stay in two columns. Ticket detail text permits arbitrary wrapping; the tape has a local horizontal scroll container.

Chromium checks at 320, 390 and 1440px found no page horizontal overflow. The rendered first screen at 390×844 shows the product and wheel; the call action is below the fold. Its existing position is documented under review limitations below. Desktop shows the call slip alongside the wheel.

## Elevation & Depth

Panels and selected band tiles use tonal surfaces with 1px structural borders. The active feed dot has a glow. The fixed toast (`index.html:137`) uses the foreground color as its fill and `z-index:20`. The page background has a 14px radial-dot pattern. The share image has a solid background without the page's pattern.

## Shapes

`--r:6px` sets panel, tile and primary-button corners. Chips, stake choices and toasts are pills. Progress bars use 3px corners. The wheel is drawn on a square canvas with a circular track. The share title is formed from amber dots.

## Components

| Pattern | Source | Behavior |
| --- | --- | --- |
| Panels and headings | `.panel`, `index.html:60` | Group live data, slip, tickets and round tape |
| Band choices | `.band`, `index.html:79`; `buildBands()` | Native buttons; category stripe, hover border, selected `aria-pressed` state; disabled during loading/lock or an existing call |
| Stake choices | `.stake`, `index.html:91` | Native buttons; selected light fill; unavailable stakes disabled |
| Call action | `.btn`, `index.html:94` | Amber primary; `.ghost` secondary actions; native disabled state |
| Scoreboard | `.score`, `index.html:101` | Play-point balances and results with tabular numeric readouts |
| Tickets | `.ticket`, `index.html:112` | Stored calls, sealed/pending outcomes and settlement results |
| Rules and status | `details.rules`, `.toast` | Native disclosure; polite status announcements |

Keyboard focus uses a 2px amber `:focus-visible` outline with 2px offset (`index.html:42`). Band and stake groups have accessible names. Status has text as well as color. Reduced motion suppresses control/progress transitions (`index.html:139`) and wheel rotation (`index.html:811`). No new interaction or wallet functionality is introduced.

## Reuse

Reuse the existing semantic tokens, `.wrap`, `.panel`, `.btn` and data typography when maintaining this page. Use the amber button for the call action and `.ghost` for secondary actions. Preserve labels and native button/disclosure semantics. Generate the export with the copy command in README; do not edit the export independently. This task authorizes metadata additions, so any layout or game changes require a separate assignment.

## Review, validation and limitations

The pinned Better Interface guidance was considered across all six domains. Design guidance: [Jakub Krehel, Better Interface](https://github.com/jakubkrehel/skills/tree/267330e1adfc66a718fb65fa6918c1f06d0a689e/skills/better-interface), MIT; documentation method: [Paul Bakaus, Impeccable](https://github.com/pbakaus/impeccable/blob/9d715cc4f5564a990ca8345abfdd5df6dc9b41c8/skill/reference/document.md), Apache-2.0. This document describes source values and observed behavior.

| Domain | Checks and disposition |
| --- | --- |
| Accessibility | Native controls and names reviewed; keyboard band selection and visible focus tested; probe target and contrast checks passed. Screen readers, forced colors and a full automated accessibility audit were not performed. |
| Layout | Screenshots inspected at 390 and 1440px; overflow checked at 320/390/1440px. No horizontal overflow or measured text overlap. Existing mobile action position is a remaining issue. RTL, pseudo-localization and native-device zoom were not tested. |
| Writing | Share sentence matches the request exactly; `og:title` is `Wheel Call`. Existing action, error and empty-state text was reviewed without edits. |
| Typography | Raster card inspected at its final dimensions; live page checked at phone and desktop widths. Existing scale/weight/tracking findings remain outside the permitted change. |
| Colors | Exact share palette and opacity verified in decoded pixels. Solid share-color contrast calculated above; probe measured game contrast passed, excluding 12 text boxes over canvas/gradients/media. |
| UI | Selection, disabled/loading, sealed ticket, cancellation, reload, cross-tab state and API failure/retry exercised in Chromium. Reduced-motion source and probe checked. Animation-panel slow playback and WebKit were not tested. |

Actual commands run included the README's copy build, `node --check test/scratch/game.js`, `node test/scratch/validate.mjs`, and the supplied `taste.mjs --out test/scratch/taste` probe using the assignment's Playwright installation. Scratch scripts, fixture data and screenshots are temporary validation material, not runtime dependencies. The browser connector returned `Transport closed`; local Playwright Chromium supplied the rendered screenshots and interaction checks instead.

The probe passed **11/16** checks: render, layout, targets, contrast, glyphs, slogans, repetition, copy budget, chrome, motion and share. The following existing issues were confirmed against source and rendered screenshots; this metadata-only assignment explicitly says “no CSS, no script, no game logic,” so they are recorded rather than altered:

| Finding | Evidence | Disposition |
| --- | --- | --- |
| Call action below first phone screen | `index.html:58`, `:65`; `.top` and `.live` stack before the slip. At 390×844 the probe's call button center is y≈1206px; the test fixture places its top at y≈1184px. The first-screen screenshot confirms it is below the fold. | Existing layout; would require a layout change to meet the generic first-screen criterion. |
| All-caps labels and wide tracking | `index.html:44`, `:61`; `.label` and panel headings use uppercase and .12em spacing. Probe reports 19 content text boxes for casing and 20 for tracking. | Existing typography; two failed checks from one recurring pattern. |
| Labels classified as eyebrows | `index.html:61`, `:85`; probe reports nine, including work labels, band ranges and scoreboard labels above numeric data. | Some are data labels rather than marketing kickers; preserved with the existing board design. |
| Broad type scale | `index.html:44`, `:48`, `:84`, `:88`, `:104`; probe reports nine repeated sizes and twelve including one-offs at each measured width. | Existing typography. |
| Five font weights | `index.html:44`, `:48`, `:61`, `:84`, `:96`; computed weights are 400/500/600/800/900 across three families. | Existing typography. |

The local HTTP checks verified both the repository root and export root serve the game with status 200 and `Wheel Call` title, and the PNG with status 200 and `image/png`. Source comparison proves removal of only the six added metadata lines reproduces the original page exactly; favicon bytes also match the original. Root/export asset equality and PNG signature, 1200×630 size, opaque pixels and exact palette colors passed.

Interaction tests use deterministic API fixtures and existing fallback fonts; they do not establish live API correctness, actual on-chain settlement, or production host behavior. The probe did contact the page's existing external font/API hosts, but is not a chain-data verification. No TypeScript sources, package manifest or dependency lockfile exist; a TypeScript typecheck is not applicable. No publication was performed. The requested public image URL will serve the new image after the network publishes this export.
