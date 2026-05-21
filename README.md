# Off Grid Atlas
## 1. What this app is

**The Off-Grid Atlas** is a field-manual style website that scores all 50 US states for off-grid living and homesteading. Each state is rated across five domains (water, legal, soil, energy, climate risk), combined into a weighted total score, and shown in a sortable atlas with deep-dive state pages and a side-by-side comparison tool.

- **Published URL:** https://homestead-scorecard.lovable.app
- **Preview URL:** https://id-preview--feb7b808-a380-42c7-b77c-60f9ff935c55.lovable.app
- **Tone:** editorial, topographic, "USGS field manual" â€” not a SaaS dashboard.

---

## 2. Tech stack

- **Framework:** TanStack Start v1 (React 19, file-based routing in `src/routes/`)
- **Build:** Vite 7
- **Styling:** Tailwind CSS v4 via `src/styles.css` (semantic tokens in oklch)
- **Hosting:** Cloudflare Workers (edge)
- **Backend:** None yet. No Lovable Cloud, no database, no auth. All data is static in `src/lib/states-data.ts`.

---

## 3. File / folder map

### Pages (routes)
| File | URL | Purpose |
|---|---|---|
| `src/routes/__root.tsx` | â€” | Root layout + global head tags |
| `src/routes/index.tsx` | `/` | Atlas home: all 50 states, sortable + filterable list |
| `src/routes/states.$slug.tsx` | `/states/:slug` | Deep-dive page per state |
| `src/routes/compare.tsx` | `/compare` | Side-by-side state comparison |
| `src/routes/methodology.tsx` | `/methodology` | Scoring system explained |
| `src/routes/contact.tsx` | `/contact` | Contact page with mailto + Gmail/Outlook options + copy-to-clipboard |

### Components
- `src/components/SiteChrome.tsx` â€” site header (logo, nav, Buy Me a Coffee button) and footer.
- `src/components/ScoreVisuals.tsx` â€” score bars / visual primitives.

### Data + logic
- `src/lib/states-data.ts` â€” the canonical dataset: every state with `name`, `abbr`, `region`, `scores` (water/legal/soil/energy/climate), narrative blurbs, and notes.
- `src/lib/scoring.ts` â€” domain definitions, weights, total-score math, and score-band thresholds.
- `src/lib/utils.ts` â€” small shared helpers.
- `src/lib/error-capture.ts`, `src/lib/error-page.ts` â€” error boundary helpers.

### Routing / infra
- `src/router.tsx`, `src/start.ts`, `src/server.ts` â€” TanStack Start bootstrap.
- `src/routeTree.gen.ts` â€” auto-generated, do not hand-edit.
- `vite.config.ts`, `wrangler.jsonc` â€” build + Cloudflare config.

### External APIs / integrations
- **None.** No third-party APIs, no fetch calls, no auth. The Buy Me a Coffee and Contact buttons are plain outbound links (`https://buymeacoffee.com/Britus` and `mailto:britus1290@gmail.com`).

---

## 4. Page layouts

### Header (every page) â€” `SiteChrome.tsx`
Sticky header with a thin "topo rule" line underneath.
`[â—¬ logo + "The Off-Grid Atlas"]  Â·Â·Â·Â·Â·Â·  [Atlas] [Compare] [Methodology] [Contact] [â˜• Coffee]`

### `/` Atlas index
- Hero / intro band
- Controls: search box, region filter (All / West / Midwest / South / Northeast), sort selector (total or any single domain)
- Ranked list/grid of states, each linking to its detail page

### `/states/:slug` State detail
- State name, region, headline summary
- Total score + band (excellent / good / mixed / poor / avoid)
- Per-domain breakdown with bars and narrative notes
- Long-form notes on water rights, dwelling/septic code, soil, energy, climate risk

### `/compare`
Pick multiple states and view their domain scores side by side.

### `/methodology`
Explains the five domains, their weights, the Â±range per domain, and how the total is computed.

### `/contact`
Three explicit options (default mail app, Gmail web, Outlook web) plus a "copy email" button â€” so the contact path works even when no mail client is configured.

### Footer
Three-column: brand blurb Â· reference links Â· disclaimer.

---

## 5. Scoring model (must stay consistent)

Five domains, each scored in a signed range, weighted, normalized to a âˆ’100â€¦+100 total:

