# HTML email signatures

Seven signatures and one campaign email, hand-coded. No builder, no framework, no generator output.

Open `index.html` in a browser to see all eight. The buttons switch light/dark
and desktop/mobile.

| File | What it is |
|---|---|
| `01-classic-divider.html` | Photo left, 1px vertical rule, details right |
| `02-stacked-logo.html` | Wordmark on top, labelled contact rows |
| `03-accent-card.html` | Tinted panel with a solid accent bar |
| `04-signature-banner.html` | Signature plus a promo strip with a CTA button |
| `05-pozhegu-corporate.html` | Full corporate: logo, department, socials, legal footer |
| `06-minimal-text.html` | Zero images, ~1.5 KB, cannot break |
| `07-header-bar.html` | Reversed white-on-brand header bar |
| `08-banka-dardania-signup.html` | 600px acquisition email: offer band, card shot, ticks, CTA |
| `index.html` | All eight, with light/dark and desktop/mobile toggles |

Images: `dard-rezniqi.png`, `logo.png` / `logo-dark.png`, `pb-logo.png` /
`pb-logo-dark.png`, `pb-mark.png` / `pb-mark-dark.png`, `icon-*.png`,
`bd-*.png` (wordmark, hero, card, ticks, step and social icons).

**Pozhegu Brothers and Banka Dardania are invented companies**, used as demo
brands in 05/07 and 08. No real institution, product, offer, phone number,
address or domain appears anywhere. The social glyphs are placeholders too -
swap in the real brand marks before shipping anything.

## Before you use them

The image URLs in this folder already point at
`https://dardrezniqi.github.io/email-signatures/`. If you host them anywhere
else, find and replace that string.

Email clients can't read local files and Gmail blocks `data:` URIs, so the
images have to sit on a normal HTTPS host. There is no way around that.

## Installing

**Outlook Classic (Windows)** - open the `.html` file in a browser, select the
signature, copy, then paste into File - Options - Mail - Signatures. Outlook
strips the `<style>` block on paste. That is expected: everything structural is
inline, so the signature still renders. What you lose is the dark-mode CSS and
the mobile stacking, neither of which Outlook Classic supports anyway.

**New Outlook / Outlook on the web** - Settings - Mail - Compose and reply.
Same copy-paste. This one does read some of the `<style>` block.

**Apple Mail / iOS** - paste into Preferences - Signatures, then untick
"Always match my default message font".

## Notes on why they're built this way

**Tables, not divs.** Outlook Classic renders through Microsoft Word, not a
browser engine. `flex`, `grid`, `float` and `position` do nothing there. Nested
tables with `cellpadding="0" cellspacing="0" border="0"` are the only layout
primitive that behaves the same in all four targets.

**Spacing lives in `padding` on `<td>`, never `margin`.** Word drops margins on
block elements inside table cells. Vertical gaps use spacer rows with `height`,
`font-size:0` and `line-height:0` - without the last two, Outlook adds a
phantom line of leading and the gap comes out ~18px taller than asked.

**`mso-line-height-rule:exactly`** on every text cell. Otherwise Outlook treats
`line-height` as a minimum and the block grows. This bites hardest on the 11px
legal footer in 05, where inherited leading double-spaces the whole paragraph.

**Thin lines are filled cells, not borders.** The 1px rule in 01, the accent
bars in 03 and 06 - all `<td>`s with a background colour. Word rounds thin
borders inconsistently between zoom levels; a filled element always draws at
the width you gave it.

**No `border-radius`.** Outlook Classic squares corners off. Rounding would
mean the signature looks different in the one client this is actually about, so
everything is square throughout instead.

**Buttons are written twice.** Outlook gets a VML `<v:roundrect>`, everything
else gets a padded `<a>`, and conditional comments make sure only one renders.
Word ignores padding on anchors, so without the VML half the button collapses
to plain blue text. See 04 and 08.

