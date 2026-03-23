# REQUIREMENTS.md

## High-Level Requirements

| ID | Requirement | Source | Status |
|----|-------------|--------|--------|
| REQ-01 | Eliminate all `!important` tags from CSS files. | SPEC Goal 1 | Pending |
| REQ-02 | Map all section-specific colors to Shopify Schema color settings. | SPEC Goal 1 | Pending |
| REQ-03 | Implement dynamic spacing (margin/padding) controls for all sections. | SPEC Goal 1 | Pending |
| REQ-04 | Ensure all interactive elements have ARIA labels and keyboard focus states. | SPEC Goal 2 | Pending |
| REQ-05 | Optimize all images using Shopify's `image_tag` filter with lazy loading. | SPEC Goal 2 | Pending |
| REQ-06 | Convert any remaining Liquid 1.0 templates to JSON templates. | SPEC Goal 4 | Pending |
| REQ-07 | Documentation for each custom section explaining schema usage. | SPEC Goal 3 | Pending |

## Technical Implementation Rules
- Always use CSS variables for theme settings.
- Avoid inline styles where possible; use global theme CSS or section-specific CSS files.
- Proactively fix identify technical debt found during mapping.
- **REQ-08**: Premium Marquee Banner with logo support (clickable), advanced typography, visual effects, and global logo filtering.
