# Foldable postures and lower-region controls

Use for half-open devices, segmented windows, and overlays affected by folding. The useful abstraction is the current arrangement of usable regions, not a separate feature set for every device name. Apply it to existing product tasks and controls; do not invent a camera, player, or control deck just to demonstrate folding.

## Determine geometry before choosing a policy

Inspect the actual window/container, reported posture, fold/hinge bounds, separation/occlusion, and current orientation. Convert all geometry into the owning layout's coordinates and units. Recompute on rotation, resize, folding, and inset changes. A crease may have zero thickness; the usable regions need not be equal halves.

| Current arrangement | Policy |
| --- | --- |
| Half-open tabletop, upper/lower regions | Viewing above, existing touch controls below; lower sheets open from the lower region's bottom |
| Half-open book posture, left/right regions | Use related panes only when the job benefits; place local overlays in the owning usable region |
| Flat continuous expanded window | Ordinary adaptive layout; no mandatory tabletop control deck |
| Compact/closed outer window | Adapt to the actual permitted window; cover widgets require separate access |

A Flip in tabletop and a book-style Fold rotated into landscape tabletop reduce to the same upper/lower interaction problem. Reuse one geometry-driven policy instead of permanently mapping “Flip = horizontal” and “Fold = vertical.” Dual-screen devices with the same arrangement follow that policy too. This does not imply every folded posture is tabletop: preserve the distinct left/right case when that is what the window reports.

On dual-screen hardware, a separating hinge can remain even when the reported state is flat. Preserve the usable-region boundaries and avoid occlusion; flat state does not turn two displays into one continuous canvas. It also does not by itself prove a tabletop viewing angle.

Do not infer tabletop from landscape, a wide viewport, a model string, or a nominal hinge angle alone. With no posture/segment reporting, support the ordinary resizable window and report that physical posture remains unverified. Do not invent a midpoint hinge or claim device support from a mock. An intended Apple/dual-screen target still requires its own verified public geometry contract; Android APIs establish no Apple support.

## Keep bottom-origin interactions in the lower region

In tabletop, a bottom sheet, drawer, action panel, picker, or bottom-origin composer belongs entirely within the lower usable region. Anchor it to that region's bottom, constrain its expanded height to that region, and preserve its bottom-up entrance/exit and drag behavior. The hinge is the lower region's upper boundary, not an origin for a panel opening into the upper screen. Centering an overlay in the entire window can produce the same error.

Inspect the existing sheet/dialog component and overlay host before editing placement. Portals and native dialogs can escape the pane's layout and keep full-window sizing. Use the framework's supported container/window positioning or a region-aware host while preserving modal semantics. Changing only an inner panel's margin is insufficient if its host still positions, measures, or animates against the full window.

- Derive the panel's bounds, maximum height, gesture coordinates, and animation endpoint from the lower region after relevant insets. The entire visible animation path must stay within that region; test intermediate frames as well as the final position.
- Give long content its own scroll area. Keep drag handle, close/cancel, focused input, error, and primary action reachable with the keyboard and large text. Do not move the panel above the hinge to make it fit. If the task cannot fit even with scrolling, identify the constraint and choose an explicit usable flow within the assigned scope.
- Define scrim coverage and modality deliberately: a scrim may cover the full window when the whole task is modal, while the panel stays below. Prevent interaction behind a modal; retain accessible naming, focus containment, Back/Escape, and focus restoration. Do not clip away touch targets or focus rings at the hinge.
- Reuse existing content and handlers. Keep selected object, input, pending operation, open/closed state, and logical focus through posture changes. Do not mount two independent interactive copies for upper/lower layouts.

## Platform entry points

- **Android:** obtain `FoldingFeature` geometry/state through the project's WindowManager/Compose integration. Reconcile pixel bounds with dp and the content origin. Inspect whether a sheet uses a separate dialog window before assuming parent constraints apply. See [Android implementation](android.md) and [Android fold-aware guidance](https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/make-your-app-fold-aware).
- **Web:** use exposed viewport-segment geometry only where supported. Establish a lower-region overlay container and ensure the portal/positioning/animation uses its bounds; global `position: fixed` can still target the full viewport. See [web implementation](web.md). Viewport segments describe geometry; do not treat their presence alone as proof of a half-open angle.
- **Shared frameworks:** reconcile native fold bounds with the actual component container and overlay host. Use the installed framework's supported sub-screen/dialog APIs where available. See [cross-platform boundaries](cross-platform.md).

## Acceptance checks

Exercise an existing bottom sheet in Flip tabletop and in Fold landscape tabletop, plus the flat and left/right cases relevant to the app. Use reported or explicitly simulated unequal regions and a fold away from the viewport midpoint; fixed `50vh` placement must not pass accidentally. Open, expand, scroll, drag, dismiss, reopen, and rotate/fold while it is open. Check animation frames, panel bounds, hit testing, keyboard, large text, focus, Back, and preserved input. See [verification](verification.md); simulated geometry validates the layout policy, not physical device reporting.
