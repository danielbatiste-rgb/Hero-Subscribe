# Milieu: MiHQ hero + subscribe form (developer handoff)

Two self-contained components to bring into the new Milieu site. Each one is a working demo page. Open it, scroll it, resize it, then lift the marked code into your build.

| Component | Demo page | What it is |
|---|---|---|
| **MiHQ hero** | `hero/index.html` | Top fold, then a pinned scroll sequence: the orb glides to centre, the MiHQ lockup and line fade in, and four product cards glide in one by one. The cards then fade away and an "Explore MiHQ" message with a See Pricing button takes their place. Includes the mobile and portrait-tablet layout. |
| **Subscribe form** | `subscribe/index.html` | Newsletter card that sits under the Latest Intelligence articles. Optional "Have more time?" interests panel and a thank-you message. |

Only these two sections are in this repo. The rest of the live site has newer copy and is **not** included on purpose, so nothing here should overwrite it.

---

## How the code is marked

Each demo page has three clearly fenced blocks. Copy what is between the markers and ignore anything labelled **DEMO ONLY**.

```
/* MILIEU HERO: CSS START */  ...  /* MILIEU HERO: CSS END */
<!-- MILIEU HERO: HTML START --> ... <!-- MILIEU HERO: HTML END -->
/* MILIEU HERO: JS START */   ...  /* MILIEU HERO: JS END */
```

The subscribe page uses the same pattern with `MILIEU SUBSCRIBE`.

DEMO ONLY parts are stand-ins for things your site already has: the reset, a placeholder nav, placeholder buttons and a "next section" block.

There's no framework and no build step. It's plain HTML, CSS and JavaScript, with no npm packages. The font is Archivo from Google Fonts.

---

## 1. MiHQ hero

### Files
```
hero/index.html
hero/assets/mihq-orb.mp4        orb video (3.6 MB, autoplay, muted, loop)
hero/assets/mihq-lockup.svg     MiHQ + "Powered by MiCortex" logo lockup
hero/assets/icon-audience.svg   card icons
hero/assets/icon-reports.svg
hero/assets/icon-services.svg
hero/assets/icon-solutions.svg
```

### How it works
- `.hero-eco-wrapper` is **560vh** tall. Inside it, `.hero-eco-sticky` is pinned (`position: sticky`) for the whole scroll.
- One scroll progress value (0 to 1 across the wrapper) drives everything. The timeline constants are at the top of the JS:

  | Constant | Value | Moment |
  |---|---|---|
  | `ORB_LOCK_END` | 0.13 | Orb finishes gliding from the bottom to centre, and stays large |
  | `HQ_IN_START` / `HQ_IN_END` | 0.155 / 0.23 | MiHQ lockup and orbit rings fade in. The logo glows as it arrives, then settles |
  | `COPY_IN_START` / `COPY_IN_END` | 0.205 / 0.265 | "Your research. Our panel. One platform" fades in after the logo |
  | `CARD_START`, `CARD_STEP`, `CARD_DUR` | 0.27, 0.075, 0.10 | Cards glide in one at a time, overlapping slightly |
  | `CARDS_OUT_START` / `CARDS_OUT_END` | 0.64 / 0.70 | All four cards fade away, and the line under the logo fades out |
  | `EXPLORE_IN_START` / `EXPLORE_IN_END` | 0.68 / 0.76 | "Explore MiHQ" title, line and See Pricing button fade in |
  | `FADE_OUT_START` / `FADE_OUT_END` | 0.88 / 0.96 | Scene fades out |
  | `ORB_EXIT_START` | 0.90 | Orb shrinks up and away |

