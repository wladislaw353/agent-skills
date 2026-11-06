# Visual and perceptual access

Use the applicable criteria in [WCAG 2.2](https://www.w3.org/TR/WCAG22/) as the normative source. The notes below are implementation prompts, not an exhaustive conformance checklist. Inspect exceptions before asserting that a specific instance fails.

## Contrast and color

- Under SC 1.4.3 AA, normal text needs 4.5:1; large text needs 3:1. Large means at least 18 pt (24 CSS px), or 14 pt (about 18.67 CSS px) bold. Do not round a failing value up to pass. AAA SC 1.4.6 uses 7:1 and 4.5:1 respectively.
- SC 1.4.11 AA applies 3:1 against adjacent colors to visual information needed to identify controls/states and meaningful graphics. It is not a blanket demand for borders around every control. Inactive controls and other cases have specific exceptions; disabled text can still benefit from readability.
- Measure the actual rendered foreground/background combinations, including transparency, gradients, images, selection, errors, hover/focus, and light/dark themes. An opaque token pair alone cannot validate text over a variable background.
- Use text, shapes, patterns, or markers as well as color for errors, statuses, and data series. Simulation of color vision deficiency is diagnostic, not proof of anyone's lived experience.

### APCA without misleading claims

APCA supplements perceptual design decisions; it does not substitute for the contrast method required by the applicable WCAG version. In the published WCAG 3 Working Draft reviewed on 2026-11-05, the algorithm was still undecided. Recheck the [current WCAG 3 draft](https://www.w3.org/TR/wcag-3.0/) before making a present-day adoption claim. Do not describe APCA as an approved or inevitable WCAG replacement.

Use an identified version of the [reference APCA implementation](https://github.com/Myndex/apca-w3), not a guessed approximation or values copied from an image. Record color space, polarity, resulting colors after compositing, and implementation version when reporting calculations. For reproducible examples, pin the source to a release or commit; a moving branch URL and a hash alone do not preserve access to that revision. Check the license before incorporating an implementation into a distributed product.

Lc is not a percentage or a WCAG ratio. Its sign expresses polarity; negative is not failure. The core color calculation does not take font size or weight as inputs: typography and use case affect how the value is interpreted. Do not invent a universal |Lc| ≥ 60 pass rule. If a pair is questionable, adjust colors or typography while satisfying the required WCAG check. Do not label a sample “real perception: pass” without appropriate user evidence.

## Focus and system preferences

- Retain a visible focus indicator with contrast against its surroundings. Check clipping, overlays, sticky headers, and footer transitions. Hover is not a replacement for focus.
- AA SC 2.4.11 prohibits complete obstruction of the focused component by author-created content; keeping it fully visible is a useful stronger design target.
- Do not mislabel the quantitative area/contrast requirements of [SC 2.4.13 Focus Appearance](https://www.w3.org/WAI/WCAG22/Understanding/focus-appearance.html) as AA: they are AAA.
- Test forced colors. Box shadows and background-only indicators may disappear. Preserve an outline and use suitable system colors when necessary. Do not disable forced-color adjustment globally to preserve branding.
- Respect browser zoom and user text settings; do not disable pinch zoom. Optional font/theme preferences can help, but should not be prerequisites for completing a basic task.

## Layout and text

- Test text enlargement to 200% without losing content/function (SC 1.4.4). Separately test reflow at an equivalent width of 320 CSS px for vertically scrolling content (SC 1.4.10); 400% zoom from a 1280 CSS px viewport is a common check. A narrow viewport alone does not test text resizing.
- Avoid two-direction scrolling for ordinary reading. Keep necessary two-dimensional content such as complex tables within an appropriate region; exceptions do not justify whole-page overflow.
- Test user overrides together: line height 1.5× font size, paragraph spacing 2×, letter spacing 0.12×, word spacing 0.16× (SC 1.4.12). These are resilience tests, not required default typography.
- Use layouts that can grow: wrapping labels, flexible control heights, and content-driven spacing. Avoid clipping validation text or truncating essential instructions. Check long translations and RTL layouts when relevant.

## Motor access, motion, and media

- SC 2.5.8 AA uses a 24×24 CSS px target minimum with exceptions such as qualifying spacing. 44×44 belongs to the enhanced AAA criterion, not a universal AA minimum. Larger touch targets can still be a sensible product choice.
- For drag interactions, provide an equivalent single-pointer action without dragging unless an exception applies (SC 2.5.7). Keyboard operation alone is not that pointer alternative. Do not require hover, precise movement, or complex gestures as the only route.
- Respect reduced-motion preferences; remove nonessential movement without removing essential information or feedback. Separately assess pausing auto-updating/moving content, time limits, and flashing. One media query does not satisfy all related requirements.
- Provide captions, transcripts, and description of essential visual information as applicable to the media type and target level. A transcript does not universally replace synchronized captions or audio description. Check player controls with keyboard and assistive technology.
