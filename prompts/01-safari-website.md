# Prompt: build a safari / tour operator website like Asili Trails

Reference build: **https://asilitrails.com** (WordPress, Hello Elementor theme,
Elementor v4 free atomic widgets, no Elementor Pro).

Copy everything below the line into a new Claude Code session that can reach your
WordPress site, for example through a WordPress MCP server (Novamira, the Elementor
MCP) or SSH/SFTP. Fill in the `{{…}}` values first.

---

Build a complete, premium safari operator website for **{{BRAND}}** on
**{{SITE_URL}}** (WordPress). Match the structure, look and behaviour described
below. Write all copy originally: never copy text, reviews, prices, licences or
photos from other operators' sites, even ones I point you to for inspiration.

## 1. Brand inputs

- Brand name {{BRAND}}, base town {{e.g. Arusha, Tanzania}}, countries we sell {{Tanzania, Kenya, Uganda, Rwanda, Zanzibar}}
- Logo: {{horizontal SVG lockup}} + {{square icon}}; the name is split into two words, the second word in the accent colour
- Contact: email {{EMAIL}}, phone/WhatsApp {{+000…}}, Instagram {{URL}}, address {{…}}
- Hero video {{own MP4}}, plus own photos where possible. Stock photos must be from free licences, and every one gets credit meta
- Real facts only: no "#1 on TripAdvisor"-style badges, licences, awards or reviews unless I give you proof

## 2. Look and feel

- **Font**: Josefin Sans (Google Fonts) for everything. Headings, eyebrows, buttons and nav are ALL CAPS with generous letter-spacing; body is sentence case.
- **Palette** (CSS custom properties): `--ink #313131` (body text), `--black #202020` (dark bands, footer), `--accent {{#BE8F6E copper}}`, `--grey #6B6B6B`, `--line #E4E4E1`, `--off #F4F4F2` (light bands). Plain colour backgrounds only. No patterns, textures or topo lines.
- **Contrast rule**: the accent fails AA as text on white and as white text on itself. Use a darker step (`#8F6345`) for accent text on light backgrounds, a lighter step (`#EBCDB4` + soft text-shadow) on photos, and dark `#202020` text on accent buttons. Automate this: a small footer script checks each accent-coloured element's real background and sets `data-ink="light|photo|onaccent"`.
- **Buttons**: "text-bar" style: a thin vertical bar, then an uppercase label (`│ EXPLORE`); solid accent button only for the main Enquire CTA (hover = lighter accent + 1px lift).
- Big full-bleed photography, generous whitespace, thin hairlines, subtle scroll reveals (off under `prefers-reduced-motion`).
- Theme reset traps to override: Hello Elementor gives `button:hover` a pink background and `a:hover` navy. Every custom button and link needs explicit `:hover/:focus` colours.

## 3. Global chrome (one PHP file in a sandbox/mu-plugin, not Elementor)

- **Top bar** (38px): short trust line ("Private safaris from {{base}}"), email, phone.
- **Fixed header**: logo left, nav centre, solid accent **Enquire** button right (icon-only square ≤1024px). Transparent over the hero, solid after scroll.
- **Mega menus** per item (Safaris, Destinations, Traveller Info, Blog, About Us, Partner With Us): link column + thumbnail grid ("Experiences", "Safari styles") with a "View all →" header. Data comes from one `menu()` PHP array that also feeds the footer.
- **Mobile menu**: full-screen, accordion sub-menus, Enquire button.
- **Enquiry overlay**: full-screen, opens from any `#enquire` link, own photo background. It's a step wizard: (1) countries → "places you'd like to see" for the ticked countries, then activities filtered to those places; (2) dates, party size, budget, safari style; (3) contact details with a country-code phone select. Every trip page has a JSON "trip profile" (countries, places, style, activities) that pre-fills the wizard and tags matching activities "Suggested". Also served as a normal page at `/enquire/?trip={slug}`.
- Submissions are saved as a private custom post type (`enquiry`, `subscriber`) with a readable "Key: value" body, and a branded HTML email goes to the team and the guest. Use SMTP, not PHP mail.
- **Footer**: columns Safaris, Experiences, Explore, Get in touch (email, phone, address, round social icons), Mailing list form. Below: a full-width giant brand lockup (scroll reveal), a row of country links, then copyright · legal links (Privacy, Terms, Booking & Cancellation, Cookies, Photo credits).

## 4. Sitemap and page templates

Build one template per type, then clone it by element ID for every page of that type.