- **Gliding cards:** cards follow a *smoothed* copy of the scroll (`glideCards`, eased each animation frame), so they drift into place rather than tracking the scroll exactly.
- **Layout is calculated in JS** (`layoutHeroEco`) on load and resize. It sizes the orb, draws the orbit rings around it, and places each card using its `data-angle` attribute.
- **Mobile and portrait tablets** (width under 760px, or width/height under 0.8): the orb sits at the top and the cards stack as a deck under it. Each new card rises from below and the earlier cards step back.
- **Fits every screen:** sizes are worked out from the visible area below the nav.
  - The locked orb is at most 84% of that height and 50% of the width, so it never runs under the nav or off-screen.
  - On load the orb peeks up from the bottom but always starts below the hero buttons.
  - Cards scale down on smaller screens (to a minimum of 62% on desktop and 80% on phones), and their slots are spaced so they never overlap each other or leave the screen.
  - The MiHQ lockup and line size themselves from the orb (`--orb-w`).
  - Tested at 1920×1080, 1440×900, 1366×768, 1280×720, 1280×600, 1180×820, 1024×768, 1024×600, 820×1180, 768×1024, 390×844, 375×667, 360×640, 320×568 and 844×390 (landscape phone).
- **Two-line headline:** each line of the H1 is wrapped in `.h1-line` (`white-space: nowrap`). The font size scales to the narrower of screen width and screen height, so it always sits on exactly two lines, from 28px on a 320px phone up to 96px.
- **Clickable logo and cards:** the four cards and the MiHQ logo are links. On hover (or keyboard focus), the cards lift, scale and glow brighter, and the logo grows 6% and glows.
- **Explore step:** once all four cards have shown, they fade away. The line under the logo is replaced by the Explore MiHQ title, line and See Pricing button. On desktop this sits inside the orb, and the group lifts slightly to stay centred. On phones it sits under the orb, where the cards were.
- **Layering:** the orb video sits at `z-index: 3` with `mix-blend-mode: lighten`. The stage (cards and MiHQ copy) is at `z-index: 4`, above the orb.
- **Load flash fix:** the orb video stays at `opacity: 0` until the JS has placed it. The JS then adds `.is-ready` and the orb fades in, so it never flashes in the top-left corner on load.

### Integrating
1. Copy the CSS, HTML and JS blocks, plus `hero/assets/`. Update the asset paths in the HTML if your folder structure differs.
2. **Nav height:** set `NAV_HEIGHT` in the JS to your fixed nav's height. It's 64px in the demo. Everything centres in the space below it, including the top-fold copy (via the `--nav-h` CSS variable the JS sets). If your nav isn't fixed, set it to 0.
3. Run the JS once the hero HTML is in the page, for example at the end of `<body>`, on `DOMContentLoaded`, or after the component mounts. It only touches elements inside the hero.
4. **Links and buttons:** these all use placeholder links. Swap in the real URLs:

   | Element | Placeholder `href` |
   |---|---|
   | MiHQ logo (inside the orb) | `#mihq` |
   | MiAudience card | `#miaudience` |
   | MiReports card | `#mireports` |
   | MiResearch card | `#miresearch` |
   | MiBrand, MiTemplates card | `#solutions` |
   | See Pricing button | `#pricing` |
   | Request Demo / Contact Us (top fold) | none yet (`<button>`) |

   "Request Demo", "Contact Us" and "See Pricing" use placeholder `.btn` classes, so swap in your site's button component. The cards and logo only accept clicks while they're visible (the JS toggles `.is-live`).
5. **Scroll length:** to shorten or lengthen the sequence, change `.hero-eco-wrapper { height: 560vh }`. The timeline is proportional, so it all stays in step.
6. **Frameworks** (React, Next.js and similar): render the HTML as a component, import the CSS, and run the JS inside an effect. Remove the scroll and resize listeners and cancel the animation frame on unmount.
7. **CSS name clashes:** class names are prefixed (`hero-eco-*`, `eco-*`, `hq-*`). Only `.btn`, `.badge-dark`, `.badge-dot` and the keyframes `hero-in`, `pulse-dot`, `eco-bob`, `eco-bob-lg`, `orb-in` are generic. Rename them if they clash with yours.

### Copy (final, word for word)
- Pill: Decision Intelligence System
- Buttons: Request Demo, Contact Us, See Pricing (all pills, CTAs and buttons use Capitalization Case)
- H1: We are a Decision / Intelligence Company
- Sub: Milieu unifies data, research, and AI so businesses can move first and move right
- Centre: MiHQ lockup, then "Your research. Our panel. One platform"
- After the cards: **Explore MiHQ** [Formerly Canvas] / Our own panel in six Southeast Asian markets, and partner panels in more than 150 countries / See Pricing
- Cards: Audience / MiAudience, Reports / MiReports, Services / MiResearch (MiCustom, MiBus, MiRetail), Solutions / MiBrand, MiTemplates

