# Responsive web, PWA, and browser windows

Read for websites, PWAs, and web content inside an app. Checked 2026-11-06; verify current browser support before adding optional APIs. CSS pixels are not physical screen pixels. The browser can occupy only part of a device display, and hardware posture is not always exposed.

## Build the baseline from content

1. Inspect the existing viewport meta tag, component constraints, stylesheet source, and scroll containers. Use `width=device-width, initial-scale=1` where appropriate; preserve pinch zoom. Do not add `maximum-scale=1` or `user-scalable=no`.
2. Fix intrinsic sizing before adding device breakpoints: `min-inline-size: 0` on shrinking grid/flex children, `minmax(0, 1fr)` tracks, wrapping action groups, responsive media, and bounded paragraph/form widths. Decide how exceptionally long strings wrap. `overflow-x: hidden` on the document conceals a failure rather than making controls reachable.
3. Prefer Grid/Flexbox and fluid constraints. Use media queries for page structure and container queries for components that can live in different-width panes. Apply `container-type: inline-size` to a suitable ancestor; query the ancestor, not the queried element itself. Containment can affect intrinsic sizing, so inspect the result.
4. Choose thresholds where the actual content stops fitting, including translated labels and enlarged text. Preserve project tokens and existing layout primitives. Do not add a breakpoint for every phone model.

Illustrative CSS; replace thresholds and spacing with the project's decisions:

```css
.task-layout {
  display: grid;
  grid-template-columns: minmax(0, 1fr);
  gap: 1rem;
}
.task-layout > * { min-inline-size: 0; }
.task-panel { container-type: inline-size; }
.task-actions { display: flex; flex-wrap: wrap; gap: .75rem; }

@media (min-width: 60rem) {
  .task-layout {
    grid-template-columns: minmax(14rem, 20rem) minmax(0, 1fr);
  }
}
@container (max-width: 24rem) {
  .task-actions { flex-direction: column; }
}
```

This describes geometry, not list-detail navigation. Preserve the selected object and Back behavior when only one pane is shown. On large displays, cap reading width without arbitrarily wasting workspace for tools that benefit from more room. See [container queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Containment/Container_queries).

## Height, browser chrome, and safe areas

Use document flow for long content. For an app shell, decide which region scrolls and how fixed/sticky controls interact with it. Use `min-block-size` when content can grow; a fixed full-screen height can crop errors or translated actions. `svh`, `lvh`, and `dvh` have different meanings: a stable small viewport can suit a landing hero; a dynamic viewport can suit a currently visible shell but changes during browser UI movement. `100dvh` alone does not guarantee virtual-keyboard avoidance in every browser.

Apply safe-area padding to the owning edge container, for example `max(var(--space), env(safe-area-inset-bottom, 0px))`. The zero is the documented no-inset case, not guessed geometry.

`env(safe-area-inset-*)` reports an edge band, not the occluded shape, and CSS exposes no cutout rectangle. Decide per element whether it meets the occluded region:

