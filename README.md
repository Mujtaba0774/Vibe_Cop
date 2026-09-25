# Vibecop — Website

![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white) ![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB) ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black) ![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

Marketing website for **Vibecop**, which runs senior-engineer audits of software products, especially ones built with AI coding tools (Claude Code, Cursor, Lovable, Bolt, Copilot). The site explains the six services, shows a sample audit report, publishes prices and case studies, and asks visitors to apply for an audit.

![Home page](https://mujtabaasif.vercel.app/assets/projects-screenshots/vibecop/landing.webp)

**Live site:** https://vibecop.io/ · **Portfolio:** https://mujtabawd.vercel.app/

## Tech stack

| Area | Choice |
| --- | --- |
| Framework | Next.js 16 (App Router, Turbopack) + React 19, plain JavaScript/JSX |
| Styling | Tailwind CSS v4 via `@tailwindcss/postcss`; the design system is `styles/theme.css` |
| Fonts | Inter Tight, self-hosted in `styles/fonts/` |
| Icons | Font Awesome Free 6 |
| Rendering | Every page is prerendered to static HTML. There are no API routes, no database and no environment variables. |

## Getting started

Requires Node.js 20.9 or newer (a Next.js 16 requirement).

```bash
npm install
npm run dev        # http://localhost:3000
```

| Script | What it does |
| --- | --- |
| `npm run dev` | Dev server with hot reload |
| `npm run build` | Production build. Prerenders all pages |
| `npm run start` | Serve the production build |
| `npm run lint` | `next lint` |

## Project structure

```
app/
├── layout.jsx              <html>/<body>, site metadata, the one stylesheet import, motion boot script
├── not-found.jsx           404
├── (site)/                 pages WITH promo bar slot + header + footer
│   ├── layout.jsx          that chrome, in one place: Header, Footer, Atmosphere, MotionRuntime, AuditModalProvider
│   ├── page.jsx            /
│   ├── about/ team/ process/ services/ services/[slug]/ case-studies/ pricing/ faq/ contact/ blog/ …
│   └── (blog)/             the six blog layout demos + /single-blog
└── (bare)/                 pages with NO chrome
    └── coming-soon/
components/
├── ui/                     Container, Section, SectionHeading, Button, Card, Badge, Modal, Sparkline
└── …                       Header, Footer, AuditModal, SampleReportModal, Accordion, forms, grids, …
content/                    ALL page copy and data, as plain JS (see "Content")
lib/cn.js                   class-name joiner
styles/
├── theme.css               the design system: Tailwind v4 + tokens + shared classes
├── fonts.css / fonts/      self-hosted Inter Tight
public/assets/images/       artwork, logo, favicons
tools/                      the original HTML→JSX port scripts (historical, see tools/README.md)
docs/                       project PDF + screenshots
```

## Pages and URLs

Routing is the Next.js App Router, one folder per page. Route groups in parentheses (`(site)`, `(bare)`, `(blog)`) choose a layout and don't appear in the URL.

| URL | Page | In menus? |
| --- | --- | --- |
| `/` | Home | — |
| `/about` | About: story, principles, team, "the shift" | Header |
| `/services` | Services overview: review areas + pricing | Header |
| `/services/:slug` | Service detail, six pages from one template (below) | Header, footer |
| `/case-studies` | Case studies with category filters | Header (Pages) |
| `/pricing` | The four engagements + FAQ | Header |
| `/faq` | FAQs | Header (Pages) |
| `/process` | The 48–72 hour audit plan and handoff kit | Footer |
| `/contact` | Audit request form | Header button, footer |
| `/privacy-policy`, `/cookie-policy`, `/term-of-use` | Legal pages | Header (Pages) |
| `/team` | Team grid | **Not linked** |
| `/blog`, `/single-blog` | Blog index and the one article page | **Not linked** |
| `/one-column` … `/six-column-full-width` | Blog layout demos from the original template | **Not linked** |
| `/coming-soon` | Countdown holding page, no header/footer | **Not linked** |
| `/single-services` | Permanent redirect to `/services/architecture-audit` (`next.config.mjs`) | — |

Any unknown URL shows `app/not-found.jsx`.

Service slugs: `architecture-audit`, `security-audit`, `ai-built-product-review`, `production-readiness`, `cto-advisory`, `continuous-assurance`. They are generated at build time by `generateStaticParams`, and an unknown slug returns a 404.

## Header navigation

Header, footer and mobile menu all read [content/navigation.js](content/navigation.js):

- **About** links to its page.
- **Services** opens a dropdown with All Services plus the six services. The list is built from `SERVICE_PAGES`, so a new service shows up here automatically.
- **Pages** opens a dropdown with Case Studies, Pricing, FAQs, Privacy Policy, Cookie Policy and Terms of Use.
- **Pricing** links to its page.
- **Contact Us** is the orange button on the right.

Dropdowns open on hover (closing waits 180 ms, so the pointer can cross the gap to the panel) and on click or tap. Esc closes a dropdown and returns focus to its button, and a click outside closes it too. On mobile, the menu button opens a panel where each dropdown expands in place.

The footer has two link columns (`FOOTER_NAV`: Services and Company), social links (`SOCIAL_LINKS`) and a newsletter sign-up. Keep it to two columns: the 12-column footer grid has no room for a third.

## Content

Page copy is data, not markup. [content/](content/) holds one module per subject, and pages map over them:

| File | Holds |
| --- | --- |
| `navigation.js` | Header menu, footer columns, social links |
| `home.js` | Hero, the five audit stages, stat cards, the home-page FAQ override |
| `service-pages.js` | The six service detail pages |
| `services.js` | "What We Review" cards, Find Your Fit questions, engagement blueprints |
| `pricing.js` | The four priced engagements (used on Home, Services, Pricing) |
| `audit.js` | Audit application modal: text and form fields |
| `sample-report.js` | The sample audit report (fictional "Acme SaaS"; keep it fictional) |
| `about.js`, `team.js`, `process.js` | Story/principles, team members, timeline + handoff kit |
| `case-studies.js`, `faqs.js` | Case studies (filters are derived from them), the seven FAQs |
| `articles.js`, `posts.js`, `single-post.js`, `blog-sidebar.js` | Blog |
| `legal.js` | Privacy, cookie and terms text |

Rules: **no JSX in content files** (`home.js` keeps the headline as an array of lines for this reason), and **no invented figures**. A service without a published price has `price: null`.

### Adding a service

Add a record to `SERVICE_PAGES` in `content/service-pages.js` with `slug`, `title`, `navLabel`, `lede`, `summary`, `price`/`priceNote`, `includedTitle`/`included`, `findingsTitle`/`findingsLede`/`findings`, `scopeTitle`/`scope` and `steps`. The page is created at `/services/<slug>`, and the header dropdown and footer pick it up automatically. To list it in the "What We Review" grid on `/services`, also add it to `REVIEW_AREAS` in `content/services.js`.

## Lead capture

- **Audit application modal.** Every "Request an audit" CTA is an `<AuditButton/>`, which opens one shared modal owned by `<AuditModalProvider/>` in the site layout. Outside that provider (for example on `/coming-soon`), the button falls back to a link to `/contact`. The fields are defined in `content/audit.js`.
- **Sample report.** The "Sample audit output" button in the hero collage opens `<SampleReportModal/>`.
- Both modals use `components/ui/Modal.jsx`, which handles the backdrop, focus trap, scroll lock, portal, and returning focus on close.

## The design system

Everything lives in `styles/theme.css`: colours, type scale, content width, the base layer and the three treatments below. Utilities are unprefixed and preflight is on.

```jsx
<Section tone="surface">
  <SectionHeading title="Heading" lede="Body copy." />
  <ul data-stagger className="grid gap-6 md:grid-cols-3">
    <li className="vc-card vc-card-hover p-8">…</li>
  </ul>
</Section>
```

Use tokens rather than raw values: `bg-brand`, `text-muted`, `bg-surface-2`, `max-w-page`, `text-display` / `text-h1` / `text-h2` / `text-h3`. If a value appears twice, it belongs in `@theme`, not in a page. Heading sizes use `clamp()`, so they scale with the viewport.

| Token | Hex |
| --- | --- |
| `brand` | `#f95b34` |
| `ember` / `crimson` | `#ff8a16` / `#b21515` |
| `brand-dim` | `#b53d1e` |
| `surface` / `surface-2` / `surface-3` | `#0a0909` / `#121010` / `#1b1718` |
| `muted` | `#bababa` |

### The three layers

The page is one continuous environment rather than a stack of flat bands:

| Layer | What it is |
| --- | --- |
| `<Atmosphere/>` | One **fixed** element per layout: the orange/crimson glows, the faded grid and the grain. It sits behind everything, is inert, and is mounted once. |
| `.site-section` | A radial wash on each section that alternates sides down the page. `tone="surface"` is a translucent lift, not an opaque fill. |
| `.vc-card` | The glass treatment every panel shares: fill, fine border, lit top edge, blur. `.vc-card-hover` adds the lift. |

Because the atmosphere sits behind everything, **nothing full-width may paint an opaque background**. An opaque background covers the glow and meets the next section on a hard line. The hero and the sub-page banner both end on `transparent` for this reason.

`.vc-card`, `.vc-btn-*` and `.vc-field` are plain CSS rather than utility bundles, because they rely on pseudo-elements and multi-layer backgrounds. They are **unlayered**, so they deliberately win over Tailwind utilities. This is why `.vc-btn` guards its `position: relative` behind `:where(:not(.absolute)…)`.

## Server and client components

Pages and cards are server components. Only the parts that need interactivity are client components:

| Behaviour | Component |
| --- | --- |
| Menu, dropdowns, mobile nav | `Header.jsx` |
| Audit application / sample report | `AuditModal.jsx` + `AuditButton.jsx` / `SampleReportModal.jsx` + `SampleReportButton.jsx` |
| FAQ accordions | `Accordion.jsx` |
| Case-study filters | `CaseStudyGrid.jsx` |
| Post galleries | `PostMedia.jsx` |
| Countdown | `Countdown.jsx` |
| Forms | `ContactForm.jsx`, `NewsletterForm.jsx`, `CommentForm.jsx`, `FitFinder.jsx` |
| Back to top | `BackToTop.jsx` |
| Every scroll animation | `MotionRuntime.jsx` |

### Motion

`MotionRuntime.jsx` mounts once per layout and is the only thing that drives animation. Pages opt in with **data attributes** instead of importing a hook, which keeps every page and card a server component:

| Attribute | Effect |
| --- | --- |
| `data-reveal` | Fade and rise on entry. `="blur" / "scale" / "left" / "right"` pick a variant. |
| `data-stagger` | The same effect, applied to direct children one after another. |
| `data-draw` | Marks connector rules and the sparkline draw as revealed. |
| `data-count` | Counts the rendered figure up once, keeping its `+` / `%`. |
| `data-parallax="0.08"` | Drifts the element against the scroll. |

The runtime uses three listeners in total: one `IntersectionObserver`, one scroll handler and one `MutationObserver` (client-side navigation swaps the tree, so new sections have to be picked up as they arrive). Reveals fire once and then stop observing.

Know these five things before editing it. Each one is a bug that has already been fixed once:

* **Don't store wiring state on the element.** `reactStrictMode` is on, so in development React mounts the effect, cleans it up and mounts it again. A DOM "already bound" flag survived that cycle, and nothing on the page revealed, in `next dev` only. The bound set is now a `WeakSet` scoped to the effect run. **Check reveals in `next dev`, not only in `next start`.**
* **Revealed state is an attribute (`data-revealed`), never a class.** React owns `className`, so any re-render (a FAQ opening, a filter changing) dropped a JS-added class and the element faded back out.
* **Anything the viewport has jumped past is revealed outright.** After a reload restores the scroll position, sections above it never intersect. The sweep only runs on jumps larger than one viewport.
* **Entrances animate `translate`/`scale`, never `transform`.** Card hover lifts use `transform`, and a reveal using it would silently cancel them.
* **`data-count` reads the rendered text, not the attribute.** JSX serialises a bare `data-count` as `"true"`. The markup always ships the final value, so the figure is correct with JS off and there is no layout shift.

Everything degrades gracefully. The reveal styles only apply once a small inline script in `app/layout.jsx` adds the `motion-ready` class. A timer removes that class if `MotionRuntime` hasn't started within 3 seconds, so a failed hydration shows a plain page rather than a blank one. `prefers-reduced-motion` turns off animation and parallax.

## Current feature status

| Feature | Status |
| --- | --- |
| All pages, header/footer, mobile menu | Working |
| Six service pages, `/single-services` redirect | Working |
| Sample report, case-study filters, accordions, countdown | Working |
| Per-page titles/descriptions, favicons | Working |
| Audit application, contact, newsletter and comment forms | Validate and confirm, but **nothing is sent** |
| Blog | Static: every card links to the one `/single-blog` article, and the blog isn't in the menus |
| "Find Your Fit" + "Core Vibecop Engagements" on `/services` | Built but commented out in `app/(site)/services/page.jsx` |
| Promo top bar | Commented out in `app/(site)/layout.jsx` |

## Things to wire up

* **Connect the forms.** Contact, audit application, newsletter and comment forms all validate and acknowledge, but have no backend. Point their `onSubmit` at a real handler (a server action, Formspree, a CRM) and add spam protection.
* **Decide on the blog.** Either add per-post routes (`/blog/[slug]`) and a menu entry, or remove the blog and the six layout demos.
* **Remove leftovers:**
  * `Header.jsx` imports `BUY_URL`, which `content/navigation.js` no longer exports. It is unused, so remove the import.
  * `PromotionalTopbar.jsx` still references `BUY_URL`. Restore the export before switching the promo bar back on.
  * `styles/style.css`, `styles/animate.css` and `styles/tailwind.css` aren't imported anywhere and can be deleted.
* **SEO.** Add `app/sitemap.js`, `app/robots.js` and Open Graph images.

## History

This project started as a static HTML kit (`../vibecop`) that was machine-converted to JSX. That port is gone: the pages are now hand-written Tailwind, and Bootstrap 4, the kit's stylesheet and the jQuery stack were removed from the app.

`tools/` still holds the conversion scripts, for reference only. **Some of them overwrite the current pages.** Read [tools/README.md](tools/README.md) before running any of them.
