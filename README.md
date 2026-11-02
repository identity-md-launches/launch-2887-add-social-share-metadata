# Wheel Call

A browser game for [Squeekchain](https://squeekchain.com/), the hamster-powered proof-of-work chain.

Each Squeekchain round lasts 10 minutes and records how much work the hamster's wheel produced. In Wheel Call you call the **next** round before it starts:

| Band | Work in the round |
|---|---|
| Nap | 0 |
| Trot | 1–299 |
| Run | 300–549 |
| Zoomies | 550+ |

Odds are fixed when you place a call. An AI **Oracle** seals its own call with the same stake and always takes the band it thinks is most likely; its call is revealed when the round starts. Every call settles on the real on-chain round result. Seeds are play points kept in your browser and have no cash value.

## How it works

- Static site: `index.html` plus `favicon.svg`. No build step, no wallet, no backend.
- Live data comes from the public Squeekchain API at `https://api.squeekchain.com`:
  - `/v1/status` (polled every 5 s): wheel rpm, speed, odometer and work in the running round
  - `/v1/core`: round configuration (genesis time, 600 s rounds) and the latest settlements
  - `/v1/events?category=protocol`: settlement history, paged with `cursor`, used to settle calls and to train the Oracle
  - `/v1/measurement/stats?range=7d`: the hamster's usual hours
- The Oracle blends how often the hamster keeps running or wakes up (from ~300 settled rounds) with the hamster's usual hours, and whether it is running right now.
- Calls lock 10 seconds before a round starts. A call whose round cannot be resolved from chain data is refunded.
- Open tabs share one saved state: a call placed in one tab shows up in the others, and a second call for the same round is refused.

## Run locally

Serve the folder with any static server, for example `npx serve .`, and open the page.

## Static export and sharing

No dependency installation or compilation is needed. Rebuild the production export by copying the three runtime files:

```sh
mkdir -p dist
cp index.html favicon.svg og.png dist/
python3 -m http.server 4173 --directory dist
```

Open `http://localhost:4173/` to preview the game. Both the repository root and `dist/` contain an `index.html`; use `dist/` as the publishing root. The favicon uses a relative URL. Social metadata points to `https://wheel-call-ab9f.imd.site/og.png`, an opaque 1200×630 PNG with the navy/amber title and light description.

Validation for this metadata update: the copy build, JavaScript syntax check (`node --check` on the extracted inline script), export/source byte comparisons, and local HTTP checks passed. `/` returned 200 with title `Wheel Call`; `/og.png` returned 200 `image/png`, from both publishing roots. Chromium checks passed for keyboard band selection, stakes, placing/cancelling calls, cross-tab state, reload, rules, and API retry using deterministic test responses. TypeScript checking is not applicable to this plain HTML/JavaScript project; there are no package manifests or lockfiles.

The pinned design probe passed 11/16 checks, including sharing, rendering and responsive overflow. Existing typography findings and the mobile call action below the first screen remain under this task's explicit restriction against CSS, script and game changes. See [DESIGN.md](DESIGN.md) for review coverage and limitations. Hosting/public URL responses and actual round settlement were not verified by this local update.