| Domain | Range (Â±) | Weight | What it covers |
|---|---|---|---|
| Water | 50 | 0.32 | Wells, rainfall, aquifers, surface water, harvesting law |
| Legal | 40 | 0.26 | Dwelling code, septic, zoning, RV/tiny-home, HOAs |
| Soil | 30 | 0.16 | USDA class, drainage, organic matter, perc |
| Energy | 25 | 0.13 | Solar, slope aspect, wind, hydro |
| Climate Risk | 25 | 0.13 | Wildfire, flood, tornado, extreme heat |

Bands: excellent â‰¥ 50, good â‰¥ 20, mixed â‰¥ âˆ’20, poor â‰¥ âˆ’50, otherwise avoid.

---

## 6. Decisions made so far

1. **No backend / no Cloud.** Project is fully static; state data is hand-curated in `states-data.ts`. Don't introduce a database without being asked.
2. **Editorial / topographic visual identity.** Keep the field-manual tone (mono uppercase nav, "stamp" labels, topo rule lines). Don't drift into generic SaaS.
3. **Separate routes, not hash anchors.** Each major section (Atlas, Compare, Methodology, Contact) is its own route with its own `head()` metadata.
4. **Contact via dedicated page, not raw `mailto:`.** A bare `mailto:` link silently fails for users without a configured mail client, so the Contact page exposes Gmail, Outlook, default mail, and a copy-email fallback. The destination address is `britus1290@gmail.com`.
5. **Buy Me a Coffee link** points to `https://buymeacoffee.com/Britus`, opens in a new tab, sits to the right of the nav.
6. **Colorado scoring was relaxed** to reflect lighter restrictions on off-grid water (rain-harvesting allowances) â€” water score increased accordingly. Any future revisits to CO water rules should preserve this intent.

---

## 7. Summary of questions asked & answered

| User asked | Resolution |
|---|---|
| Fix Colorado scoring given easier off-grid water rules | Updated `states-data.ts` to raise CO's water score |
| Create a file with the project code for GitHub | Provided exportable code package |
| Add a Contact button that hides my email | Added Contact nav item â†’ dedicated `/contact` route with Gmail/Outlook/mail/copy options; destination `britus1290@gmail.com` is never shown in raw href on header |
| Contact button didn't work | Replaced inline `mailto:` with the Contact page (works without a desktop mail client) |
| Republish needed? | Yes â€” code changes only go live on the published URL after Publish |
| Add Buy Me a Coffee icon right of Contact | Added `Coffee` lucide icon button linking to `buymeacoffee.com/Britus`, nav shifted left to fit |
| How many credits left? | Can't read; user checks via workspace name â†’ credit bar, or Settings â†’ Plans & Credits |

---

## 8. Current feature list

- [x] All-50-state atlas with search, region filter, and sort by total or any domain
- [x] Per-state deep-dive pages with narrative + scored breakdown
- [x] Multi-state compare view
- [x] Methodology explainer
- [x] Contact page with mail-client-agnostic options + copy email
- [x] Buy Me a Coffee link in header
- [x] Editorial topo/field-manual visual system
- [x] SEO metadata per route (title, description, og tags)
- [x] Static, no-backend deploy on Cloudflare Workers

---

## 9. Recommended next steps

Ordered roughly by impact Ã· effort.

1. **County-level drill-down (highest user value).** State-level scores hide huge variation. Add a second tier: pick a state â†’ see 3â€“5 representative counties with their own water/legal notes. Even a curated 50â€“100 county sample would massively increase trust.
2. **Source citations.** Add a small footnotes block on each state page linking to the statutes / state agency pages behind the scores. Builds credibility and makes future updates auditable.
3. **"Last reviewed" date per state.** A `lastReviewed: "2026-05"` field on each state entry, shown on the detail page. Off-grid law changes; visible freshness matters.
4. **Downloadable scorecard.** "Download this state as PDF" â€” a one-page printable summary. Natural lead magnet and shareable artifact.
5. **Lightweight analytics.** Plausible or Cloudflare Web Analytics to see which states get the most attention; informs where to invest research time.
6. **Email capture (requires Cloud).** "Notify me when scores change" newsletter signup. Would need Lovable Cloud + an email provider.
7. **User-submitted notes / corrections (requires Cloud + auth).** Bigger lift; only worth it once traffic justifies moderation.
8. **Compare page polish.** Add a domain-by-domain delta view and an "export comparison" action.
9. **Dark mode pass.** Verify all semantic tokens read well in dark; the topo aesthetic could look excellent in dark.
10. **OG image generation.** Per-state social share images (state silhouette + total score). Big lift on click-through from social.


