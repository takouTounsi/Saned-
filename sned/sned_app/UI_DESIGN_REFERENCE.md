# CIN Scanner UI Design Reference

This document describes the visual language and interaction patterns already implemented in the CIN Scanner. Use it as the baseline when creating another module in the same product family.

## Product character

The interface is a calm, trustworthy document-processing tool. It combines:

- Government/service-portal seriousness through navy structure, clear labels, and restrained borders.
- Operational feedback through green progress, amber attention states, and red error states.
- A friendly scanning experience through rounded controls, simple illustrations, animated scan lines, and short instructional copy.

The UI should feel practical, legible, and reassuring rather than decorative. Every animation or color change communicates progress, confidence, attention, or completion.

## Core design tokens

### Color palette

| Token | Hex | Use |
|---|---|---|
| `navy` | `#14415A` | Primary brand color, headers, borders, selected states, dark overlays |
| `green` | `#43AA8B` | Success, progress, focus, confirmed data, active scan indicators |
| `amber` | `#FAB414` | Primary action buttons, warnings, low-confidence data, camera capture ring |
| `bg` | `#F7F7F7` | Main light-mode page background |
| `card` | `#FFFFFF` | Panels, cards, mobile surfaces |
| `text` | `#383C44` | Main body text |
| `ink` | `#14415A` | Headings, labels, emphasis |
| `mute` | `#6B7A86` | Supporting text, helper copy, metadata |
| `line` | `#DFE4E8` | Dividers, empty states, progress-track backgrounds |
| `bad` | `#D64545` | Errors, rejected documents, destructive feedback |
| `field` | `#8CA0AC` | Input underline in its resting state |

Use the same semantic roles in a new module. Do not introduce a second blue, green, or warning yellow unless the module has a genuinely different meaning.

### Dark mode

The current dark-mode values are:

| Token | Hex |
|---|---|
| `bg` | `#0E2C3D` |
| `card` | `#14415A` |
| `text` | `#EEF3F6` |
| `ink` | `#FFFFFF` |
| `mute` | `#A9BCC7` |
| `line` | `#2C5A73` |
| `field` | `#6F93A8` |

Dark mode is handled with `prefers-color-scheme: dark`. Preserve contrast and keep green/amber/red as semantic accents.

### Typography

- Primary font: `Montserrat`.
- Arabic/fallback font: `Cairo`.
- Generic fallback: `system-ui, sans-serif`.
- Headings are bold and navy; body copy is compact and functional.
- Labels are small, approximately `12px`, semibold.
- Supporting text is approximately `13px` and muted.
- Desktop page title: approximately `26px` and bold.
- Mobile/app-bar title: approximately `16px` and bold.
- Buttons: approximately `14–15px`, semibold.

Load Montserrat and Cairo together when the environment allows it. Keep Arabic-capable fields `dir="auto"` so mixed Latin/Arabic text remains readable.

## Layout system

### Desktop dashboard

The dashboard is a full-height application shell:

1. A fixed-height navy top bar, about `54px` high.
2. A two-column workspace beneath it.
3. A narrow left dossier/tray panel, about `340px` wide.
4. A flexible main panel for pairing, document preview, OCR overlays, and field review.
5. About `16px` gaps and `16–20px` outer padding.

Cards use a white surface, a thin navy border, an `8px` radius, and compact internal padding. Panels are allowed to scroll internally so the application shell stays stable.

At widths below approximately `900px`, the columns collapse into one scrollable column. At widths below approximately `700px`, two-column content grids become one column.

### Mobile app

The mobile UI is a full-screen view stack. Only one view is visible at a time:

- Home/file view.
- Camera view.
- Document review view.

Each view uses:

- A navy top bar with safe-area padding.
- A scrollable body with approximately `14–16px` horizontal padding.
- A bottom action area with a card-colored background and a top divider.

Buttons in the bottom action area expand equally and have a minimum height of about `48px` for touch use.

### Camera view

The camera is intentionally immersive:

- Black background.
- Full-screen video with `object-fit: cover`.
- A centered document frame with a darkened outside area.
- White border when neutral, green when the image is sharp/good, amber when the user should adjust.
- Small example preview near the top-left.
- Camera controls near the bottom: gallery/camera-app fallback, large capture button, and spacing for balanced layout.

For card-shaped documents, use an approximately `1.586` aspect ratio. For A4-like documents, switch to a portrait frame with approximately `.707` aspect ratio.

## Component language

### Top bars and headers

- Navy background with white text.
- Product name on the left, supporting phrase or connection status beside it.
- Status appears in a pill with a translucent white background; connected/success uses green.
- Secondary actions use an outlined/ghost style.
- Keep header copy short and action-oriented.

### Cards and panels

- White or dark-mode card surface.
- `8px` radius on desktop; mobile list rows use up to `12px`.
- Thin navy outline for primary panels.
- Light gray dividers for internal separation.
- Avoid heavy shadows; use borders and spacing as the main structure.

### Buttons

Primary action:

- Amber fill `#FAB414`.
- Dark text `#1B1B1B`.
- Pill shape (`border-radius: 999px`).
- Semibold label and small shadow.

Success/confirm action:

- Green fill `#43AA8B`.
- White text.

Secondary/ghost action:

- Transparent background.
- Navy text and approximately `1.5px` navy border.
- No shadow.

Disabled actions use reduced opacity and no pointer affordance. Icons sit before text with a small gap; the existing UI uses compact stroke-based inline SVG icons rather than image icons.

### Status chips and progress

Status chips are small rounded pills, approximately `11–12px`, semibold:

