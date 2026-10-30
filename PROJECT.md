# Fab Haus — Architecture & Fabrication Landing Page

Build specification for a static, single-page, full-bleed cinematic website for **Fab Haus**, an architecture firm that also fabricates and manufactures.

**Revision notes (what changed from the previous version)**
- Removed every invented fact: named projects, locations, dimensions, equipment, tolerances, tonnage and carbon figures. All unknowns are now bracketed placeholders held in one content file.
- Removed the timber/carbon calculator, materials lab, filterable showcase, project modals, consultation form and the Stitch sync modal. This is a static marketing page, not a web app.
- Removed Stitch / Google Cloud IDs, endpoints and the live dev URL. They do not belong in a spec that gets shared.
- Layout changed from bento showcase to full-bleed cinematic scenes.
- Added a full parallax, scroll-reveal and UX specification.

---

## 1. Goal and Scope

**Goal:** make a first-time visitor (homeowner, developer, or commercial client) understand in 10 seconds that Fab Haus designs *and* builds, see proof of real work, and contact the studio without friction.

**In scope:** one scrolling page, desktop-first (1440px) with a fully designed mobile layout (390px), mailto and WhatsApp contact.

**Out of scope:** login, dashboards, calculators, modals, multi-step forms, CMS, backend. If a form is wanted later, add it as a separate phase.

**Stack:** React 19, TypeScript, Vite, Tailwind CSS v4, GSAP + ScrollTrigger (via `@gsap/react`), Lenis (smooth scroll).

---

## 2. Content Rule (read first)

Nothing about the firm may be stated as fact unless the client confirmed it.

- All copy that depends on the client lives in `src/data/content.ts`, with bracketed placeholders such as `[Project Name]`, `[Typology · City · Year]`, `[X]`, `[Principal Name]`.
- Stats, client logos, testimonials, certifications and capability claims stay as placeholders, or are hidden, until verified.
- Sustainability or carbon claims are not allowed without a documented source from the client.
- The "Design + Make" columns stay as `[What we fabricate 1–3]` until the client confirms what they manufacture.

---

## 3. Design System

### Typography (two families)
- **Display:** `Plus Jakarta Sans`, tight tracking, very large on desktop (clamp 56px to 160px for section headlines).
- **Body and UI:** `DM Sans`, 16–18px, line-height 1.6.
- **Labels:** small uppercase, wide letter-spacing (`text-xs uppercase tracking-[0.2em]`), same two families.
- Project metadata is unboxed text separated by `·` or `/`. No pill badges.

### Color tokens
The client has no confirmed brand palette yet. Use these as a **proposal**, held in CSS variables so they can be swapped in one place:

| Token | Proposed value | Use |
| :-- | :-- | :-- |
| `--color-bg` | `#0A0A0A` | Page canvas |
| `--color-surface` | `#141414` | Alternate sections |
| `--color-text` | `#F2F0EB` | Primary text |
| `--color-muted` | `#A3A3A3` | Secondary text |
| `--color-accent` | `#FBBF24` | Primary buttons, process numerals, active states only |
| `--color-light` | `#F2F0EB` | Light-section background (Manifesto, Studio) |

Rules: one accent only, no gradients except a dark overlay for text legibility over photos, hairline borders at 8–12% opacity.

### Layout
- 12-column grid, 120px+ vertical rhythm between sections on desktop (80px on mobile).
- Photography is full-bleed (edge to edge). Text sits on the image or beside it, never in a boxed card.
- Use `100svh` (not `100vh`) for full-screen sections so mobile browser bars do not clip content.

---

## 4. Page Structure (top to bottom)

1. **Navbar**
   - Left: wordmark `FAB HAUS`. Right: `Work`, `Design + Make`, `Process`, `Studio`, `Contact`, and a primary `Get in touch` button.
   - Transparent over the hero. After the hero it becomes a thin blurred bar.
   - Hides when scrolling down, reappears when scrolling up.
   - Active section link is highlighted.
   - Mobile: menu button opens a full-screen drawer with large links.
2. **Hero** (`100svh`)
   - Full-bleed image or muted looping video (`[Hero Asset]`) with a dark overlay.
   - Headline: "Designed. Built. Delivered." bottom-left, very large.
   - One line of subtext, two buttons: `View our work`, `Get in touch`.
   - Scroll cue at the bottom.
3. **Manifesto** (light section)
   - One oversized statement across the full width (`[Firm statement]`).
   - Three stats: `[X] Projects · [X] Years · [X] Cities`.
4. **Selected Work** (signature section)
   - Four full-screen project panels (`[Project 1–4]`), each with a huge project name over the image, `[Typology · City · Year]` beneath, and a `View project` link.
   - Side progress indicator: `01 / 04`.
5. **Design + Make** (the differentiator)
   - Two full-bleed halves: architecture (drawing or render) and fabrication (workshop or material close-up).
   - Heading: "From drawing to built, under one roof."
   - Three short columns: `[What we fabricate 1]`, `[What we fabricate 2]`, `[What we fabricate 3]`.
