# Cross-platform apps, web views, and display boundaries

Use after identifying the framework actually installed. Preserve it. Shared source code does not imply identical layout metrics, permissions, lifecycle, or hardware APIs on every target. Recheck current framework/plugin documentation before using version-specific symbols.

## React Native

Use `useWindowDimensions()` for current dimensions and font scale rather than a one-time module-level `Dimensions.get` snapshot. Account for the actual parent layout with `onLayout` when a component does not own the full window. Flexbox, wrapping, minimum/maximum constraints, and sensible scroll containers should do most of the work.

Use the project's established safe-area integration; inspect the installed `react-native-safe-area-context` version when that is the dependency. Check keyboard behavior against the real platform before choosing `KeyboardAvoidingView` or another existing solution. Do not stack multiple keyboard-avoidance mechanisms and offsets until the form moves into place.

Preserve navigation/store identity through layout changes. Window width alone cannot report an occluding hinge or prove cover-screen access. Use a maintained, authorized native bridge for actual posture/display data only when the task requires it. Reconcile its bounds with React Native coordinates; verify iOS and Android separately. Official starting points: [useWindowDimensions](https://reactnative.dev/docs/usewindowdimensions) and [KeyboardAvoidingView](https://reactnative.dev/docs/keyboardavoidingview).

## Flutter

Use `LayoutBuilder` constraints for a component and current `MediaQuery` properties for window environment. Distinguish `size`, `padding`, `viewPadding`, `viewInsets`, text scaling, and `displayFeatures`; they have different meanings. Use `SafeArea` intentionally and avoid double consumption. Follow the project's navigation and restoration approach; keep selected object/draft state above layout-specific branches.

Do not replace all layout thresholds with an Android/iOS device check. Where fold features are reported, inspect the actual `DisplayFeature` bounds/type/state and the appropriate sub-screen/dialog positioning facilities in the installed Flutter version. A plugin must not claim that a foldable screen always has a physical gap. Resolve renderer and embedding differences before adding platform exceptions. Start with [Flutter adaptive and responsive design](https://docs.flutter.dev/ui/adaptive-responsive).

## Other shared frameworks

For Compose Multiplatform, .NET MAUI, Electron, Capacitor, or another stack, identify which layer owns the window, safe area, keyboard, and native feature integration. Use the installed framework's current layout/lifecycle APIs. Keep target-specific adapters at an explicit boundary rather than spreading guessed platform flags across components. A library name is not evidence of folding-device support.

For every framework, apply [the shared foldable policy](foldable-postures.md) to the actual overlay host as well as the screen layout. A React portal, native modal, or root navigator overlay can ignore a pane's constraints. In tabletop, bottom sheets must use lower-region bounds and a bottom-origin animation on both Flip and landscape Fold; verify keyboard, focus, and gestures after coordinate conversion.

## Embedded web content

Measure the real web-view container. Ensure the HTML viewport and native insets have compatible ownership; neither layer should blindly pad the same area twice. Test keyboard, focus, zoom, scrolling, navigation, bridge lifecycle, and rotation in the actual shell. An ordinary browser test and a web-view test are separate evidence.

Adding an existing web page to a wrapper does not grant CSS access to a private rear panel, a native widget target, or ActivityKit. A full app running on a small permitted external display should adapt to that window; a manufacturer-controlled card needs a separate documented integration.

## Coordinate the skills without hard dependencies

Use this skill for layout inside a permitted window. When a task also needs an external surface, apply available interaction-surface guidance for its API/access contract. When accessibility work is relevant, apply available accessibility guidance while retaining this skill's continuity and geometry checks. Do not assume those other skills exist, load every reference, or duplicate a complete design audit for a small fix. Record unavailable hardware as an unverified integration, rather than inventing a visual substitute and calling it native support.
