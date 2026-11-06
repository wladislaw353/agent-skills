# Adaptive Apple app layouts

Use for native SwiftUI/UIKit apps on the actual Apple targets in the project. Checked 2026-11-06; verify Xcode SDK declarations and deployment targets. Design from the current scene/container, safe areas, content, and Dynamic Type. Do not infer a future folding Apple device or public hinge API from an Android capability.

## SwiftUI layout decisions

- Use normal layout proposals, stacks, Grid/Lazy grids, and flexible frames before measuring. Bound reading/form content while allowing useful workspace to grow.
- Use `ViewThatFits` to choose the first candidate that fits for a localized component, such as a horizontal versus vertical action group. It is not a whole-app state/navigation switch. Put the preferred arrangement first and include an actually usable final candidate. Check [ViewThatFits](https://developer.apple.com/documentation/swiftui/viewthatfits).
- Use `AnyLayout` where layout type changes should preserve the identity of the same child views. Avoid changing explicit `.id` values merely because geometry changed.
- Use horizontal/vertical size-class environment values as coarse inputs, together with actual constraints. A size class is not a phone model or a complete description of short height, Stage Manager, or split view.
- Use `GeometryReader` or current container measurement only where a numeric dimension is genuinely required. Do not store `UIScreen.main.bounds` as the app's available size or globally publish every geometry change into domain state.

For related navigation panes, inspect `NavigationSplitView` and `NavigationStack`. Bind selection/path to stable object identity, define the intended compact presentation, and test collapse/expansion with Back. Do not assume all columns always remain visible. See [NavigationSplitView](https://developer.apple.com/documentation/swiftui/navigationsplitview).

A small illustrative layout fragment:

```swift
ViewThatFits(in: .horizontal) {
    HStack { primaryAction; secondaryAction }
    VStack(alignment: .leading) { primaryAction; secondaryAction }
}
```

Supply the real accessible action views and verify SDK availability. This chooses an action arrangement; it does not handle authorization, task identity, or navigation persistence. Avoid instantiating independent side effects in every alternate view branch.

## Safe areas and the keyboard

Assign safe-area ownership to the root/container and persistent bars. Use `safeAreaInset` when a bottom action should participate in content layout, rather than overlaying it on fields. Use `ignoresSafeArea` deliberately for backgrounds or other intended regions; applying it to an entire form can hide important input behind the keyboard/system UI. The keyboard is a changing safe-area context, not a constant height.

Use scrollable content for long forms, `FocusState` for intentional focus, and scroll-to-field behavior where required. Ensure validation errors remain discoverable with the last field focused. Test hardware keyboard, floating keyboard on relevant targets, interactive keyboard dismissal, and transition between scenes/windows. Do not dismiss input automatically on every size change.

## Dynamic Type, localization, and input

Use text styles/scalable metrics and content-driven height. Prefer wrapping, vertical action arrangements, and disclosure of secondary information over shrinking text or applying a global Dynamic Type cap. Check accessibility text sizes, long translations, RTL, VoiceOver reading order, and bold text. Do not use a screenshot-perfect fixed height for cells containing user content.

Touch, pointer, keyboard, and accessibility focus need coherent destinations. Reordering visual panes must not strand focus in a hidden column. Keep a full task path available when a sidebar collapses. Test navigation title/toolbars and modal/sheet content in short landscape; a sheet's visible content area is smaller than the device screen.

## UIKit and mixed apps

Preserve UIKit when it is the project's established stack. Use Auto Layout, intrinsic content size, content hugging/compression priorities, `safeAreaLayoutGuide`, and `keyboardLayoutGuide` where supported. For collections, use an appropriate compositional layout and inspect the layout environment's effective container size. Use the current trait observation/lifecycle APIs for the deployment target rather than deprecated callbacks copied blindly.

Keep view-controller containment and navigation semantics correct when changing columns. `UISplitViewController` can suit related panes; test its compact behavior and restoration. Bridge SwiftUI/UIKit measurements through the actual hosting container, not a guessed full-screen size. Review [Apple's layout guidance](https://developer.apple.com/design/human-interface-guidelines/layout) for the target platform.

## State and evidence

Keep domain models, selected IDs, and ongoing operations outside disposable layout branches. Use the existing observable/store architecture. Use appropriate scene restoration or persistent storage for state that must survive termination; in-memory state alone is insufficient. Check multiple windows/scenes and avoid merging unrelated scene navigation into one global selection.

Verify portrait/landscape, compact/expanded containers, iPad multitasking if targeted, keyboard open, all supported text sizes, session continuity, and focus after column collapse. Resizing a simulator provides layout evidence; it does not prove every real device, desktop scene transition, or unsupported future form factor. Websites displayed inside WKWebView still need their own CSS/viewport testing; native safe-area handling does not automatically repair web layout.
