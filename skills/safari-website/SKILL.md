---
name: safari-website
description: Build or extend a premium safari / tour operator website on WordPress + Elementor v4 in the Asili Trails style (Josefin Sans caps, charcoal + copper, full-bleed photography, mega menus, enquiry wizard overlay, filterable safari finder, country/park/trip templates, structured-data SEO). Use when asked to create a safari, tour or travel operator site "like Asili Trails", add pages of those types, or apply that design system to another WordPress site.
---

# Safari website (Asili Trails pattern)

The full specification is in `reference.md` (next to this file). Read it before doing anything, then follow this workflow.

## Workflow

1. **Collect inputs.** Ask for anything in "Brand inputs" that's missing: brand, base town, countries, logo, contacts, own photos and video. Never invent awards, licences, reviews or prices.
2. **Inspect the target site.** Read it server-side (WordPress MCP or WP-CLI), not with parallel crawling: theme, active plugins, Elementor version, existing pages, menus. Back up anything you will touch into a web-blocked folder.
3. **Global chrome first.** Build the header, mega menus, top bar, mobile menu, enquiry overlay and footer in one sandbox/mu-plugin PHP file, driven by a single `menu()` array, then confirm it renders on an empty page.
4. **One template per page type** (home, safaris finder, safari detail, country, park guide, activities, travel guide, about, blog, 404). Get each one approved, then clone it by element ID for the rest.
5. **Dynamic blocks in PHP.** Maps, trip grids, circuits, carousels and CTA bands are injected server-side into empty `{prefix}-slot-*` divs from post meta (JSON), so editors change data, not layout.
6. **Content.** Write original copy for countries → places → trips → itineraries. Every trip page gets a trip-profile JSON (countries, places, style, activities) that pre-fills the enquiry wizard.
7. **SEO.** Use one source for meta and JSON-LD, with per-page meta fields, and turn off the duplicate output from the SEO plugin.
8. **Verify.** Check 1440 / 1024 / 375px, keyboard focus, AA contrast (the accent needs light/photo/on-accent steps), console errors and a test enquiry end to end. Then share screenshots.

## Rules

- Copy patterns and structure, never other operators' text or photos.
- Plain colour backgrounds, no patterns.
- Override theme-reset hover colours on every new button and link.
- Publish Elementor documents after editing (autosave trap), and clear the CSS cache after editing global classes.
