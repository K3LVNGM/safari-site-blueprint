# Prompt: build an "Asili Portal"-style operations dashboard on another website

Copy everything below the line into a new Claude Code session that has access to
the target site (WordPress MCP / Hostinger API / SSH). Fill in the `{{…}}` values
first. Delete any module in section 6 you don't want.

---

Build a private operations portal for my business on **{{SITE_URL}}** (WordPress).
It must be a real plugin, not page-builder widgets, and match the spec below
exactly. Keep the source in a local folder `{{LOCAL_FOLDER}}` and deploy from there.

## 1. Business details

- Business name: **{{BUSINESS_NAME}}** — the portal is called "{{BUSINESS_NAME}} Portal"
- What we sell: {{e.g. safaris / tours / services}}; a lead is one enquiry for one trip/job
- Brand tint colour: light mode **{{TINT_LIGHT}}** (must pass 4.5:1 with white text), dark mode **{{TINT_DARK}}** (lighter step, black text on it)
- Logo: {{path to horizontal SVG lockup}} plus a white version for dark backgrounds
- Login background photo: {{path to own photo}}; faded workspace photo: {{path}}, ≤1400px, ≤100 KB
- Currency {{USD}}, time zone {{Africa/Dar_es_Salaam}}, reply target {{4}} hours, "stale lead" after {{5}} days
- Where enquiries come from today: {{form plugin / custom post type / email}} — read them, do not change the public form

## 2. Architecture

- Plugin folder `{{prefix}}-portal/`: `{{prefix}}-portal.php` (defines a `VERSION` constant, bumped on every deploy and appended as `?v=` to CSS/JS), `includes/class-*.php` (one class per module), `views/shell.php` (the only server-rendered page), `assets/portal.css`, `assets/portal.js`, `assets/icons/`.
- Front-end route at `/portal/` (rewrite rule + query var), sub-routes `/portal/{screen}/`. Not inside wp-admin.
- **Single-page app**: shell.php prints one JSON boot payload (leads, settings, user, permissions); portal.js renders every screen client-side with History API routing. No framework, no build step, vanilla JS, one CSS file.
- REST namespace `{{prefix}}-portal/v1` for every write (sync, leads/{id}, settings, tasks, etc.), nonce + capability checked. Optimistic UI: update immediately, roll back with a toast on failure.
- All portal data lives in post meta prefixed `_{{p}}_` on the lead posts (stage, value, follow-up date, owner, activity log, first-reply time, last-touch time, lost reason, trip start/end, guide, vehicle, tasks array) and options prefixed `{{p}}_` for settings. Sample data is flagged `_{{p}}_demo=1` so it can be removed in one click.

## 3. Roles and access

- New role **"{{Planner}}"** with capability `{{p}}_portal`; planners are redirected out of wp-admin to /portal/.
- Administrators are shown as **"Director"** (label only, via a `wp_roles_init` filter) and see an extra "Director" nav group.
- Login page (wp-login.php) restyled: always dark, full-bleed brand photo, white logo instead of the WordPress logo, frosted-glass card, tint-coloured button.

## 4. Design system — Apple Human Interface Guidelines

Follow Apple's HIG strictly; it should feel like a native macOS/iOS app.

- **Font**: system stack only: `-apple-system, BlinkMacSystemFont, "SF Pro Text", "SF Pro", "Helvetica Neue", "Segoe UI", system-ui, sans-serif` (display: "SF Pro Display"; mono: `ui-monospace, "SF Mono", Menlo`). Apple type ramp (Large Title 34, Title 1 28, Title 2 22, Title 3 20, Headline 17 semibold, Body 17, Callout 16, Subhead 15, Footnote 13, Caption 12).
- **Every colour is a CSS token on `:root`**; dark mode is purely a token swap, under `@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) {…} }` and again under `:root[data-theme="dark"]`. Theme toggle: System / Light / Dark.
- Light tokens: `--bg #f2f2f7`, `--surface #fff`, `--surface-2 #f2f2f7`, `--surface-3 rgba(118,118,128,.12)`, `--fill-2 rgba(120,120,128,.16)`, `--line rgba(60,60,67,.18)`, `--line-2 rgba(60,60,67,.29)`, `--ink #000`, `--ink-2 rgba(60,60,67,.78)`, `--ink-3 rgba(60,60,67,.62)`, sidebar `--side rgba(246,246,248,.82)` (translucent + backdrop blur), `--bar rgba(249,249,249,.86)`, `--good #248a3d`, `--warn #c93400`, `--bad #d70015`, each with a `-soft` 13–15% tint.
- Dark tokens: `--bg #000` (true black), `--surface #1c1c1e`, `--surface-2 #2c2c2e`, `--line rgba(84,84,88,.6)`, `--ink #fff`, `--ink-2 rgba(235,235,245,.7)`, `--ink-3 rgba(235,235,245,.5)`, `--side rgba(30,30,32,.78)`, `--bar rgba(22,22,23,.86)`, `--good #30d158`, `--warn #ff9f0a`, `--bad #ff453a`.
- **One app tint only** (the brand colour), tokens `--tint`, `--tint-soft` (13% light / 18% dark), `--on-tint`. Charts use a 6-step sequential scale of the tint (6%, 20%, 38%, 58%, 80%, 100%).
- **Pipeline stage colours = Apple system colours**: New blue `#007aff`/`#0a84ff`, Contacted teal `#30b0c7`/`#40c8e0`, Quoted orange `#ff9500`/`#ff9f0a`, Deposit indigo `#5856d6`/`#5e5ce6`, Confirmed green `#34c759`/`#30d158`, Lost gray `#8e8e93`/`#98989d` (light/dark).
- Radius 12px (10px small). No card shadows; only floating layers get `0 10px 40px rgba(0,0,0,.18), 0 0 0 .5px rgba(0,0,0,.08)`.
- Components: translucent sidebar (desktop) with the full logo lockup and a small "PORTAL" label; translucent bottom **tab bar** on mobile (Today, Board, Tasks, Calendar, More); segmented controls; inset grouped lists; alert-style dialogs; sheet-style side panel for a lead; **Spotlight-style ⌘K palette** (search leads, jump to screens, run actions); HUD toasts; 44pt minimum touch targets; visible focus rings; skip-to-content link; respects `prefers-reduced-motion`.
- Faded brand photo behind the workspace via `body::before` (opacity .09 light / .13 dark).
- Must work at 375px wide with no horizontal scroll and pass WCAG AA contrast.

