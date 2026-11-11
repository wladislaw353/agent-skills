---
name: adaptive-interfaces
description: Analyze, propose, implement, and verify responsive/adaptive website or app layouts across narrow phones, landscape, tablets, resizable windows, split-screen, Flip/Fold devices, and exposed secondary screens. Use for project reviews that let the user choose valuable adaptive scenarios, and proactively during UI layout work or resizing, keyboard, zoom, cutout, and orientation fixes. Exclude notification/widget integration, purely editorial changes, and unrelated backend work.
---

# Adaptive Interfaces

Make the assigned interface useful throughout changes in available space, input, posture, and text size. Preserve the product's framework, design system, task state, and accessibility. Adapt the information hierarchy and interaction rather than scaling a screenshot.

## Project review and scenario selection

When asked to analyze a product broadly or propose what is possible, inspect its actual flows and capabilities first. Present a grounded shortlist of adaptive experiences before implementing optional features: for example list-detail on an expanded order screen, a reachable keyboard-open checkout, an editor's supporting pane, or supported viewing/controls in a half-open posture.

Trace proposed content and controls to existing data and working handlers. Adapt actual tasks; do not invent features to occupy a second pane or justify a device mode. State the reachability, continuity, or effort benefit. No additional posture-specific UI is a valid outcome when the ordinary layout already serves the task.

Use stable IDs and a compact table: **scenario/user benefit · existing project evidence · target window/posture/input · technical readiness/dependencies · priority and qualitative effort**. Sweep the relevant size, height, keyboard, text/zoom, split-screen, folding, exposed-display, and input contexts; do not manufacture a feature for every format. Explain which contexts are irrelevant or unverified. Keep a real defect in an assigned task separate from an optional product enhancement.

When useful candidates exist, let the person select IDs to implement, defer, or exclude and modify the proposal. If none pass, report that briefly without an empty selection step. Ask through an available question tool when useful. Wait for that selection before optional implementation; a recommendation or silence is not a choice. Reuse prior choices and do not keep offering excluded scenarios. If a concrete implementation or bug fix is already requested, execute it directly rather than turning it into a new selection gate. This mode does not authorize new SDKs, native targets, external access, or publication beyond the described and authorized scope.

## Establish the actual environment

Inspect rendered UI, layout/style sources, supported platforms, SDK/dependencies, existing navigation, state ownership, and project instructions. Identify whether the deliverable is a website/PWA, native app, cross-platform app, or embedded web view. Use the current window/container bounds rather than device brand, physical resolution, user-agent strings, or a frozen list of phone models.

Treat these as independent inputs:

- Available width **and height**, density, safe areas, system bars, and keyboard occlusion.
- Font scaling, browser zoom, localization, reading direction, and user preferences.
- Touch, mouse, stylus, keyboard, and assistive technology; a large window can still be touch-driven.
- Fold posture, separating/occluding hinge, and any verified viewport segments.
- Window/display transitions, app lifecycle, and the current selected object/draft/session.

Do not infer permission to draw on a cover/rear screen from responsive layout support. Do not introduce native targets, dependencies, orientation restrictions, external services, or publication merely because this skill applies. Continue useful authorized work when hardware or SDK access is missing, and identify the remaining prerequisite.

## Choose a layout policy, then implement it

For the assigned flow, decide which information and actions must remain available, which can move into another pane or disclosure, and what must survive transition. Use a concise policy appropriate to the task:

| Constraint | Useful adaptation |
| --- | --- |
| Narrow window / enlarged text | One primary pane, readable flow, wrapping actions, reachable navigation |
| Wide window with enough usable height | List-detail or supporting pane; bounded reading/form widths |
| Wide but very short landscape / keyboard open | Reduce persistent chrome; scroll content and keep focused input/actions reachable |
| Separating vertical fold in current window coordinates | Related left/right panes only when useful; preserve selection when collapsing |
| Supported tabletop posture with upper/lower regions | Viewing above, controls/input below; only bottom-origin sheets enter from the lower bottom |
| Phone-sized outer display | Ordinary compact phone flow when permitted |
| Small Flip cover display | Dedicated glance/brief-action microinterface; no shrunken full app |
| Resizable desktop/split-screen | Recompute from window bounds continuously; support keyboard/pointer as well as touch |

Use content failure points to choose web breakpoints and the platform's actual adaptive primitives for native layouts. Do not force multiple panes solely because a device is called Fold. A posture can change without a useful width-class change, and a narrow desktop window can need the same layout as a phone.

For folding work, read [Foldable postures and lower-region controls](references/foldable-postures.md). Flip tabletop and a book-style Fold rotated into landscape tabletop share the same upper/lower policy. Classify the current geometry and posture, not the device's hinge direction in its default orientation. Explain the physical laptop-like setup and decide what stays above, moves below, collapses, or hides. In chat, keep conversation above and the composer near the top of the lower region, with verified keyboard space below. Persistent input differs from bottom sheets, which enter from the lower bottom. Fully open layouts use the whole screen according to its actual dimensions.

Read the relevant implementation guide, not the whole library:

- [Web and PWA](references/web.md): CSS layout, container queries, viewport units, safe areas, browser keyboard behavior, and optional fold segmentation.
- [Android](references/android.md): window classes, Compose/View layouts, folds, insets, multi-window, and continuity.
- [Apple](references/apple.md): SwiftUI/UIKit layout, safe areas, Dynamic Type, keyboard, resizing, and scene state.
- [Cross-platform boundaries](references/cross-platform.md): React Native, Flutter, web views, display access, and version-specific bridges.
- [Verification](references/verification.md): task-based transitions and concrete evidence across form factors.

Implement in the existing component/target and styling pipeline. Prefer a single semantic content model and shared domain state, with platform-appropriate presentations. Avoid rendering duplicate interactive trees just to switch CSS visibility. Keep actual reading/focus order aligned with visual order. Give tables, code, and other inherently wide content an intentional local scrolling treatment instead of hiding document overflow.

## Preserve the task while space changes

Keep navigation identity, selected item, draft, validation errors, scroll anchor, media position, pending operation, and focus as relevant. Scope them above transient layout branches; persist what must survive process recreation. Resizing or unfolding must not submit, clear, refetch a consequential operation, or start a second session. Reconcile stale geometry without resetting domain state.

Do not disable zoom, cap user text size, lock orientation, or block resizing to make a layout pass. Honor platform requirements and documented exceptions for an actual specialized use case. Important actions need reachable touch targets, visible keyboard focus, accessible names, and safe placement clear of occlusion. System insets must have a defined owner so that nested containers do not apply them twice.

## Verify the implemented behavior

Start with the reported failure and representative task; sweep sizes and transitions using [the verification guide](references/verification.md). Test both sides of layout thresholds, short height, large text/zoom, keyboard open, long/RTL content, and navigation continuity. Use the project's existing browser/native tooling. A desktop viewport screenshot cannot prove a physical keyboard, mobile browser, hinge, cover-screen permission, or device sensor behavior.

Report what changed, why it improves the assigned task, environments actually checked, and remaining device/API limitations. Do not claim universal phone compatibility from a finite matrix. Keep automatic initiative proportional: fix useful in-scope adaptation, without turning a minor edit into a site-wide redesign or mandatory audit document.
