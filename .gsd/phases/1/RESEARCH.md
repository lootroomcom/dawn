# RESEARCH.md — Phase 1: Foundation & Debt Removal

## Overview
Phase 1 focuses on cleaning up technical debt, specifically hardcoded styles (`!important`) and legacy Liquid templates.

## Findings

### 1. Hardcoded Styles (`!important`)
We identified several "override" styles in `assets/custom.css` that use `!important` and should be moved to the Theme Editor (Schema):

| Target | Property | Current Value | Note |
|--------|----------|---------------|------|
| `body` | `background-position` | `center top !important` | Hardcoded in `custom.css:5` |
| `body` | `background-size` | `100% auto !important` | Hardcoded in `custom.css:6` |
| `body` | `background-repeat` | `repeat-y !important` | Hardcoded in `custom.css:7` |
| `.header__inline-menu .list-menu__item--link` | `color` | `var(--brand-gold) !important` | Hardcoded in `custom.css:23` |
| `.header__inline-menu .list-menu__item--link` | `font-weight` | `700 !important` | Hardcoded in `custom.css:24` |
| `.header__inline-menu .header__menu-item` | `font-weight` | `700 !important` | Hardcoded in `custom.css:34` |
| `.shopify-section:has(.footer-wrapper)` | `margin`, `padding`, `overflow` | `0 !important`, `0 !important`, `visible !important` | Hardcoded in `custom.css:68-70` |

### 2. Legacy Templates
The only remaining Liquid 1.0 template is `templates/gift_card.liquid`. All other templates have already been converted to JSON.

### 3. Missing Schema Settings
The current `config/settings_schema.json` lacks settings for:
- Body background image controls (position, size, repeat).
- Explicit header menu item typography and color overrides (currently using `brand_gold` but hardcoded in CSS).

## Recommendations
1. **Add Theme Settings**: Add settings to `config/settings_schema.json` for global body background behavior and header menu styling.
2. **Convert Gift Card**: Move the logic from `templates/gift_card.liquid` into a new section `sections/main-gift-card.liquid` and create `templates/gift_card.json`.
3. **Refactor CSS**: Update `assets/custom.css` and `assets/base.css` to use the new settings via CSS variables, removing the `!important` tags where they were used for overrides.
