# eu-peers-meta

Machine-readable metadata and operational documentation for [eu-peers.eu](https://eu-peers.eu).

---

## ⚠️ PLATFORM MIGRATION CHECKLIST

**When moving away from Webflow, complete ALL of the following before decommissioning the old site:**

### Metadata files — move to new platform root
- [ ] Serve `llms.txt` at `https://eu-peers.eu/llms.txt` (plain text, not HTML-wrapped)
- [ ] Verify `robots.txt` is regenerated and includes `Sitemap:` directive
- [ ] Verify `sitemap.xml` is auto-generated or manually created for all public pages
- [ ] Add schema markup (`Organization`, `Event`, `Article`, `FAQPage`) to relevant templates — these were never added in Webflow
- [ ] Remove the `<link rel="llms-txt">` header reference (no longer needed when file is at root)

### Content that needs manual migration attention
- [ ] **Learning Lab sessions** — moved from standalone pages to the Resources section. Confirm the 7 session URLs in `llms.txt` still resolve; update if URL structure changes
- [ ] **Knowledge Hub taxonomy** — 122+ assets categorised by type and category; confirm the taxonomy structure is preserved
- [ ] **Showcases** — 27 cases with 28 CMS fields per item; all field bindings must be remapped
- [ ] **Events lifecycle** — upcoming/past event separation is date-filter driven; replicate this logic
- [ ] **Localized pages** — 8 languages × ~15 page types; decide which to carry forward vs. retire
- [ ] **Inline scripts** — `object-position: top center` CSS fix, Piwik Pro consent banner; both registered as Webflow inline scripts
- [ ] **Glossary collection** — newer CMS collection; verify migration includes it

### DNS and redirects
- [ ] Preserve all existing URLs or set up 301 redirects from old paths to new
- [ ] Decommission `eupeers.webflow.io` staging subdomain
- [ ] Update this repo's GitHub Pages URL if the file moves to the main domain

### External references to update
- [ ] CORDIS project page link
- [ ] HumHub publishing module endpoints (if Webflow API calls are replaced)
- [ ] Train the Trainer minisite at `nstancioff.github.io/eu-peers-training/` — check for any cross-links
- [ ] Piwik Pro site configuration (new domain or path changes)

### Update this repo
- [ ] Revise `llms.txt` to reflect new site structure, URLs, and CMS architecture
- [ ] Update `MAINTENANCE.md` with new platform-specific procedures
- [ ] Archive the Webflow-specific sections of the maintenance doc

---

## Contents

| File | Purpose |
|------|---------|
| `llms.txt` | Structured site description for LLMs ([llmstxt.org](https://llmstxt.org/)) |
| `MAINTENANCE.md` | Operational procedures for maintaining EU PEERS web infrastructure |
| `README.md` | This file — repo overview and migration checklist |

## Deployment

Currently hosted via GitHub Pages at `nstancioff.github.io/eu-peers-meta/`.

Referenced from the Webflow site via a `<link rel="llms-txt">` tag in the site header (to be registered via Webflow scripts API).

**Target state:** served directly from `eu-peers.eu/llms.txt` once the platform migration is complete.

## Maintenance schedule

| What | When | How |
|------|------|-----|
| `llms.txt` content review | When new Learning Lab sessions are published, new Showcases added, or site structure changes | Edit `llms.txt`, push to main |
| `MAINTENANCE.md` review | Quarterly, or when platform/tooling changes | Edit and push |
| Migration checklist | At platform transition kickoff | Work through checklist above |

Last verified against live site: **April 2026**.
