---
name: adaptive-interfaces
description: Design, implement, and verify responsive and adaptive website or app layouts across narrow phones, landscape, tablets, resizable desktop windows, split-screen, Flip/Fold devices, and exposed secondary screens. Apply proactively to UI layout work and when resizing, folding, keyboard, zoom, cutout, or orientation problems affect a task. Exclude notification/widget integration, purely editorial changes, and unrelated backend work.
---

# Adaptive Interfaces

Make the assigned interface useful throughout changes in available space, input, posture, and text size. Preserve the product's framework, design system, task state, and accessibility. Adapt the information hierarchy and interaction rather than scaling a screenshot.

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
| Separating vertical fold | Place related panes in usable regions; preserve selection when collapsing |
| Supported half-open horizontal fold | Deliberately separate viewing and controls when the task benefits |
| Tiny exposed cover screen | Task-specific compact content when the OS permits it |
| Resizable desktop/split-screen | Recompute from window bounds continuously; support keyboard/pointer as well as touch |

Use content failure points to choose web breakpoints and the platform's actual adaptive primitives for native layouts. Do not force multiple panes solely because a device is called Fold. A posture can change without a useful width-class change, and a narrow desktop window can need the same layout as a phone.

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