| Page | Sections in order |
|---|---|
| **Home** | Hero (full-bleed autoplay video, 720p `<source media>` for phones) · Welcome · Top destinations carousel (active card raised, text panel and section background crossfade to the active country, 6s autoplay, swipe, arrows, dots) · Bespoke · Safari styles · Numbered packages slider (01–05 rail, prev/next peeks) · Experiences · Why us · Reviews · Travel guide teaser · How planning works · Our story |
| **Safaris** `/safaris/` | Hero · Finder: card grid with a sticky left filter sidebar on desktop (chips in rows: region/park, style, duration), faceted counts on every chip, empty chips hidden, URL kept as `?region=&style=`; horizontal chip rows on mobile · Tailor-made CTA |
| **Safari detail** `/safaris/{slug}/` | Hero · Facts strip (duration, countries, style, from price) · Overview · Day-by-day itinerary accordion · Included / not included · How to join · More safaris (cards sharing places) · CTA band |
| **Destinations** `/destinations/` | Hero · interactive globe (MapLibre GL + OpenFreeMap tiles, free, no key) with pins linking to guides and country filter buttons · country rows |
| **Country** `/destinations/{country}/` | Hero ("See {country} safaris" button) · Guide intro · {Country} safaris (auto-built from trip profiles, chips Safaris / Kilimanjaro / Day trips, 6 + "Show all") · Where to go circuits · Leaflet map row · Parks grid · Key facts · Month-by-month calendar (best/good/fair/wet) · Things to do · Combine with · FAQ (8 Qs) · CTA |
| **Park / place guide** `/destinations/{country}/{park}/` | Hero · Facts (size, best time…) · About · Highlights · Planning tips · Trips that go here · CTA |
| **Activities** `/activities/` + experience pages | Same as Safaris finder (region + type chips) / same as safari detail with a "How the day unfolds" timeline |
| **Travel guide** `/travel-guide/` | Hero · Jump-to links · Best time · Costs · Packing · Safety · Compare · CTA |
| **About** | Hero · Story · Why us · Process · Team · Vehicles gallery (thumbs, counter, full-screen `<dialog>`) + original top-down SVG seating plans · Reviews · Impact · FAQ |
| **Partner with us** | Hero · dark partner form card |
| **Blog** `/blog/` | Dark title band · featured sticky post · category chips · 3-column cards. Single post: full-bleed hero, 720px column, quick-answer box, auto "In this guide" TOC when 3+ H2s, question-style H2s, tables, FAQ details, CTA, 3 related |
| **404** | Full-bleed own photo, "This trail went quiet", buttons Home / Browse safaris / Plan my trip, popular links |
| Legal pages, photo credits | Simple styled text pages |

- Every closing CTA band: full-bleed photo with vignette, centred translucent panel (max 44rem, accent top border), zoom/fade on scroll.
- Map pins, park cards and "things to do" link to real guide pages, and map pin names must equal tab labels.

## 5. Content

- Countries → parks/places guides → trips → day-by-day itineraries, original copy, realistic durations and routes.
- Safari styles as filter keys: migration, luxury, family, honeymoon, fly-in, midrange, photographic, birding, kilimanjaro, day trip (each needs a chip, a mega-menu thumb and an option in the enquiry form).
- Blog: evergreen guides written for scanning and AI answers (best time, migration month by month, visa, health, Big Five, tipping, family, balloon, first safari, culture). Drip-publish them one a day.

## 6. SEO and performance

- One SEO source (a small PHP file reading per-page meta `_seo_title`, `_seo_desc`, `_faq`): meta description, Open Graph/Twitter, JSON-LD (TravelAgency, WebSite, WebPage, BreadcrumbList, TouristTrip for safaris, TouristDestination for countries, TouristAttraction + geo for parks, FAQPage, BlogPosting). Turn off the SEO plugin's duplicate output and let it keep canonical, robots, sitemap and llms.txt.
- Fill empty image alts from the media library. Compress images (≤2400px heroes), H.264 faststart video with no audio.
- Security: deny-all `.htaccess` on data/backup folders, block `*.bak*`, `.sql`; HSTS, nosniff, SAMEORIGIN, Referrer-Policy and Permissions-Policy headers.

## 7. Working rules

- Global chrome and dynamic blocks live in sandbox PHP files rendered server-side via `the_content` / `wp_footer` (no flash of unstyled content). Page bodies use Elementor v4 atomic widgets with global classes prefixed `{{p}}-`; empty "slot" divs (`{{p}}-slot-map`, `-trips`, `-circuits`) mark where PHP injects dynamic content.
- Back up any file or Elementor document before changing it, and keep backups in a folder blocked from the web.
- Elementor gotchas: published pages stage edits in an autosave until you publish the document; clear Elementor's CSS cache after editing global classes; use absolute URLs for background images in classes.
- Don't crawl the site with parallel requests (hosting bot protection blocks the IP). Read data server-side instead.
- Before saying it's done: check every template at 1440px, 1024px and 375px, check keyboard focus, contrast (AA) and the console, submit a test enquiry, and send me screenshots.
