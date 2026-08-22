# og:image specification

*Written 2026-07-28. What to build, how to build it, and the exact markup to paste into `index.html`.*

The site currently declares `og:title`, `og:description`, and `og:type` but no image. Every link to fictionlaboratory.com therefore renders as a bare text card in iMessage, Slack, Discord, LinkedIn, Bluesky, and Facebook. For a project whose entire pitch is that the design work is already done, that is the wrong first impression.

---

## Hard specs

| Property | Value | Why |
|---|---|---|
| Dimensions | **1200 x 630 px** | The universal safe size, 1.91:1. Renders uncropped on every major platform. |
| Format | **PNG** or **JPG** | Nothing else. SVG is rejected outright by most scrapers, and WebP and AVIF fail on several. |
| Transparency | **None.** Flatten onto white | Transparent areas render black or white unpredictably depending on the client's theme. |
| File size | **Under 300 KB**, hard ceiling 1 MB | Some scrapers refuse anything larger. A flat two-color design will land well under 100 KB as PNG. |
| Color profile | **sRGB** | Anything else shifts. Convert on export, do not just tag it. |
| Bit depth | 8 bit | 16 bit doubles the size for no visible gain. |

## Safe areas

Platforms crop unpredictably even at the correct aspect ratio.

- **Full canvas:** 1200 x 630. Bleed art to the edges.
- **Keep all text inside a centered 1000 x 524 box.** Roughly 100px inset left and right, 53px top and bottom.
- **Keep the single most important element inside a centered 630 x 630 square.** Some clients, iMessage among them, crop to square in certain contexts. If the wordmark falls outside that square it will get cut.

## Type

This gets viewed at roughly 360px wide in a chat window. Treat it as a poster read from across a room, not a web page.

- **Minimum cap height 40px** at 1200 wide. Anything under about 32px is unreadable at thumbnail scale.
- **Eight words maximum.** "Your students will read the book." is seven and already proven as the headline.
- Do not reproduce the lede paragraph. It will not be legible and nobody reads it in a preview.
- Domine at 500 weight is the display face, matching the h1. It is self-hosted at `fonts/domine-latin-variable.woff2` if you need to match exactly.

## Palette

Straight from the site's custom properties, so the card and the page agree:

```
--paper   #ffffff      --cyan    #00b8d4
--ink     #0d0d0d      --green   #5fb022
--ink-soft#3a3a37      --yellow  #ffe000
--line    #d9d7cd      --orange  #ff6b2c
```

Note that green and orange as **text** on white both fail accessibility contrast (2.72:1 and 2.84:1). That standard does not apply to an og:image, but the same weakness makes them hard to read at thumbnail size. Use them as fills and rules, keep type in ink.

## Three directions worth considering

You are the designer, so treat these as starting constraints rather than art direction.

1. **The calibration strip.** The four-swatch CMYK bar plus the ink block already reads as the brand's signature. Headline in Domine above it, strip anchored across the bottom third. Closest to the existing page and the least work.
2. **The specimen label.** Mimic the packet's specimen record: a bordered card, mono label rows reading `SPECIMEN 01 / 200A · 500A`, title in Domine. Says "structured curriculum" more clearly than the headline alone does.
3. **The grid and crop marks.** The faint engineering grid plus the four corner crop marks, with the headline centered. Quietest option, and the crop marks reinforce the print heritage that the whole identity leans on.

## File placement

Save as `fictionlaboratory_web/og-image-v1.png`.

**Version the filename.** Platforms cache preview images aggressively and for a long time. Changing the image without changing its name often means the old one keeps appearing for weeks. Bump to `og-image-v2.png` on any revision and update the markup.

---

## Markup to paste

Replace the existing Open Graph block in `index.html` (currently three lines under the `<!-- Open Graph / social preview -->` comment) with this. Adjust the filename if you version it.

```html
<!-- Open Graph / social preview -->
<meta property="og:site_name" content="The Fiction Laboratory" />
<meta property="og:title" content="The Fiction Laboratory" />
<meta property="og:description" content="Your students will read the book. The science comes with it." />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://fictionlaboratory.com/" />
<meta property="og:image" content="https://fictionlaboratory.com/og-image-v1.png" />
<meta property="og:image:secure_url" content="https://fictionlaboratory.com/og-image-v1.png" />
<meta property="og:image:type" content="image/png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="The Fiction Laboratory. Your students will read the book. The science comes with it." />

<!-- Twitter / X -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="The Fiction Laboratory" />
<meta name="twitter:description" content="Your students will read the book. The science comes with it." />
<meta name="twitter:image" content="https://fictionlaboratory.com/og-image-v1.png" />
<meta name="twitter:image:alt" content="The Fiction Laboratory. Your students will read the book. The science comes with it." />

<link rel="canonical" href="https://fictionlaboratory.com/" />
```

**The single most common failure is a relative path.** `og:image` must be an absolute `https://` URL. A relative path works fine when you test the page locally and then silently produces no preview once it is live. The same applies to `og:url` and the canonical link.

The file must also be publicly reachable with no authentication and no redirect. Scrapers do not follow login flows and many will not follow redirects.

Note that `og:image:alt` is genuinely read aloud by some clients, so write it as a description of the image rather than repeating the title verbatim if the two differ.

---

## Verify before announcing anything

Each platform caches independently, so check them separately and force a refresh where offered:

- **Facebook Sharing Debugger**, at developers.facebook.com/tools/debug. The most informative of the lot, and it has a "Scrape Again" button. Use this one first even if you never post to Facebook, because it surfaces malformed tag errors the others hide.
- **LinkedIn Post Inspector**, at linkedin.com/post-inspector. LinkedIn caches roughly a week and this is the only way to clear it.
- **iMessage and Slack:** paste the link into a message to yourself. No debugger exists, so a version bump in the filename is the only reliable cache buster.

### Checklist

- [ ] 1200 x 630, PNG or JPG, sRGB, flattened, under 300 KB
- [ ] All text inside the centered 1000 x 524 box
- [ ] Key element inside the centered 630 x 630 square
- [ ] Legible at 360px wide, checked by actually shrinking it
- [ ] Saved as `og-image-v1.png` in `fictionlaboratory_web/`
- [ ] Markup uses absolute `https://` URLs throughout
- [ ] Deployed and publicly reachable, verified by loading the image URL directly
- [ ] Facebook debugger scraped clean with no warnings
- [ ] Pasted into iMessage and Slack and eyeballed

---

## While you have the head open

Two small gaps in the same area, both one line each:

```html
<link rel="icon" href="/favicon.ico" sizes="32x32" />
<link rel="icon" href="/icon.svg" type="image/svg+xml" />
<link rel="apple-touch-icon" href="/apple-touch-icon.png" />
```

There is currently no favicon, so the browser tab shows a blank page glyph. A 180 x 180 PNG for `apple-touch-icon` covers the case where someone saves the site to a phone home screen. Unlike og:image, an SVG favicon is well supported and scales properly.

The other gap is the missing privacy policy, which matters more than any of this: the page collects email addresses through the Kit form with no privacy notice anywhere. See `FictionLab_Compliance_Reference.md`.
