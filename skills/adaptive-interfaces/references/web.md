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

Apply safe-area padding to the owning edge container, for example `max(var(--space), env(safe-area-inset-bottom, 0px))`. The zero is the documented no-inset case, not guessed geometry. Use `viewport-fit=cover` only with deliberate edge-to-edge layout and safe-area handling. Avoid double-padding nested bars. Check landscape notches and gesture areas, including standalone PWA mode. See [viewport concepts](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/CSSOM_view/Viewport_concepts) and [environment variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/env).

## Keyboard and zoom

Test focus on the last field with the real on-screen keyboard. The label, error, input, and next useful action must remain reachable. A fixed footer must not obscure the focused control. Prefer browser scrolling and a correct layout over a permanent JavaScript keyboard-height variable.

For a demonstrated floating-control problem, inspect `window.visualViewport` with feature detection and observe its `resize`/`scroll` events. Its size/offset/scale describe the visible viewport; changes can also mean zoom or browser UI movement. They do not identify keyboard state reliably. Clean up listeners and avoid layout/write feedback loops. The [VisualViewport API](https://developer.mozilla.org/en-US/docs/Web/API/VisualViewport) is a geometry tool, not permission to counteract user zoom. Use the VirtualKeyboard API or `interactive-widget` settings only when the target browsers and product actually support the intended behavior; do not set overlays globally to compensate for an unrelated layout bug.

At 200% text and desktop 400% page zoom, recheck navigation, modal controls, and reflow. Do not reduce typography until it fits. Allow local scroll for genuinely two-dimensional tables, with a usable keyboard path when scrolling requires it. Preserve a meaningful DOM order when moving sidebars below content. Hover enhancements need touch and keyboard access to the same action.

## Foldable browsers and segmented viewports

First make the ordinary resizable layout work; many foldable browsers expose one continuous rectangle. A fold is not necessarily an occluding hinge. Where actually supported, inspect `horizontal-viewport-segments` / `vertical-viewport-segments` media features and `env(viewport-segment-width 0 0)`, corresponding positions, and segment gaps. Select pane placement from exposed geometry, not model strings. Review [horizontal viewport segments](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/%40media/horizontal-viewport-segments) and the linked compatibility tables.

Scope segment styling to a matching supported media query. Preserve a deliberate ordinary one-window layout when segmentation is not exposed; do not fabricate a hinge or silently route a command elsewhere. Validate API maturity before adding experimental JavaScript posture/segment interfaces. Do not copy an old `visualViewport.segments` sample into a current implementation without finding its actual supported specification.

Keep controls, focus rings, dialogs, text, and drag handles out of a known occluded region. For an editor, a list/detail split can be useful on two regions; a full-screen video/canvas needs a different policy. Browser viewport emulation does not prove posture events or native fold geometry. A responsive site cannot request a private Flip cover panel or rear-screen canvas. Test browser navigation, state restoration, and keyboard behavior during resize/fold separately from layout screenshots.