6. **Process** (dark section)
   - Four steps: `01 Discover`, `02 Design`, `03 Fabricate`, `04 Install`, each with a one-line description. Numerals in the accent color.
7. **Studio**
   - Full-bleed studio or team image, short story, founder quote: `"[Principal quote]"` — `[Principal Name]`.
8. **Trust strip**
   - `[Client logos]` and `[Testimonial]`. Hidden entirely if the client has none yet. Do not fake it.
9. **Contact** (dark, full-width)
   - Headline "Let's build something real."
   - `[Email]`, `[Phone]`, `[Address]`, plus a `mailto:` button and a WhatsApp button (`https://wa.me/<digits only, country code, no + or spaces>`).
10. **Footer**
    - Wordmark, nav links, `[Instagram]`, `[LinkedIn]` (only networks the client actually has), copyright.

---

## 5. Motion System

### Principles
- Slow, confident, architectural. No bounce, no spring overshoot, no flashy effects.
- Animate only `transform`, `opacity` and `clip-path` (GPU-friendly). Never animate width, height, top or left.
- Motion explains hierarchy: it directs attention, it does not decorate.

### Tokens

| Token | Value |
| :-- | :-- |
| Ease (reveals) | `expo.out` / `cubic-bezier(0.16, 1, 0.3, 1)` |
| Ease (scrub) | `none` (linear, tied to scroll) |
| Duration, text | 0.9s |
| Duration, image reveal | 1.2s |
| Duration, small UI | 0.3s |
| Stagger, lines | 0.08s |
| Stagger, cards or columns | 0.12s |
| Reveal trigger | element top at 85% of the viewport, play once |

### Smooth scroll
- Lenis with `lerp: 0.1`, wired into GSAP (`lenis.on('scroll', ScrollTrigger.update)` and `gsap.ticker.add`).
- Touch devices use native scroll (no smooth-scroll hijack on mobile).
- In-page anchor links scroll smoothly with an offset equal to the navbar height.

### Parallax specification

| Element | Effect | Values |
| :-- | :-- | :-- |
| Hero image | Slow vertical drift and scale as the user scrolls out | `yPercent 0 → 15`, `scale 1 → 1.08`, scrubbed |
| Hero headline | Moves slightly faster than the image and fades | `y 0 → -60`, `opacity 1 → 0` |
| Project panel images | Image taller than its frame, drifts inside it | image height 120%, `yPercent -10 → 10`, scrubbed |
| Design + Make halves | Opposite directions, which reads as depth | left `yPercent -8 → 8`, right `yPercent 8 → -8` |
| Studio image | Gentle drift | `yPercent -6 → 6` |
| Contact headline | Slight upward lift on entry | `y 40 → 0` |

Implementation note: parallax images sit in a container with `overflow: hidden`, and the image is oversized (115–120% height) so edges never show during movement.

### Reveal specification

| Element | Reveal |
| :-- | :-- |
| Headlines | **Masked line reveal.** Split into lines, each line in an `overflow: hidden` wrapper, slides up from `yPercent 110 → 0`, staggered. |
| Body text and buttons | Fade and rise: `opacity 0 → 1`, `y 24 → 0`. |
| Images | **Clip-path wipe:** `inset(0 0 100% 0) → inset(0 0 0 0)` while the image inside scales `1.15 → 1`. |
| Dividers and hairlines | Draw in: `scaleX 0 → 1`, origin left. |
| Stat numbers | Count up from 0 on entry, only when the value is numeric. Placeholders such as `[X]` stay static. |
| Process steps | Activated one by one by scroll progress (see below). |

Initial hidden states are applied by JavaScript after mount (`html.js` class), never by plain CSS, so the content is not invisible if JS fails to load.

### Section choreography

**Hero**
- On load: headline lines mask-reveal (0.9s), subtext and buttons follow (0.3s later), and the nav fades in last. The whole intro is under 1.5s and never blocks interaction.
- On scroll: image drifts and scales, headline lifts and fades, scroll cue fades out.

**Selected Work (desktop)**
- The section pins. Each panel is `100svh`, and the next panel slides up over the previous one (`yPercent 100 → 0`).
- The panel underneath scales `1 → 1.06` and darkens (overlay `0 → 0.5`) as it is covered.
- Project name reveals line by line as each panel becomes active. Metadata and link fade in after it.
- The side counter updates (`01 / 04`) and a thin progress line fills.
- Total scroll length is about 100vh per panel, with no scroll-jacking beyond the pin.

**Selected Work (mobile)**
- No pinning. Panels stack at `80svh`, with the image parallax reduced and simple line reveals. Metadata is always visible.

**Design + Make**
- The two halves wipe in with opposite clip-path directions and drift in opposite directions on scroll.
- The center divider line draws in, then the heading reveals line by line, then the three columns stagger in.

**Process**
- Desktop: the section pins. Scroll progress is divided into four stages. The active step is at full opacity with its numeral in the accent color, the others sit at 25%. A progress line scrubs along the steps.
- Mobile: vertical stack, each step reveals as it enters view.

**Studio**
- The image wipes in with a clip-path and drifts. The quote reveals line by line.

