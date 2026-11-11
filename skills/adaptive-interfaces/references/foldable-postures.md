# Foldable postures and lower-region controls

Use for half-open devices, segmented windows, and overlays affected by folding. The useful abstraction is the current arrangement of usable regions, not a separate feature set for every device name. Apply it to existing product tasks and controls; do not invent a camera, player, or control deck just to demonstrate folding.

## Determine geometry before choosing a policy

Inspect the actual window/container, reported posture, fold/hinge bounds, separation/occlusion, and current orientation. Convert all geometry into the owning layout's coordinates and units. Recompute on rotation, resize, folding, and inset changes. A crease may have zero thickness; the usable regions need not be equal halves.

| Current arrangement | Policy |
| --- | --- |
| Half-open tabletop, upper/lower regions | Viewing above, existing touch controls below; lower sheets open from the lower region's bottom |
| Half-open book posture, left/right regions | Use related panes only when the job benefits; place local overlays in the owning usable region |
| Fully open continuous screen | Use the whole available window; tablet-style layout when it fits, phone layout when narrow |
| Closed with phone-sized outer screen | Ordinary compact phone flow in the permitted app window |
| Closed Flip with small cover screen | One glance or brief supported action; dedicated microinterface, not the full app shrunk down |

A Flip in tabletop and a book-style Fold rotated into landscape tabletop reduce to the same upper/lower interaction problem. Reuse one geometry-driven policy instead of permanently mapping “Flip = horizontal” and “Fold = vertical.” Dual-screen devices with the same arrangement follow that policy too. This does not imply every folded posture is tabletop: preserve the distinct left/right case when that is what the window reports.

On dual-screen hardware, a separating hinge can remain even when the reported state is flat. Preserve the usable-region boundaries and avoid occlusion; flat state does not turn two displays into one continuous canvas. It also does not by itself prove a tabletop viewing angle.

Do not infer tabletop from landscape, a wide viewport, a model string, or a nominal hinge angle alone. With no posture/segment reporting, support the ordinary resizable window and report that physical posture remains unverified. Do not invent a midpoint hinge or claim device support from a mock. An intended Apple/dual-screen target still requires its own verified public geometry contract; Android APIs establish no Apple support.

## Explain the physical setup and redesign the task

Tabletop means placed on a table like a small laptop: the lower display region lies approximately horizontal and the upper region faces the person. A roughly right-angle bend describes the intended use, not an exact sensor threshold. A Flip can reach this arrangement directly; a book-style Fold needs rotation so the hinge runs horizontally in the current viewing coordinates. Treat this hands-free setup as the primary half-open design scenario. A device held partly open like a book is a separate, situational layout, not a substitute for tabletop support.

Fully opening restores one full working surface, except for a real separating hinge. A large book-style inner display can use tablet-like navigation and supporting panes when space permits. An unfolded Flip is generally a tall phone: do not force tablet columns onto it. For an intended “Duo” or future device, use the specified geometry without inventing hardware availability or a public API.

In tabletop the upper viewing area has much less usable height than the fully open screen. Recompose the existing task instead of clipping the tablet layout into two halves. Before implementation, state a compact disposition for the actual screen's elements: **keep above / move below / collapse behind a control / hide temporarily**. Base every choice on the task and observed dimensions, including keyboard-open and keyboard-closed states.

- Keep the main reading/viewing result above, with only essential context and status. Reduce tall headers and duplicated toolbars. Preserve enough context to identify the current object and understand errors.
- Move the existing primary controls, contextual navigation, and input below. Do not fill the lower region with unrelated shortcuts or a mandatory full navigation bar.
- Collapse secondary lists, sidebars, filters, metadata, and infrequent actions where they consume needed space. Keep a discoverable path to required actions; do not remove them from the task. Restore the appropriate layout on unfolding without clearing selection or drafts.
- Allocate scrolling deliberately: upper content and lower controls can scroll independently. Keep text size and touch targets usable rather than shrinking everything. Avoid duplicate interactive trees and focusable descendants of hidden panes.
- With the keyboard open, prioritize input, its error/status, and submit/cancel over secondary navigation. With it closed, reveal useful existing controls again; an uncluttered lower region is acceptable.

## Text entry and the keyboard

For an existing chat, keep the conversation in the upper region. Put the composer near the **top of the lower region**, safely below the hinge, with its send action and bounded multiline growth. Reserve space underneath for the system keyboard. The composer is persistent task input, not a bottom sheet: it must not inherit a rule that pins all lower-region controls to the physical bottom. Preserve the conversation's reading position and draft when focus, posture, or keyboard visibility changes; follow the app's existing policy for new messages.

The desired arrangement is conversation → hinge → composer → keyboard. It is a layout requirement to verify, not permission to claim the app can force any IME into that region. The OS/user controls keyboard placement and mode; third-party, floating, split, hardware keyboards, suggestion rows, and accessibility settings change the available space.

Measure the reported fold regions, current content viewport, safe areas, and actual keyboard occlusion in one coordinate system. Intersect occlusion with each region rather than subtracting a global keyboard height from both. If the system already resized the content window, do not deduct that space again. Keep the physical fold location distinct from the resized viewport midpoint; `50vh` changes when the keyboard appears and is not a hinge coordinate.

Verify that the composer, caret, errors, send/cancel, and enough conversation context remain visible while typing. Reduce secondary chrome first, bound and scroll multiline input, and make the lower controls scroll where necessary. Never silently move input across the hinge or hide required actions to pass a screenshot. If the actual keyboard leaves insufficient usable lower space, report the constraint and design an explicit continuation within the authorized task, such as preserving the draft while asking the person to unfold. Do not promise an unverified keyboard relocation, introduce a custom keyboard, or substitute a different interaction without authorization.

For native apps inspect actual IME/inset behavior; for web inspect the real mobile browser's viewport and focus scrolling. A desktop resize does not prove the keyboard stays below the hinge. With a hardware keyboard or dismissed IME, reclaim the space without leaving a permanent empty keyboard spacer.

## Closed cover screens

Distinguish a phone-sized outer display from a small Flip cover display using actual usable bounds, cutouts, input, and access. The first can support an ordinary phone layout; the second needs a microinterface with a single useful answer or brief action from the existing product. Think of its attention and interaction budget as closer to a watch than a whole phone, while verifying its own APIs and capabilities.

Choose minimal truthful content, readable controls, freshness/privacy states, and a clear continuation into the same task after opening. Omit full sidebars, multi-pane navigation, long forms, and dense dashboards. Full-app launch permission does not make the full app a useful cover design. A short input flow is appropriate only if the host, available keyboard, and usable area actually support it. Do not copy a generic list of device features into the product or add unsupported commands to fill the display. If no useful supported microtask exists, propose none.

## Keep bottom-origin interactions in the lower region

In tabletop, a bottom-origin sheet, drawer, action panel, or picker belongs entirely within the lower usable region. This rule applies to transient bottom-origin panels; persistent input follows the text-entry policy above. Anchor the panel to that region's bottom, constrain its expanded height to that region, and preserve its bottom-up entrance/exit and drag behavior. The hinge is the lower region's upper boundary, not an origin for a panel opening into the upper screen. Centering an overlay in the entire window can produce the same error.

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
