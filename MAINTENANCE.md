# EU PEERS — Web Infrastructure Maintenance Guide

**Last updated:** April 2026
**Maintainer:** Nicholas Stancioff
**Backup contact:** Ankur (media@) during absences

---

## 1. Platform overview

EU PEERS operates across three platforms:

| Platform | URL | Purpose | Access |
|----------|-----|---------|--------|
| **Webflow** (public site) | eu-peers.eu | Public-facing website, CMS content | Webflow Designer + Data API |
| **HumHub** (intranet) | eu-peers.humhub.com | Consortium collaboration space | Member login; Jenny-Claire has admin |
| **GitHub Pages** | nstancioff.github.io | Training minisite, metadata files | Git push |

### Webflow identifiers
- **Site ID:** `65fa6bbec0dbb7181a8df0fc`
- **Staging URL:** eupeers.webflow.io
- **Live URL:** eu-peers.eu

### Key CMS collection IDs
| Collection | ID | Approx. items |
|------------|----|---------------|
| Events | `6613be96bdbfee726b23b072` | 30+/year |
| News | `660e67f2523acb1ad47d86bc` | ~77 |
| News Categories | `660e67fc445fe440df1e019a` | — |
| News Authors | `660e6824445fe440df1e1d0c` | — |
| Event Schedules | `6613c6b3ebc1305913b95db1` | — |
| Speakers | `6613c693e9cfe5234242c5df` | — |
| Showcases | `66154830451997f1ea65b5eb` | ~27 |
| Showcase Tags | `661548efe368ca45a6135b7b` | — |
| Members | `660bef89e4ece0b34dd53b59` | 229+ |
| Knowledge Assets | `661f68e8df7f3338ccce6a6a` | 122+ |
| FAQs | `660a77b7cbee8be3fb2bdd98` | — |
| Glossary | `69691c821246f36e806ed271` | — |

### Default reference IDs (used when creating News items)
- Category "EU Peers": `663646e5667fb464ac075deb`
- Author "EU Peers Consortium": `663646fcc47b979335bc15a5`
- Event-schedule placeholder: `67d9441195bf010af0cae772`

---

## 2. Publishing workflow

### Two separate publish systems

Webflow has two independent publish mechanisms. **Both** must be triggered; skipping either leaves changes invisible on the live site.

1. **Designer publish** — pushes structural and template changes (layout, styles, components). Triggered from the Webflow Designer UI.

2. **CMS content publish** — pushes content changes to CMS items. Requires two API calls in sequence:
   - `publish_collection_items` (up to 25 item IDs per call)
   - `publish_site` (triggers a full site deployment)

### Standard operating procedure

1. Make CMS content updates via API (items remain in staged/draft state)
2. Confirm changes look correct on the staging URL (eupeers.webflow.io)
3. Only publish after explicit confirmation
4. If `publish_site` returns a 429 rate limit error (common after high-volume sessions), publish from the Webflow Designer UI instead

### Bulk update guidelines

- Batches of **3–5 items** when content fields contain large HTML
- Batches of **10 items** can hit payload limits
- Always verify the first batch before proceeding with the rest

---

## 3. Content operations

### Adding a News article

1. Create item in the News collection with:
   - Title, slug, body content (HTML in rich text field)
   - Category reference: use `663646e5667fb464ac075deb` for general EU Peers news
   - Author reference: use `663646fcc47b979335bc15a5` for consortium-attributed articles
   - Thumbnail/banner image: must be uploaded manually via Webflow Designer Assets panel (shortcut: J), then CDN URL passed to the API
2. Stage the item (do not publish automatically)
3. Confirm content, then publish via `publish_collection_items` + `publish_site`

### Managing Events

**Upcoming events:** Published items with a future date appear in the Events listing automatically via date filter.

**Past events:** Leave published — the date filter moves them to Past Events automatically. Do **not** archive past events; archiving removes them from the site entirely, including Past Events.

**Archive only:** cancelled events, test entries, or items that should never be publicly visible.

**`registration-url-2` field:** Must be set to the event's own relative URL path (e.g., `/events/[slug]`), not an anchor. This is what navigates visitors from the listing page to the event detail page.

### Uploading assets

**Webflow asset uploads are always manual.** No MCP tool or Data API endpoint supports programmatic asset upload. All images and PDFs must be:
1. Uploaded through the Designer's Assets panel (keyboard shortcut: J)
2. CDN URL copied from the Assets panel
3. CDN URL passed to the relevant CMS field via API

### Locating CMS items

- **Recently edited items:** `sortBy: lastPublished`
- **Browsing alphabetically:** `sortBy: name` with pagination offsets
- **Checking total count:** `limit: 1` with pagination metadata
- **Slug-based lookup is unreliable:** Events slugs do not reliably reflect page titles. Browse and paginate instead.
- **Filters are exact-match only:** `list_collection_items` does not support partial or contains matching on slug or name fields.

---

## 4. CMS template constraints

### Template pages are MCP-blind

Webflow Designer MCP tools **cannot read or interact with CMS collection template page elements.** They return cached site-wide components (nav, footer) instead of template-specific content. All template-level changes must be made manually in the Designer, step by step.

### Collection List edits propagate automatically

Editing a template-level element (e.g., button text) inside a Collection List wrapper only needs to be done once — it applies to all repeated cards. This also cannot be done via MCP; Designer only.

### Showcases template redesign (in progress)

Adopting the Campaign for Cities visual design onto the Showcases CMS template. All 28 collection fields must remain bound. Next step: add a full-width banner image hero above the `hero-margin-fix` element, bound to the Banner/Thumbnail Image field. This is a Designer-only task.

---

## 5. Known issues and deferred fixes

