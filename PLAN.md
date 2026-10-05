# Winter Promo Snowboard Mini-Game: Project Plan

Status: **Draft v0.1, planning.** Open decisions are marked **[DECIDE]**.

---

## 1. Concept

A short, replayable browser game. The player snowboards down a slope through a winter forest,
dodges obstacles, collects coins and opens rare chests that give promo loot. At the end of the
run they see a result screen with their loot and a clear call to action, such as "Use your code in the catalog".

**Design goals**
- Anyone can play within 3 seconds: one-finger or arrow-key controls, no tutorial wall.
- One run lasts 60–90 seconds, so people replay it and it fits a promo attention span.
- Mobile first. Most promo traffic comes from phones via Instagram, WhatsApp and so on.
- The brand is visible but not annoying: it's part of the world (billboards, flags, finish gate),
  not pop-ups.
- It must be fair and resistant to cheating if the loot has real value.

---

## 2. Recommended technical approach

| Area | Recommendation | Why |
|---|---|---|
| Engine | **Phaser 3** (JS, 2D) | Mature, small, great on mobile, well documented. |
| View | **Top-down / ¾ view, scrolling downhill** (SkiFree-style) | Reads well in portrait on phones and is simple to control. Easy to place billboards and chests. |
| Build | Vite → static bundle (`index.html` + `assets/`) | Easy to drop into any hosting or Bitrix. |
| Size budget | Up to about 3 MB on first load | Fast on 4G. |
| Controls | Mobile: touch and hold the left or right half of the screen, or drag. Desktop: ← → / A D | Simple and one-handed. |
| Languages | Strings kept in one file. RU / KZ / EN **[DECIDE]** | Easy to add translations. |
| Analytics | Events to Yandex.Metrika and/or GA4 **[DECIDE]** | Measure promo ROI. |

### Bitrix integration (two options)

**Option A: Static game in an iframe (simplest, works everywhere)**
- Upload the build to e.g. `/local/games/snowboard/` or `/upload/snowboard/`.
- Put it on a Bitrix page with an `<iframe>` in the visual editor, or in an HTML block on Bitrix24.Sites.
- Pros: the Bitrix template's CSS and JS (jQuery etc.) can't break the game, and deployment is trivial.
- Cons: prizes can't be tied to the logged-in Bitrix user unless we add Option B's API.

**Option B: Custom Bitrix component with a backend (for real prizes)**
- `/local/components/<company>/snowboard.game/` with `class.php`, `template.php` and an AJAX controller.
- The server decides which chest loot is given. The client only animates it. This prevents cheating.
- Promo codes are generated or issued through the **`sale` module coupons** (Marketing → Discounts)
  or come from a pre-loaded pool in a Highload-block.
- Limits: e.g. N chests per user per day, and a total stock per prize.
- Optional: a leaderboard stored in a Highload-block.
- Requires the "boxed" 1C-Bitrix: Site Management with PHP access. **Not possible on cloud
  Bitrix24.Sites**, which only allow an HTML/iframe block, so an external backend would be needed there.

**Update:** our Bitrix developer confirmed that there's no ready-made integration, but a custom PHP
layer is possible. The spec for that layer, written in Russian for the developer, is in
[`docs/BITRIX_INTEGRATION_SPEC.md`](docs/BITRIX_INTEGRATION_SPEC.md).

➡ **Plan:** build the game as a standalone static bundle first (it works with A), and design it
so that the "open chest" step calls a pluggable `PrizeService`. For B, we then just add the
Bitrix component and API.

---

## 3. Gameplay spec (draft)

**Run structure**
- Start screen → 3-2-1 → run of about 75 s on a fixed-length slope → finish gate → results screen.
- Speed slowly increases. The slope is built from hand-designed segments shuffled with a seed.

**Player**
- Snowboarder in brand colors (jacket or board with the logo). Optional choice of 2–3 riders **[DECIDE]**.
- Carves left and right. Crashing into an obstacle causes a short tumble, a speed loss and one lost life
  (3 lives), or a time penalty **[DECIDE]**.

**Obstacles**: pine trees, rocks, snowmen, logs, frozen patches (slide), other skiers (moving).

**Helpers**: ramps (jump plus a small trick bonus), speed boost pads, magnet power-up (pulls in coins).

**Collectibles**
| Item | Frequency | Effect |
|---|---|---|
| Coin | Many | +1 score. Coins can also convert into a reward tier at the end **[DECIDE]** |
| Big coin / gift | Rare | +10 |
| **Chest** | 1–3 per run, placed on risky lines | Opened at the finish (or instantly). Gives loot |

**Loot ideas** (all **[DECIDE]** with the business)
- Discount promo codes (5% / 10% / 15%)
- Free delivery
- A gift with purchase
- Participation in a prize draw (grand prize)
- Cosmetic in-game items (board skins), which cost nothing and still feel rewarding

**Scoring**: distance + coins + tricks − crashes. A "Best score" is saved on the device.

**Results screen**: score, coins, the chest loot with its promo code (copy button), a
"Shop now" CTA button, "Play again" and "Share".

---

