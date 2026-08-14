# MIGRATION — Adobe Fonts Web Project (Arabic)

**Status:** OPEN · **Owner:** Founder (needs an Adobe account sign-in) · **Opened:** 2026-08-14

## Decision

| | |
|---|---|
| Current fix in `assets/css/site.css` | **APPROVED AS TEMPORARY HOTFIX ONLY** |
| Final solution | **Adobe Fonts Web Project with Arabic support** |

## Why the hotfix exists

Measured, not assumed:

| Asset | Glyphs | Arabic codepoints | GSUB (shaping) |
|---|---|---|---|
| Kit `pjw7fmu` — `subset_id=2` | 215 | **0** | no |
| Kit `dij7rlz` — `subset_id=2` | 215 | **0** | no |
| Same font — `subset_id=1` | 960 | **253** | yes |

Both kits generated through the connector default to the Latin character set, so
every Arabic glyph fell back to the system sans-serif. The hotfix loads
`subset_id=1` scoped by `unicode-range` to Arabic only.

**Adobe requires the generated embed code as the delivery method — hand-built
`@font-face` rules are not the sanctioned architecture.** Hence: temporary.

## Blocker

The connector's font tool (`get_fontkit_embed_url`) has **no character-set
parameter**. It produced a second kit (`dij7rlz`) with the identical Latin-only
subset. Character set can only be changed in the Adobe Fonts web UI.

## Human step — one action

1. Sign in at **https://fonts.adobe.com** with the Farida Systems Adobe account.
2. **Web Projects → Create new project.**
3. Add family **Forma DJR Arabic Display**.
4. Tick weights **300 · 400 · 500 · 700**.
5. Set **Character Set → All Characters** (or an Arabic language subset that
   covers all letterforms and shaping).
6. Save / publish, then copy the generated **embed code** and send the kit id.

## Cutover checklist (run after the kit id arrives)

- [ ] Replace `pjw7fmu` with the new kit id in all six HTML pages
- [ ] Confirm the new kit's CSS does **not** serve `subset_id=2`
- [ ] Delete the five manual `@font-face` rules from `assets/css/site.css`
- [ ] Bump `site.css?v=` on all six pages
- [ ] Wait for the GitHub Pages deploy
- [ ] Fresh browser session — for weights 300/400/500/700:
      `document.fonts.check('16px "forma-djr-arabic-display"', 'برومبتات عملية ونتائج حقيقية') === true`
- [ ] Computed `font-family` on `body · h1 · pre · code · buttons` = `forma-djr-arabic-display, sans-serif`
- [ ] Visual QA at **390px** and **1440px**: letter joining · dots · diacritics ·
      digits and percentages · Arabic/English mixing · no clipping · no permanent fallback
- [ ] All six pages pass

**Do not cut over on a partial pass.** If the new kit fails the Arabic check,
keep the hotfix and reopen this document.

## Hard rule

Never upload OTF / TTF / WOFF font files to this repository.
