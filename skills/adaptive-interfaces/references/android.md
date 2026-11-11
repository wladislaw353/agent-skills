# Adaptive Android interfaces and foldables

Use for native Android UI and native containers around cross-platform content. Checked 2026-11-06. Resolve the installed Compose Material 3 Adaptive / `androidx.window` versions and the target SDK before selecting symbols. Do not combine classes from incompatible stable/alpha examples.

## Window-driven layout

Get the current app window geometry, not `Display.getRealSize`, physical resolution, or a one-time `Configuration.orientation`. Multi-window, desktop mode, orientation, and folding can change usable space while the same activity is alive. With Compose Adaptive, inspect the available `currentWindowAdaptiveInfo` / newer `currentWindowAdaptiveInfoV2` and their returned `windowSizeClass`; documentation and release generations can differ. Use the actual imported type and width/height breakpoint methods supported by the dependency. Pass adaptive state into the screen rather than recomputing brand rules inside each child.

Use the [window-size-class guide](https://developer.android.com/develop/adaptive-apps/guides/use-window-size-classes) and [adaptive implementation guidance](https://developer.android.com/develop/adaptive-apps/guides/adaptive-dos-and-donts). Compact/medium/expanded width and additional large classes are layout inputs, not device identities. A wide, short window can need less chrome than a tall phone. Test the real content and font scale at the class boundaries.

For list-detail, use the version-appropriate `NavigableListDetailPaneScaffold` and navigator or `ListDetailPaneScaffold`; consider `SupportingPaneScaffold` for primary content plus tools. Inspect dependency and experimental opt-in requirements. Verify pane back behavior and predictive Back; do not place two unrelated navigation stacks side by side and hope selection remains consistent. Follow [list-detail implementation](https://developer.android.com/develop/adaptive-apps/guides/list-detail).

For View-based apps, preserve the stack: use current window metrics, ConstraintLayout/adaptive resource layouts, and WindowManager integration as appropriate. Resource qualifiers can provide layout variants, but should not reset the domain state. Do not rewrite a functioning View app into Compose solely for this task.

## Fold geometry and posture

Collect `WindowInfoTracker.getOrCreate(context).windowLayoutInfo(activity)` with lifecycle-aware cancellation. Inspect its `displayFeatures` for `FoldingFeature`, including:

| Property | Decision |
| --- | --- |
| `bounds` | Actual fold/hinge geometry in the reported window coordinate system |
| `orientation` | Horizontal versus vertical fold in the current window, not the device's default orientation |
| `state` | Flat versus `HALF_OPENED`, subject to available device reporting |
| `isSeparating` | Whether content should treat the regions as separated |
| `occlusionType` | Whether the feature hides content, including `FULL` occlusion |

Convert coordinate spaces and units deliberately: reported pixel bounds are not Compose dp. Account for where the content window begins. A zero-width crease can affect posture without concealing pixels; not every fold needs a blank gutter. Do not equate half-open with a particular marketing name or assume a fixed 90-degree angle.

Apply [the shared posture and overlay policy](foldable-postures.md). Flip tabletop and Fold rotated into landscape tabletop use the same viewing-above/controls-below arrangement. A vertical separating feature in the current window can suit left/right list-detail; do not pin that policy to the Fold name. If the task benefits from one pane, keep it coherent in a usable region. See [fold-aware app guidance](https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/make-your-app-fold-aware).

Inspect the actual bottom-sheet/dialog host. A separate modal window may ignore constraints applied to the underlying pane. In tabletop, place bottom-origin panels in the lower region, limit their expanded height to its usable bounds, and animate from its bottom. Keep keyboard avoidance, drag/dismiss, focus, and accessibility working there; do not anchor a sheet at the hinge and expand it into the upper viewing region. Use the installed toolkit's supported positioning APIs and verify the whole host, not just inner content padding.

## Insets, keyboard, and input

Use the current edge-to-edge setup and [window insets](https://developer.android.com/develop/ui/compose/system/insets). Choose ownership for `WindowInsets.safeDrawing`, system bars, display cutout, and IME. With Compose, inspect `windowInsetsPadding`, `imePadding`, and inset consumption before layering modifiers. Nested safe-area and Scaffold content padding can double-apply edges. In Views, use `WindowInsetsCompat` and the compatible animation/lifecycle path.

Keep the focused field, its error, and the essential action reachable with the IME visible. Do not assume the keyboard always resizes the window the same way. Test gesture and button navigation, display/font scaling, landscape, floating keyboards when relevant, TalkBack, physical keyboard focus, and pointer input. A density change must not preserve obsolete cached pixel geometry.

For tabletop text entry, use [the composer and keyboard policy](foldable-postures.md): input near the upper edge of the lower region, system IME space below where supported. Convert IME occlusion and fold bounds into the same coordinates; account only for their actual intersection and avoid applying IME padding again after window resize/inset consumption. Keep upper content useful and collapse secondary controls during typing. Test the real keyboard; an app cannot assume it controls every IME's position.

## Continuity: Fold, Flip, rotation, and multi-window

Hoist the selected object, draft, validation state, and task/session outside layout branches. Use `ViewModel`, `SavedStateHandle`, `rememberSaveable` for appropriate restorable UI state, and durable storage for data that must survive process death. `ViewModel` alone does not survive process death. Use stable item keys and preserve a meaningful scroll anchor. A fold/unfold can trigger activity recreation or a display transition, depending on the device.

Handle resize without repeating requests that create orders, payments, recordings, or workouts. Keep the current detail object when expanding to two panes and restore the intended navigation path when collapsing. Preserve playback/session ownership rather than creating another player with a new composition branch.

Review target-specific platform behavior: newer Android releases restrict the effectiveness of orientation/aspect/resizability locks on larger windows. Do not add locks or manifest exceptions just to avoid supporting resizing. Record any legitimate specialized exception and verify it against current platform rules.

## Cover screens and multiple displays

Separate three cases: the same app window being resized/moved, a permitted app launch on a cover display, and an OEM-owned card/widget surface. A correct adaptive layout establishes only the first case. For `Presentation`, `ActivityOptions.setLaunchDisplayId`, or Window Area transfer/presentation, verify display access and lifecycle independently. Cover-screen permission and feature availability vary by model, firmware, region, and app type.

Use the existing display SDK integration if present. Do not install a vendor SDK, hard-code a display ID, use hidden APIs, or draw an overlay to claim native cover support. A Pixel Fold window-area operation is not proof of a Galaxy Flip cover-widget API. Test outer → inner → outer, fold during text entry, process recreation, two active panes, split-screen, and an actual separating hinge. Android Studio emulation is useful evidence for its configured environment; it cannot establish an OEM admission or physical cover-display result.