- Neutral: line gray.
- Running: translucent amber with a small spinning navy indicator.
- Warning/review: amber.
- In review/okay: translucent green.
- Validated/done: solid green with white text.
- Rejected: translucent red.

Progress bars are short rounded tracks using `line` as the background and green as the fill. Progress should animate smoothly when its value changes.

### Document list rows

Document slots are compact horizontal rows with:

- Thumbnail or numbered placeholder on the left.
- Flexible document title in navy.
- Status chip on the right.
- Light border and rounded corners.
- Dashed border and reduced opacity for an unfilled slot.

Selected rows use a navy border and a subtle navy focus ring. On mobile, rows are slightly roomier and use a `12px` radius.

### Dossier/stack visual

The file overview uses a stack of document thumbnails layered with small offsets and rotations. The empty state uses an illustration. A dark navy count pill anchors the stack. A completion banner uses a pale green background and the completion illustration.

This pattern is useful for any module that collects multiple items: show the collection visually, show the count, and keep the next missing item obvious.

### Inputs and field review

Inputs are intentionally minimal:

- Transparent background.
- No enclosing box.
- Bottom border only.
- Navy/green focus treatment.
- Labels above fields, small and semibold.

Low-confidence OCR values use an amber bottom border and a translucent amber background (`#FAB41418`). Helper text explains that amber fields should be checked or edited. Field forms use two columns on desktop and one column on mobile.

### OCR/document overlays

The document image is the primary visual object. Overlay behavior:

- Faint navy boxes show detected text regions.
- Green boxes show extracted fields.
- Green labels sit above extracted boxes.
- A green scan line sweeps across the image during analysis.
- OCR fields populate with a short green flash animation.

Use overlays as progressive feedback, not as permanent visual noise. They should appear after the image is visible and remain legible against both light and dark document photos.

### QR pairing panel

The pairing view uses a centered `230px` QR box with:

- White background.
- `10px` radius.
- Green corner brackets.
- An animated green scan line.
- A short numbered guide beside it.

Guide steps use navy numbered circles and small supporting illustrations. The active step receives a brief pale-green highlight animation.

### Completion and modal overlays

Completion feedback uses a full-screen or panel overlay in translucent navy (`#14415AF2` range), centered content, and a green outlined check animation. The final message is short and reassuring.

Errors appear as red toast/banner blocks with rounded corners and enough padding for multi-line reasons. Rejection flows always offer a recovery action such as rescan, remove, or retry.

## Interaction and state model

The existing UI is state-driven. Reuse this general vocabulary where appropriate:

`checking` → `preview` → `queued`/`analysing` → `review` → `validated`

Failure exits to `rejected`, with a clear reason and recovery path.

Recommended behavior for another module:

1. Start with an empty, instructional state.
2. Show immediate feedback after capture/upload.
3. Keep processing states visible with a spinner or amber indicator.
4. Let the user review machine-generated data before final confirmation.
5. Highlight uncertain values instead of silently accepting them.
6. Make completion unmistakable with green confirmation and a concise summary.

The current product also supports an optional auto-analyse mode. If a new module includes automation, expose it as an explicit checkbox or setting and preserve the manual review path.

## Motion language

Animations are short and purposeful:

- `pop`: new cards/documents enter with a small scale-up.
- `spin`: running status indicator rotates.
- `sweep/down`: green scan line moves across a QR/document/camera frame.
- `flash`: newly populated field briefly highlights green.
- `drop`: new document drops into the dossier stack.
- `shake`: invalid/rejected feedback draws attention to the error.
- `fade`: overlays and completion panels appear without abruptness.

Keep motion under roughly one second for feedback and around two seconds for looping scan indicators. Respect `prefers-reduced-motion` in a new implementation by disabling looping and reducing transitions.

## Responsive and accessibility requirements

- Preserve at least `44–48px` touch targets on mobile.
- Keep text and controls readable over camera footage with dark translucent surfaces or text shadows.
- Provide text alternatives for illustrations and QR images.
- Use visible focus states, especially on inputs and ghost buttons.
- Do not rely on color alone: pair status colors with labels such as “Running”, “Validated”, or “Rejected”.
- Preserve keyboard navigation on desktop.
- Use `dir="auto"` for mixed-language extracted values.
- Respect device safe-area insets on mobile headers and bottom controls.
- Add reduced-motion handling for scan lines, spinners, and completion animations.

## Asset direction

Existing illustrations are transparent PNGs with a friendly instructional style. They are used for:

- QR pairing steps.
- Camera tips.
- Empty document stack.
- Completed dossier.

New module illustrations should be simple, high-contrast, and supportive rather than ornamental. Use the existing asset placement patterns: small illustrations beside instructions, thumbnails inside rows, and larger completion art only in empty/completion states.

## Implementation checklist for a new module

- Reuse the token names and palette above.
- Use Montserrat/Cairo typography and the same semantic text hierarchy.
- Decide whether the module is desktop-first, mobile-first, or both; preserve the same shell behavior.
- Define empty, active, processing, review, success, and error states before building the happy path.
- Use amber for the next action/attention and green for confirmed progress.
- Use the same pill buttons, ghost buttons, cards, status chips, and underline inputs.
- Keep primary content in a bordered card or full-screen task surface.
- Add a recovery path to every error state.
- Add responsive breakpoints near `900px` and `700px` where appropriate.
- Test light mode, dark mode, Arabic/mixed text, keyboard focus, narrow screens, and reduced motion.

## Existing reference files

- Desktop dashboard: `app/static/dashboard.html`
- Responsive/mobile flow: `app/static/mobile.html`
- Legacy/full-screen scanner flow: `app/static/scan.html`
- Illustration notes: `app/static/img/README.txt`