**Contact**
- The headline reveals line by line. Contact details stagger in. Link underlines draw in on hover.

### Hover and micro-interactions (desktop only)
- Buttons: fill slides in from the left (0.3s), arrow nudges 4px.
- Project panel: image zooms `1 → 1.03` over 0.7s, `View project` underline draws.
- Nav links: underline draws from the left.
- All hover-revealed information (project metadata) is **always visible on touch devices**.

---

## 6. UX and Accessibility Requirements

**Navigation and orientation**
- Skip-to-content link as the first focusable element.
- Active section highlighting in the nav, and a thin scroll-progress line at the top of the page.
- Navbar hides on scroll down and shows on scroll up, so the content is not blocked.
- Every key page action is reachable in at most two clicks (`Get in touch` is in the nav and the hero, and anchors to Contact).

**Reduced motion** (`prefers-reduced-motion: reduce`)
- Disable Lenis, pinning, parallax, scale effects and count-ups.
- Replace reveals with a simple 0.2s fade. Content must never depend on animation to be visible.

**Accessibility**
- Text over photos must meet WCAG AA contrast, so use a dark overlay of at least 40% on image sections.
- Visible focus states on all interactive elements.
- Descriptive alt text for every image, and `aria-hidden` for purely decorative ones.
- Drawer menu: focus trap, `Esc` closes it, and focus returns to the menu button.
- Hero video: muted, `playsInline`, no audio, with a pause control, and a still-image fallback.
- Headline line-splitting must keep the original text readable by screen readers (`aria-label` on the heading).

**Mobile**
- Pinning and heavy parallax are disabled below 768px, replaced by lightweight reveals.
- Tap targets are at least 44px. Container padding is 20px. Respect safe-area insets.
- Use `100svh`, never `100vh`.

**Performance budget**
- LCP under 2.5s, CLS under 0.1, and no jank while scrolling at 60fps on a mid-range phone.
- Hero image preloaded and served as AVIF or WebP with responsive `srcset`. All other images lazy-loaded.
- Every image has explicit width and height or an aspect-ratio box to avoid layout shift.
- Video is compressed, muted and short, with a poster image. It is not loaded on slow connections or when data-saver is on.
- Fonts: `font-display: swap`, subset to Latin, preloaded.
- Kill or refresh ScrollTrigger instances on unmount and on resize (`ScrollTrigger.refresh()` after images load).

---

## 7. File and Component Structure

```
/src
├── App.tsx                    # Page assembly, Lenis + ScrollTrigger setup
├── main.tsx
├── index.css                  # Tailwind v4, color tokens, global styles
├── data/
│   └── content.ts             # ALL client copy and placeholders
├── hooks/
│   ├── useLenis.ts            # Smooth scroll, reduced-motion aware
│   ├── useReveal.ts           # Fade/rise, clip-path and line reveals
│   ├── useParallax.ts         # Scrubbed parallax for images
│   └── useReducedMotion.ts
├── components/
│   ├── Header.tsx             # Hide/show navbar, active link, drawer
│   ├── ScrollProgress.tsx
│   ├── Hero.tsx
│   ├── Manifesto.tsx
│   ├── SelectedWork.tsx       # Pinned panels (desktop) / stacked (mobile)
│   ├── DesignAndMake.tsx
│   ├── Process.tsx            # Pinned scroll-driven steps
│   ├── Studio.tsx
│   ├── TrustStrip.tsx         # Hidden if no content supplied
│   ├── Contact.tsx
│   ├── Footer.tsx
│   └── ui/
│       ├── SplitLines.tsx     # Masked line-by-line headline reveal
│       ├── Button.tsx
│       └── ParallaxImage.tsx
└── assets/images/             # Client photography (replace placeholders)
```

---

## 8. Build Scripts

```bash
npm install
npm run dev          # local dev server
npx tsc --noEmit     # type check
npm run build        # production bundle
```

---

## 9. Acceptance Checklist

- [ ] No unconfirmed fact appears anywhere on the page.
- [ ] The page is static: no modals, forms, calculators or backend calls.
- [ ] Hero, Selected Work, Design + Make, Process and Studio all use the specified parallax and reveals.
- [ ] Reveals play once and never leave content invisible if scripts fail.
- [ ] Reduced-motion mode works and the content stays fully usable.
- [ ] Mobile (390px): no pinning, hover content always visible, drawer menu works.
- [ ] Contrast, focus states, alt text and the skip link pass an accessibility check.
- [ ] LCP < 2.5s, CLS < 0.1, smooth scrolling on a mid-range phone.
- [ ] WhatsApp link uses digits only, and the `mailto:` link works.

---

## 10. Needed From the Client Before Launch

1. What Fab Haus manufactures, and the capabilities they are comfortable stating publicly.
2. Real project photography (at least four strong projects) and ideally workshop photos. The full-bleed layout exposes weak images, so image quality decides the result.
3. Brand colors, logo and fonts, if any exist. Otherwise approve the proposed palette.
4. Real stats, a principal quote and name, and client logos or testimonials (or confirm none yet).
5. Email, phone, address and social links.