## 4. Branding placements

- Loading screen: logo and promo slogan.
- **Billboards along the slope**, rotating 3–5 banner images such as promo offers and products.
- Start and finish arches in brand colors with the logo.
- Flags or gates with the logo.
- Rider outfit or board with the logo.
- Chests in brand style.
- Results screen: offer, CTA and promo end date.
- Optional: brand mascot as a character or an NPC who waves at the finish.

---

## 5. What we need from you (brief checklist)

### Business
- [ ] Company name, what you sell, website URL
- [ ] Promo name, start and end dates, main goal (sales / sign-ups / engagement / traffic)
- [ ] Target audience: age, and the share of mobile versus desktop visitors if you know it
- [ ] Languages: RU / KZ / EN?

### Brand assets
- [ ] Logo, ideally **SVG** (or PNG ≥ 1000px on a transparent background), plus a light and a dark version
- [ ] Brand colors (HEX) and fonts (or the brandbook PDF)
- [ ] Banner images or texts for billboards: 3–5 slogans or offers
- [ ] Mascot or character art, if you have any
- [ ] Preferred art style: flat/vector, cartoon, pixel art, soft 3D look (share 2–3 reference games or images)

### Prizes and rules
- [ ] Full loot list, how many of each, and the chance of each
- [ ] How prizes are delivered: shown on screen / sent by email or SMS / applied to the Bitrix account
- [ ] Is login or registration required to get a prize?
- [ ] Limits: plays or chests per day per person
- [ ] Promo rules text and privacy notice (legal). Check the local rules for prize promotions and draws

### Bitrix and hosting
- [ ] Which product: **1C-Bitrix: Site Management (boxed)** and its edition, or **Bitrix24 (cloud) Sites**?
- [ ] Do you have FTP/SSH or admin access to upload files? What PHP version?
- [ ] Is the `sale` (e-shop) module in use, and do you already use coupons or discounts?
- [ ] Where the game should appear: a separate landing page, a pop-up, or a banner on the homepage?
- [ ] Analytics: Yandex.Metrika counter ID and/or GA4 ID

### Nice-to-haves
- [ ] Music and sound: do you have brand audio, or should we use royalty-free tracks?
- [ ] Leaderboard with a weekly prize for the top players?
- [ ] Social share image and text

---

## 6. Roadmap and ready-to-use prompts

Each phase ends with something you can play or check. Paste the prompt, adjusted, to start a phase.

### Phase 0: Brief (now)
Fill in section 5. Where something is unknown, write "decide for me".

### Phase 1: Playable prototype (placeholder art)
> Set up the project with Vite + Phaser 3. Build a playable prototype of the snowboard game
> according to PLAN.md: top-down downhill scrolling, portrait layout that scales to any screen,
> touch and keyboard controls, procedurally placed trees and rocks, collisions with a tumble,
> a 75-second run with a finish line, and a simple score. Use simple shapes as placeholder art. Add a README
> with how to run and build it.

### Phase 2: Coins, chests and loot
> Add coins, rare chests, ramps and a speed boost as described in PLAN.md. Chest opening should go
> through a `PrizeService` interface with a local mock implementation (weighted random from a JSON
> config). Build the results screen with the loot, a copy-code button, a CTA button and Play again.

### Phase 3: Art and branding pass
> Here are our brand assets: [logo, colors, fonts, banner texts]. Replace the placeholder art with
> a cohesive [flat vector] winter forest style. Add billboards along the slope using the banner
> images from `/assets/brand/banners/`, branded start and finish arches, and a branded loading screen. All
> brand content must be configurable from one `brand.config.json`.

### Phase 4: Bitrix integration
> We use [1C-Bitrix Site Management, edition X, PHP 8.x, sale module ON]. Create a Bitrix component
> `<company>:snowboard.game` in `/local/components/` that embeds the game and exposes an AJAX
> controller for `startRun` and `openChest`. The server decides the loot using a prize pool with stock limits
> and per-user daily limits, and issues a real coupon via the sale module. Include installation steps.

### Phase 5: Polish and performance
> Add music and sound effects with a mute toggle, particles (snow spray, coin sparkle), screen shake,
> and chest opening animation. Profile on a low-end Android and keep 60 fps. Add analytics events
> (game_start, game_finish, chest_open, cta_click) for [Metrika/GA4].

### Phase 6: QA and launch
> Produce a QA checklist (iOS Safari, Android Chrome, desktop Chrome/Firefox/Edge, in-app browsers
> like Instagram and WhatsApp). Write the deployment guide for our Bitrix site and a rollback plan.

---

## 7. Risks and notes
- **Cheating:** any prize decided in the browser can be faked. Real-value prizes need Phase 4.
- **In-app browsers** (Instagram, WhatsApp, TikTok) have quirks with audio, fullscreen and clipboard,
  so we test them explicitly.
- **iOS audio** only starts after the first tap. We handle this on the start screen.
- **Bitrix template conflicts:** the iframe approach avoids them. The component approach puts the game
  in a scoped container.
- **Legal:** prize promotions may need published rules. Get your legal or marketing team to confirm.
