# Milieu: MiHQ hero + subscribe form (developer handoff)

Two self-contained components to bring into the new Milieu site. Each one is a working demo page. Open it, scroll it, resize it, then lift the marked code into your build.

| Component | Demo page | What it is |
|---|---|---|
| **MiHQ hero** | `hero/index.html` | Top fold, then a pinned scroll sequence: the orb glides to centre, the MiHQ lockup and line fade in, then four product cards glide in one by one. Includes the mobile and portrait-tablet layout. |
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
- `.hero-eco-wrapper` is **440vh** tall. Inside it, `.hero-eco-sticky` is pinned (`position: sticky`) for the whole scroll.
- One scroll progress value (0 to 1 across the wrapper) drives everything. The timeline constants are at the top of the JS:

  | Constant | Value | Moment |
  |---|---|---|
  | `ORB_LOCK_END` | 0.18 | Orb finishes gliding from the bottom to centre, and stays large |
  | `HQ_IN_START` / `HQ_IN_END` | 0.21 / 0.31 | MiHQ lockup and orbit rings fade in |
  | `COPY_IN_START` / `COPY_IN_END` | 0.28 / 0.36 | "Your research. Our panel. One platform" fades in after the logo |
  | `CARD_START`, `CARD_STEP`, `CARD_DUR` | 0.37, 0.10, 0.14 | Cards glide in one at a time, overlapping slightly |
  | `FADE_OUT_START` / `FADE_OUT_END` | 0.84 / 0.95 | Scene fades out |
  | `ORB_EXIT_START` | 0.86 | Orb shrinks up and away |

- **Gliding cards:** cards follow a *smoothed* copy of the scroll (`glideCards`, eased each animation frame), so they drift into place rather than tracking the scroll exactly.
- **Layout is calculated in JS** (`layoutHeroEco`) on load and resize. It sizes the orb, draws the orbit rings around it, and places each card using its `data-angle` attribute.
- **Mobile and portrait tablets** (width under 760px, or width/height under 0.8): the orb sits at the top and the cards stack as a deck under it. Each new card rises from below and the earlier cards step back.
- **Layering:** the orb video sits at `z-index: 3` with `mix-blend-mode: lighten`. The stage (cards and MiHQ copy) is at `z-index: 4`, above the orb.
- **Load flash fix:** the orb video stays at `opacity: 0` until the JS has placed it. The JS then adds `.is-ready` and the orb fades in, so it never flashes in the top-left corner on load.

### Integrating
1. Copy the CSS, HTML and JS blocks, plus `hero/assets/`. Update the asset paths in the HTML if your folder structure differs.
2. **Nav height:** set `NAV_HEIGHT` at the top of the JS to your fixed nav's height. It's 64px in the demo.
3. Run the JS once the hero HTML is in the page, for example at the end of `<body>`, on `DOMContentLoaded`, or after the component mounts. It only touches elements inside the hero.
4. **Buttons:** "Request demo" and "Contact us" use placeholder `.btn` classes. Swap in your site's button component and links.
5. **Scroll length:** to shorten or lengthen the sequence, change `.hero-eco-wrapper { height: 440vh }`. The timeline is proportional, so it all stays in step.
6. **Frameworks** (React, Next.js and similar): render the HTML as a component, import the CSS, and run the JS inside an effect. Remove the scroll and resize listeners and cancel the animation frame on unmount.
7. **CSS name clashes:** class names are prefixed (`hero-eco-*`, `eco-*`, `hq-*`). Only `.btn`, `.badge-dark`, `.badge-dot` and the keyframes `hero-in`, `pulse-dot`, `eco-bob`, `eco-bob-lg`, `orb-in` are generic. Rename them if they clash with yours.

### Copy (final, word for word)
- Pill: Decision intelligence system
- H1: We are a Decision / Intelligence Company
- Sub: Milieu unifies data, research, and AI so businesses can move first and move right
- Centre: MiHQ lockup, then "Your research. Our panel. One platform"
- Cards: Audience / MiAudience, Reports / MiReports, Services / MiResearch (MiCustom, MiBus, MiRetail), Solutions / MiBrand, MiTemplates

House style: no em dashes in visible copy, and no full stops at the end of the lines above.

---

## 2. Subscribe form

### How it works
- Name and Email are required, and the email must look valid. The browser shows its own messages if not.
- "Have more time? **Tell us what interests you**" opens a panel of six interest chips. They're optional.
- On submit, the button turns into a green tick and the card expands to show:
  **Thanks for signing up** / Stay ahead with the latest consumer insights across the region

### Not connected yet
The form does **not** send data anywhere. In the JS, find the `TODO` in the submit handler and post `FormData` to your newsletter service (Mailchimp, HubSpot, etc.). Fields: `name`, `email`, and `interests` (can be several). Show the thank-you state only after a successful response, and add an error state if the request fails.

### Integrating
1. Copy the CSS, HTML and JS blocks. No assets needed.
2. The colour tokens are set on `.subscribe` itself. Map them to your design tokens if you have them.
3. It sits inside your page's content container, directly under the article cards.

---

## Viewing the demos locally
Open `hero/index.html` in a browser through a local server, because some browsers block local video:
```
python3 -m http.server 8000
```
Then visit http://localhost:8000/hero/ and http://localhost:8000/subscribe/

If GitHub Pages is switched on for this repo, the live demos are at `https://<owner>.github.io/<repo>/hero/` and `/subscribe/`.