## 5. Core screens (everyone)

1. **Today** — KPI tiles with 14-day sparklines: New today (vs last week), Awaiting reply (red if any over the reply target), Follow-ups due (overdue count), Open pipeline value (quoted + deposit), Confirmed · 90 days, Median first reply (vs target). Below: "needs you now" list sorted by urgency, each lead showing a timer badge (ok / warn / bad by reply target and stale days).
2. **Board** — kanban with the six stages, drag and drop (and keyboard/menu move for accessibility), column totals in money, card shows name, trip, value, age badge. Moving to Lost asks for a reason.
3. **Tasks** — per-lead tasks plus general tasks, due dates, owner, done state. A booking **checklist template** in Settings auto-creates tasks relative to booking date / trip start / trip end.
4. **Calendar** — month view of booked trips (start–end), flags guide or vehicle double-bookings.
5. **Enquiries** — searchable, filterable table of every lead; opening one shows the lead sheet: all form fields, stage, value, follow-up date, owner, activity log, tasks, trip dates, guide, vehicle, and **reply templates** (merge fields like {first_name}, {trip}) that open the email/WhatsApp with text filled in and log the first-reply time.
6. **Insights** — range segmented control (30d / 90d / 12m / all): Enquiries (vs previous period), Win rate, Confirmed revenue, Average trip value, Median first reply; charts: enquiries over time (current unfinished period drawn lighter), confirmed revenue by month, stage funnel, sources, lost reasons, countries.
7. **Content** — newsletter subscribers list + CSV export.
8. **Settings** — reply templates, reply target, stale days, checklist template, theme, notifications, sample data (seed / remove).

## 6. Director-only modules (pick what you want)

- **Payments** — invoices and payments per lead, sequential invoice numbers, business details for the invoice header, public `/invoice/{token}/` page; KPIs: received this month, received this year, still to collect, overdue invoices.
- **Proposals** — itinerary proposal builder per lead, public `/proposal/{token}/` page with an Accept button (REST), KPIs sent / opened / accepted.
- **Emails** — send from the portal with wp_mail (SMTP), optional IMAP inbox fetch, history saved on the lead.
- **Guests** — guest book built from all leads (trips, booked yes/no, newsletter consent) and newsletter campaigns sent in batches by WP-Cron, with unsubscribe handling.
- **Edit website** — safe editors for the site's own content: page text/photo editor, package/product create (copy as draft), hide, delete, price; find & replace limited to text, links and SEO; contact details stored in one option and used site-wide; top bar + announcement. Back up every write (keep the last 15 per page).
- **Visitors** — first-party, cookieless analytics: a small beacon in wp_footer posts to a public REST `hit` endpoint (same-origin check, skip bots and logged-in staff, honour DNT/GPC). Store hits in a custom table, prune after 400 days by daily cron. Visitor key = daily-salted hash, never store IPs. Country from the browser time zone. KPIs: visits, pages per visit, time on page, left after one page, opened enquiry form; top pages, sources, countries, devices, form funnel.
- **Ledger feed** — read-only JSON feed `GET /wp-json/{{prefix}}-portal/v1/ledger-feed` authenticated with an `X-Ledger-Key` header (generate / regenerate / disconnect in Settings), `since` and `include_drafts` parameters, `Cache-Control: no-store`.

## 7. PWA and notifications

- Serve `/portal/manifest.webmanifest` and `/portal/sw.js`, app icons from the brand icon, "Install app" button.
- Web Push with VAPID keys stored in an option (pure PHP OpenSSL, aes128gcm). Events: new enquiry (everyone), reply and proposal accepted (Directors), 08:00 daily digest. Per-user on/off preferences and a "send test" button. Mute push while seeding sample data.

## 8. Sample data and handover

- A seeder that creates ~45 realistic sample enquiries across all stages and dates, ~25 subscribers and sample visitor hits, all flagged as demo, plus a one-click "Remove sample data" in Settings.
- Before calling it done: load every screen in light and dark mode at desktop and 375px width, move a demo lead on the board and back through the real REST API to prove saves work through the host's cache, check the browser console is clean, then send me screenshots.
- Do not touch the public enquiry form, the theme, or other plugins. Back up any existing file you must edit.