House style: no em dashes in visible copy, and no full stops at the end of the lines above.

---

## 2. Subscribe form

### How it works
- Name and Email are required, and the email must look valid.
- **PDPA consent** is a required tick box with a link to the Privacy Policy (placeholder `#privacy-policy`). The wording is a **draft for legal sign-off**: *"I agree to Milieu collecting and using my personal data to send me newsletters and updates, in line with the Privacy Policy"*. The exact text a person agreed to is sent to HubSpot with the submission.
- "Have More Time? **Tell Us What Interests You**" opens six optional interest chips: Consumer Trends, Survey Results and Data Stories, Industry Insights, Research Tips, Events and Webinars, Company News and Product Updates.
- On a successful submit, the button turns into a green tick and the card expands to show:
  **Thanks for signing up** / Stay ahead with the latest consumer insights across the region
- If HubSpot rejects the submission, a short error shows and the form stays usable: *"Something went wrong. Please try again"* (draft wording).
- On phones the order is Name, Email, consent, Submit, so the consent box is seen before submitting.

### HubSpot and source tracking (needs doing before launch)
The team needs every sign-up to show **where the person came from** in HubSpot (original source, UTM campaign, pages viewed). This broke in Aug/Sep, so please check it end to end.

HubSpot can only attribute a submission if it can tie it to the visitor's HubSpot tracking cookie (`hubspotutk`). Two ways to do that, both fine:

**Option A: HubSpot embed** (what the team asked for)
Use HubSpot's own embed code for the newsletter form (`hbspt.forms.create({ portalId, formId, ... })`) and style it to match this design using `subscribe/index.html` as the visual reference. Turn on HubSpot's data-privacy / consent options for the form, with the same consent wording and Privacy Policy link. Attribution is handled automatically.

**Option B: this custom form, posting to HubSpot** (already built in)
The JS already submits to HubSpot's Forms API (`api.hsforms.com/submissions/v3/integration/submit/{portalId}/{formId}`), with:
- `context.hutk` from the `hubspotutk` cookie, plus `pageUri` and `pageName`, which is what carries the source tracking
- `legalConsentOptions` with the consent text the person ticked
- fields `firstname`, `lastname` (split from Full Name), `email` and `newsletter_interests`

To go live, fill in `HUBSPOT.portalId`, `HUBSPOT.formId` and `HUBSPOT.subscriptionTypeId` at the top of the subscribe JS, and create a `newsletter_interests` contact property in HubSpot (or rename it to match yours). Until the IDs are set, the form runs in demo mode and just shows the thank-you message.

**Either way, these are what make tracking work:**
1. The **HubSpot tracking code** (`js.hs-scripts.com/<portalId>.js`) is installed on **every page** of the new site, not just this one. Without it there is no `hubspotutk` cookie and sign-ups show as "Offline sources" or "Direct traffic". This is the most common reason tracking breaks after a site rebuild.
2. If the site has a cookie banner, HubSpot's cookie must be allowed once the visitor accepts.
3. **Test it:** open the site through a link with UTMs (for example `?utm_source=test&utm_campaign=handoff-check`), browse a page or two, submit with a test email, then check that contact in HubSpot. Original source and the UTM campaign should be filled in.

### Integrating
1. Copy the CSS, HTML and JS blocks. No assets needed.
2. The colour tokens are set on `.subscribe` itself. Map them to your design tokens if you have them.
3. It sits inside your page's content container, directly under the article cards.
4. Replace `#privacy-policy` with the real Privacy Policy URL.

---

## Viewing the demos locally
Open `hero/index.html` in a browser through a local server, because some browsers block local video:
```
python3 -m http.server 8000
```
Then visit http://localhost:8000/hero/ and http://localhost:8000/subscribe/

If GitHub Pages is switched on for this repo, the live demos are at `https://<owner>.github.io/<repo>/hero/` and `/subscribe/`.