- Fixed or sticky chrome on that edge (top bar, side controls, floating buttons) takes the inset where it would otherwise sit under the cutout or system indicators.
- Content that scrolls along the edge the occlusion sits beside needs the inset only if the occlusion spans the scroll's whole run. A notch or island in the middle of a side in landscape does: every row passes it. A camera hole or status column confined to the top corner does not: content passes it the way it passes under a top status bar, so page padding, cards, and media keep their full width.
- Backgrounds and media bleed edge to edge.
- The top edge follows the same rule. A tall top inset produced by a camera and status items in one corner must not push the whole bar and page down: the bar starts where the host places a bar of its height (on a display whose camera sits in a corner that is a little below the edge, so the row lines up with the camera, never the status column's full height) and only its controls beside the corner step sideways. Visible round buttons at the bar's ends keep the page's gutter from the screen edge. Where a bar holds several rows (logo and icons, then search), only the row that meets the corner moves.

- Overlays follow the same zone. A side sheet's header steps aside like a bar, and only the body rows resting beside the corner narrow; a long list or card of rows is not one row, so descend to its rows and let its background pass under the corner. Dropdowns align to their trigger, pointer-anchored menus move aside, and bottom sheets cap their height so their top stays below the corner.
- The bottom edge is no exception. A bottom inset that exists for the home indicator must not become padding under a composer or bar: that is an empty band, the same defect as a top band. The bar stays at the edge with its normal gap, its end controls step in by the curve's width, and only content that would sit under the indicator mid-screen moves.
- Cover every surface, not just the one where the defect was reported: public pages, the fixed header's rows (including a search row that is absolutely positioned across the bar), full-screen search or menu overlays and their top rows, sheets, chat streams. A photo or an empty shade layer running on below the corner is a backdrop and passes under it; narrowing it leaves a raw stripe of photo beside the shade. A short row of items side by side (chips, a swipeable strip) narrows as a whole; narrowing its items one by one changes nothing. A heading that decides "fits on one line" must measure its own row, not the window, or it stays unwrapped under the corner. When marking containers nest, each one owns and clears only its own marks; an outer one removing an inner one's marks makes them flip until a guard freezes them wrong.

- Rounded display corners cut content near them even where every inset is zero (the top edge in landscape). A bar near that edge moves its end controls inward by the curve's width at their height; a circle of the corner radius with a few points of margin is close enough. CSS cannot do that arithmetic on lengths, so take the clearance from the host.

Padding the whole page's inline edge with a side inset that comes from a corner cutout leaves a full-height empty strip; that is a bug, not caution. The one deliberate exception is a side column that holds the navigation on a closed outer display (see [foldable postures](foldable-postures.md)): pad the in-flow page root by the column's width only while the rail is shown (for example `:root.side-rail:has(nav.tabs) body > .app-root { padding-inline-end: var(--rail-width) }`), never the fixed layers, so bleeding strips end at the column and bars, sheets, and dialogs keep the whole screen. Compute the rail state in script from the bridged occlusion rectangle; CSS cannot compare two inset values. Where the exact geometry matters in a web view, bridge the native occlusion rectangles (iOS `UIViewReservedRegion` of the occlusion kind, Android `DisplayCutout.getBoundingRects()`) into CSS variables and keep the edge inset as the fallback for hosts that do not provide them. Use `viewport-fit=cover` only with deliberate edge-to-edge layout and safe-area handling. Avoid double-padding nested bars. Check landscape notches and gesture areas, including standalone PWA mode. See [viewport concepts](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/CSSOM_view/Viewport_concepts) and [environment variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/env).

## Keyboard and zoom

Test focus on the last field with the real on-screen keyboard. The label, error, input, and next useful action must remain reachable. A fixed footer must not obscure the focused control. Prefer browser scrolling and a correct layout over a permanent JavaScript keyboard-height variable.

For a demonstrated floating-control problem, inspect `window.visualViewport` with feature detection and observe its `resize`/`scroll` events. Its size/offset/scale describe the visible viewport; changes can also mean zoom or browser UI movement. They do not identify keyboard state reliably. Clean up listeners and avoid layout/write feedback loops. The [VisualViewport API](https://developer.mozilla.org/en-US/docs/Web/API/VisualViewport) is a geometry tool, not permission to counteract user zoom. Use the VirtualKeyboard API or `interactive-widget` settings only when the target browsers and product actually support the intended behavior; do not set overlays globally to compensate for an unrelated layout bug.

At 200% text and desktop 400% page zoom, recheck navigation, modal controls, and reflow. Do not reduce typography until it fits. Allow local scroll for genuinely two-dimensional tables, with a usable keyboard path when scrolling requires it. Preserve a meaningful DOM order when moving sidebars below content. Hover enhancements need touch and keyboard access to the same action.

## Foldable browsers and segmented viewports

First make the ordinary resizable layout work; many foldable browsers expose one continuous rectangle. A fold is not necessarily an occluding hinge. Where actually supported, inspect `horizontal-viewport-segments` / `vertical-viewport-segments` media features and `env(viewport-segment-width 0 0)`, corresponding positions, and segment gaps. Select pane placement from exposed geometry, not model strings. Review [horizontal viewport segments](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/%40media/horizontal-viewport-segments) and the linked compatibility tables.

Scope segment styling to a matching supported media query. Preserve a deliberate ordinary one-window layout when segmentation is not exposed; do not fabricate a hinge or silently route a command elsewhere. Validate API maturity before adding experimental JavaScript posture/segment interfaces. Do not copy an old `visualViewport.segments` sample into a current implementation without finding its actual supported specification.

Apply [the shared posture and overlay policy](foldable-postures.md) when geometry and posture are available. Upper/lower segments use the same tabletop policy across Flip and landscape Fold. For stacked segment geometry, index `0 1` identifies the lower segment; derive its actual top, height, and edges from the exposed environment variables, not `50vh`. Segmentation alone does not establish a half-open posture. Keep a bottom-origin panel and its animation inside the lower region. Inspect portals, fixed positioning, and transformed containing blocks so a global overlay host does not recenter it in the full viewport or open it above the hinge.

Keep controls, focus rings, dialogs, text, and drag handles out of a known occluded region. For an editor, a list/detail split can be useful on left/right regions; a full-screen video/canvas needs a different policy. Browser viewport emulation does not prove posture events or native fold geometry. A responsive site cannot request a private Flip cover panel or rear-screen canvas. Test browser navigation, state restoration, and keyboard behavior during resize/fold separately from layout screenshots.

For tabletop text entry, follow [the composer and keyboard policy](foldable-postures.md). Keep persistent input near the top of the lower segment, distinct from bottom-origin sheets. Inspect actual focus scrolling and visual/layout viewport changes on the target mobile browser. Avoid shrinking both regions by the same keyboard offset or recalculating a hinge from the resized viewport midpoint. If exposed geometry cannot establish keyboard clearance, report that limitation rather than claiming verified laptop-mode typing.
