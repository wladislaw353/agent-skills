---
name: accessible-ui
description: Build and improve accessible websites and web interfaces. Apply when creating, changing, or reviewing frontend components, layouts, navigation, forms, dialogs, themes, typography, charts, or motion—even when accessibility is not explicitly requested. Also use for UI accessibility fixes and WCAG/APCA questions. Exclude backend-only work and prose translation without interface changes.
---

# Accessible UI

Make the ordinary interface usable without activating a special accessibility mode. Support browser and assistive-technology preferences; offer useful personalization without treating it as a repair for inaccessible content. The presence of a theme or font control is not evidence of poor accessibility.

## Work within the task

- Apply this guidance proactively to the interface being changed. Do not turn a small component edit into an unsolicited site-wide audit or redesign.
- Inspect the rendered interface, relevant source, design tokens, existing primitives, and build pipeline. Preserve the framework and visual language. Edit style sources and compile generated assets.
- Use the project's accessibility target. If none exists, use WCAG 2.2 AA as an engineering baseline, not a legal conclusion or a claim of conformance. Preserve stricter requirements.
- Reuse established components, but verify their behavior in context; a library's reputation is not proof of accessibility.
- Fix an in-scope barrier directly. If a necessary fix crosses scope or needs unavailable information, identify the specific dependency. Do not silently substitute a different feature or conceal the failure.
- This skill does not authorize installations, publication, external messages, account changes, or broader repository modifications. Do not add approval steps for ordinary authorized implementation.

## Implement the whole interaction

1. Identify the user task and affected states: initial, active, expanded, loading, error, recovery, and success as applicable. Include relevant keyboard, touch, pointer, voice, and screen-reader access; do not assume one input method identifies a disability.
2. Use native HTML semantics and behavior first. Links navigate to resources or document locations; buttons perform actions. Use real labels, headings, landmarks, and appropriate image alternatives. Set button types deliberately.
3. Expose meaningful names, roles, values, and states. Use ARIA only for missing semantics. ARIA does not implement keyboard behavior, validation, focus management, or modality. For controls with visible text labels, include that label text in the accessible name, preferably at the start (SC 2.5.3).
4. Preserve logical focus order and a visible, unobscured focus indicator. Restore or move focus deliberately after overlays, deletion, route changes, and validation. Do not add positive tabindex or make static content tabbable without a concrete navigation need.
5. Keep text, controls, feedback, and task completion usable with zoom, reflow, text-spacing overrides, system colors, and reduced motion. Check all affected themes and states, not just the default screenshot.
6. Verify the changed behavior in the rendered app. Use existing project tools and meaningful regressions. Match test depth to the change; report what actually ran and what remains unverified.

## Load only the relevant detail

- Forms, menus, dialogs, custom selection controls, async feedback, and navigation: read [Component behavior](references/components.md).
- Colors, typography, layout, focus styling, touch targets, graphics, or animation: read [Visual and perceptual access](references/visual.md).
- Browser verification, testing strategy, or a requested accessibility review: read [Verification](references/verification.md).

For a single link or wording correction, the core rules can be sufficient. Do not read every reference or run a full test matrix by default.

## Avoid these shortcuts

- Do not install an overlay or create a separate “accessible version” as a substitute for fixing the main experience. Do not remove useful existing preferences merely because they are optional.
- Do not hide a focusable control or its ancestor with aria-hidden. Opacity and pointer-events do not remove keyboard focus or accessibility semantics.
- Do not use CSS :focus-visible to switch between a “mouse component” and a “screen-reader component.” Input methods can be mixed; hidden duplicates need an explicitly designed and tested architecture.
- Do not remove outline globally or rely on box-shadow alone. :focus-visible is a browser heuristic, not a synonym for Tab.
- Do not claim that automated checks, an accessibility-tree snapshot, or simulated color vision reproduce screen-reader use or establish full conformance.
- Do not replace a failed WCAG contrast result with an APCA pass. APCA is supplementary; read the visual reference before applying its values.

## Finish proportionally

Implement accessibility with the feature, rather than leaving essential behavior as optional follow-up. Briefly report the changes and the relevant checks. Name concrete limitations, such as an untested browser/screen-reader pairing; do not append a generic compliance lecture or claim certification. Avoid creating extra audit documents unless requested.