**Social icons are separate `<td>`s**, not one image with an image map. Outlook
drops image maps and you lose every link at once.

**Dark mode** runs off `prefers-color-scheme`, with `[data-ogsc]` selectors
alongside it for Outlook.com's older inversion. Three caveats worth saying out
loud:

- Outlook Classic doesn't support media queries at all and applies its own
  inversion. Nothing here can change that; the job is to degrade well.
- A transparent-PNG logo in dark ink stays dark on a dark background, because
  no client recolours image pixels. That's why 02 and 05 serve a second logo
  file through `<picture>` - Apple Mail and iOS honour it, Gmail and Outlook
  fall through to the `<img>`, which is the version that reads on white.
- Navy bold text and a navy button both disappear on a dark card. In 08 the emphasis lifts to white and the button flips to the brand gold.

**The reversed bar in 07** is the single riskiest thing you can put in a
signature: white text on a solid fill, where some clients invert the background
and leave the text alone. Defences used - the fill is declared twice (bgcolor
attribute and inline `background-color`), it's a mid-dark tone rather than a
light tint so inversion algorithms mostly leave it alone, and the dark-mode
rule keeps it in the same colour family instead of flipping it.

**Images always carry `alt`, `width`, `height` and `display:block`.** Outlook
blocks images by default, so every signature has to still make sense as text.
That's also why the CTA copy in 04 is live text rather than baked into the
banner image, and why the offer band in 08 is a filled `<td>` with live text
rather than a banner graphic - a baked-in offer image loses the entire pitch
the moment images are blocked, which is Outlook's default.

**Clickable everywhere:** `tel:` on phones, `mailto:` on emails, a Google Maps
link on addresses. The `a[x-apple-data-detectors]` block stops iOS re-styling
numbers it auto-detects into its own blue.

## The campaign email (08)

A signature and a campaign email are not the same job. 08 is the acquisition
format - offer band, product shot, benefit ticks, one CTA, a deadline - and it
follows a different set of rules:

- **600px, not 470.** That is the width Outlook's reading pane settled on and
  everyone still builds to. Wider gets clipped.
- **A preheader.** The hidden block under `<body>` is what the inbox shows next
  to the subject line. Without one, clients grab the first text they find,
  which is usually "View in browser". The zero-width joiners after it stop the
  client dragging body copy in behind it.
- **Nothing load-bearing inside an image.** Headline, offer, every benefit and
  the button label are live text. Turn images off - Outlook's default - and the
  email still makes its case. A baked-in offer graphic loses the whole pitch.
- **Centring comes from `align="center"` on a nested table**, because Outlook
  ignores `margin:auto`. The outer table is a 100% wrapper; the fixed 600px
  table sits inside it.
- **Every row is its own table**, so a marketer can reorder them without
  breaking the layout.
- **Benefit lists are two-column tables, not `<ul>`.** Outlook ignores padding
  on list items and indents them differently between versions, so the ticks
  drift out of alignment.
- **Dark mode needed two extra rules**, both found by testing rather than
  guessing: navy bold text disappears on a dark card, and a navy button barely
  separates from one. The emphasis lifts to white, the button flips to gold.

**Every link points at the portfolio index, never at a bank.** A polished bank
email whose links resolve to a real financial institution is a phishing
template whatever the intent behind it, and it only takes one forward for that
to stop being hypothetical. The card art says DEMO on its face for the same
reason.

## The preview page

`index.html` is responsive in its own right, not a desktop page with a
simulator button bolted on. Below 700px each example switches to its own mobile
rules for real; the Mobile toggle exists so the same thing can be checked from
a desktop. Signatures are fixed-width by design, so each stage scrolls on its
own axis rather than forcing a horizontal scrollbar across the whole page.

## Tested

Rendered and checked at desktop and 390px widths, light and dark, in a Chromium
engine, and inspected for the Outlook rules above. A Litmus or Email on Acid
pass on the real design is the next step if the client has an account.