### Active issues
- [ ] Hamburger nav not working on mobile
- [ ] Footer navigation missing: Community Life, Learning Lab, Campaign for Cities
- [ ] Learning Lab URL inconsistency (canonical path is `/learning-lab-v-1`)
- [ ] Campaign for Cities naming inconsistency across pages

### Deferred pagination fixes

Three collection-backed pages have unnecessary pagination hiding content. Pagination is only needed above Webflow's 100-item Collection List maximum.

| Page | Path | Items | Needs pagination? |
|------|------|-------|-------------------|
| News | `/news` | ~77 | No — remove pagination |
| Success Cases | `/success-cases` | ~27 | No — remove pagination |
| Learning Lab | `/learning-lab-v-1` | ~18 | No — remove pagination |
| Members | `/members` | 229+ | Yes — keep |
| Knowledge Hub | `/knowledge-hub` | 122+ | Yes — keep |

### Resolved issues (for reference)
- Cookie consent: Finsweet `fs-cc.js` removed; Piwik Pro banner is the sole consent layer
- Image crop: `object-position: top center` CSS fix registered as inline script
- Expired registration URLs: replaced across past events
- Past events: bulk past-tense conversion completed
- Learning Lab "Book a seat" button: corrected
- "Pre-Register" button: repurposed to "Join the Community" → `/membership-application`

---

## 6. Inline scripts

Registered via the Webflow scripts API. Review before any platform migration.

| Script | Purpose | Location |
|--------|---------|----------|
| `object-position: top center` CSS | Fixes image cropping on card thumbnails | Site-wide |
| Piwik Pro tracking + consent banner | Analytics and GDPR compliance | Site-wide |

To list current scripts: use `data_scripts_tool` → `list_registered_scripts` with site ID `65fa6bbec0dbb7181a8df0fc`.

---

## 7. Analytics

**Platform:** Piwik Pro
**Purpose:** Site traffic, engagement, and community health indicators for consortium reporting (CINEA)

### Reporting cadence
- Quarterly one-page PDF with reach, engagement, and community health indicators
- Data feeds into D3.5 deliverable (enriched website report)

### Outstanding setup
- Piwik Pro API reporting script: needs site ID and API token from Nicholas
- Automated quarterly report generation: planned but not yet implemented

---

## 8. HumHub integration

### Publishing module
A `webflow-publisher.zip` module plus standalone Python/Flask app enables consortium colleagues to publish Webflow content without direct Webflow access.

**Status:** Built, functional for local testing. Pending: authenticated publish-to-Webflow button inside HumHub replacing the manual "publish now" step.

### Access levels
- **Nicholas:** regular member (not admin)
- **Jenny-Claire Keilmann:** admin authority
- Temporary admin access: Nicholas has committed to requesting this for a community health audit (prerequisite for EU PEERS II platform design)

---

## 9. Related infrastructure

### Train the Trainer minisite
- **URL:** https://nstancioff.github.io/eu-peers-training/
- **Stack:** React/Vite/shadcn-ui, bundled to single HTML (383K)
- **Status:** Beta (D5.2); pending consortium buy-in before country-specific module layer
- **Country data:** structured forms for France, Belgium, Germany, Spain, Latvia, Lithuania, Estonia; Latvia form to be completed by Nicholas

### D3.5 deliverable (website report)
- Enriched version (v1.1, 12 pages) with Piwik Pro data, site architecture, Knowledge Hub taxonomy, development roadmap
- **Outstanding manual steps:** insert EU logos over yellow placeholders, add three screenshots, refresh Word TOC, confirm Events Q1 2025 data discrepancy

### EU PEERS II planning
- Kickoff meeting: targeted first week of September 2026
- Vision: replace Webflow + HumHub with a single purpose-built platform
- Prerequisite: crawl/audit the live HumHub instance for real usage patterns
- Migration timeline: 8–16 weeks realistic
- EU hosting preferred (Hetzner, Cloudways Frankfurt)
- Candidate platforms: WordPress, Wagtail, headless CMS (to be evaluated)

---

## 10. Key contacts

| Person | Role | Email | Notes |
|--------|------|-------|-------|
| Jenny-Claire Keilmann | Project coordinator, Climate Alliance | j-c.keilmann@klimabuendnis.org | Maintains issue tracker shared with CINEA; HumHub admin |
| Sébastien Descours | Filao Labs | — | Leads renovation financing training workstream |
| Marcus | Climate Alliance | — | Available for coordination calls |
| Valentina Cabal & Paula Ferrando | GNE Finance | — | Contributed Business Models Learning Lab report |
| Francesca Hugony | ENEA | — | Italian contact point |
| Ankur | — | media@ | Backup contact during Nicholas's absences |

---

## 11. Document generation toolchain

For deliverables, reports, and presentations:

| Tool | Use |
|------|-----|
| `docx-js` via Node.js | Word document generation |
| `validate.py` | Document validation |
| `soffice --headless` | PDF conversion from docx |
| `pdftoppm` | Visual verification of PDF output |
| `pandoc [file.docx] -t markdown` | Reading uploaded Word docs into context |
| `openpyxl` (read_only=True) | Reading Excel files |

---

## 12. Maintenance calendar

| When | Task |
|------|------|
| **After each Learning Lab session** | Add session to `llms.txt`; publish post-event report |
| **After adding Showcases** | Update item count in `llms.txt` |
| **Quarterly** | Review analytics via Piwik Pro; generate reporting PDF |
| **Before consortium meetings** | Verify all upcoming events are published; check for stale content |
| **At platform migration** | Follow the checklist in `README.md` |
| **Annually** | Review llms.txt for accuracy; update consortium partner list if membership changes |
