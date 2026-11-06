# Rear, cover, external, and matrix displays

Use when a device has a second screen, rear panel, cover display, or LED matrix. Checked 2026-11-05. Determine the access model before choosing code. A marketing feature, an editor for the device owner, and a third-party SDK are different evidence.

## Classify first

| Actual access | Implementation | Important boundary |
| --- | --- | --- |
| OS moves/mirrors the ordinary app | Responsive/adaptive app; preserve state across display/configuration changes | Mirroring does not imply two independent tasks |
| Display exposed for presentations | `DisplayManager` + `Presentation` | A detected display can still reject a window |
| Separate activity launch allowed | Secondary-display feature check, `ActivityManager`, `ActivityOptions` | Not the same operation as presenting a dialog |
| Foldable window-area capability | Jetpack WindowManager area API for transfer/presentation | Requires supported runtime/operation, not merely a folding hinge |
| Vendor-controlled canvas/matrix | Official OEM SDK and lifecycle | Device-specific; not general Android multi-display |
| System cards or selected partners only | Supported notification/template or approved integration | Do not assume arbitrary drawing access |

Record exact device, firmware, region, app/channel eligibility, SDK version, access result, and physical-device evidence. Do not hard-code display ID 1, equate a `Display` name with authorization, or use hidden APIs/root/overlays to claim supported integration.

## Android Presentation path

1. Enumerate current candidates with `DisplayManager.getDisplays(DisplayManager.DISPLAY_CATEGORY_PRESENTATION)`; select the intended display explicitly from verified device behavior/user selection. Do not silently pick an unrelated external monitor.
2. Create a `Presentation(activity, selectedDisplay)`. Inflate its layout using the presentation's context/resources, not metrics cached from the primary display. Observe the real window bounds/density/insets.
3. Catch `WindowManager.InvalidDisplayException` from `show()` and report the unavailable surface through the designed product state. Do not continue as if content were visible.
4. Handle removal, geometry changes, and owner lifecycle. Release the presentation and callbacks; preserve domain state separately. `Presentation` dismisses on display removal, but application-owned observers/resources still need cleanup.
5. Verify screen locking, user switch/sign-out, mirroring, secure/public data behavior, touch ownership, and physical visibility. Successful `show()` is not evidence that a person looked at it.

See [DisplayManager](https://developer.android.com/reference/android/hardware/display/DisplayManager) and [Presentation](https://developer.android.com/reference/android/app/Presentation).

A minimal enumeration step is:

```kotlin
val displayManager = context.getSystemService(DisplayManager::class.java)
val candidates = displayManager.getDisplays(
    DisplayManager.DISPLAY_CATEGORY_PRESENTATION
)
```

This fragment uses `getSystemService(Class)` on API 23+; adapt the lookup to the project's supported range. An empty result is a real unavailable state. After explicit selection, construct the `Presentation` with an Activity owner and the selected `Display`; bind the public-safe view using that presentation's context. Keep a reference only while its lifecycle is active. Catch `WindowManager.InvalidDisplayException` around `show()`, clear the reference on dismissal, and call `dismiss()` during the appropriate owner teardown. Observe `DisplayManager.DisplayListener` for changes the app owns and unregister it. Do not search for a magic ID or silently redirect output to another monitor when the chosen panel disappears.

## Launch an activity on another display

Check `PackageManager.FEATURE_ACTIVITIES_ON_SECONDARY_DISPLAYS`. On the supporting runtime, use `ActivityManager.isActivityStartAllowedOnDisplay(context, displayId, intent)` before `ActivityOptions.makeBasic().setLaunchDisplayId(displayId)`. Handle runtime errors/races and normal background-launch restrictions. Recheck API availability: the launch option and permission check were introduced at different API levels. Do not invent an older-platform bypass. [ActivityOptions](https://developer.android.com/reference/android/app/ActivityOptions) defines launch restrictions.

## Foldable rear display / dual-screen mode

Read [the foldable display-mode guide](https://developer.android.com/develop/adaptive-apps/guides/foldables/support-foldable-display-modes) against the installed `androidx.window` version. Its older examples use `windowAreaInfos`, `getCapability`, `transferActivityToWindowArea`, and `presentContentOnWindowArea`; newer reference APIs may introduce `WindowArea`/`transferToWindowArea`. Do not mix stable and alpha generations in one sample.

Find a rear-facing area, check the **specific operation** (transfer versus simultaneous presentation), and distinguish unsupported, unavailable, available, and active. Hold the session returned by the callback, close it intentionally, and handle system-ended sessions on folding, app backgrounding, power/thermal changes. Preserve task state when returning to the original screen. This path does not establish access to a Xiaomi rear panel or a Flip cover widget.

## Nothing Glyph Matrix

Use the [official Glyph Matrix kit](https://github.com/Nothing-Developer-Programme/GlyphMatrix-Developer-Kit), pinned to a reviewed release/commit with its license, supported models, system build, library, and sample. Distinguish it from the [Glyph Interface kit](https://github.com/Nothing-Developer-Programme/Glyph-Developer-Kit). Inspect the included JAR signatures before inventing Kotlin wrappers.

**App-controlled frames:** Declare `com.nothing.ketchum.permission.ENABLE`, obtain `GlyphMatrixManager`, `init(callback)`, then `register()` the supported target after connection. Use `GlyphMatrixFrame`/`GlyphMatrixObject` or the documented raw frame format. Use **`setAppMatrixFrame`**, not the Toy rendering path, for app-owned output. Stop output with `closeAppMatrix()` and unbind with `unInit()` as appropriate. Handle disconnection and system priority; a selected Toy can replace app frames. Do not promise unrestricted background rendering.

**Glyph Toy:** Declare a service with the action, metadata, preview, and behavior required by the current kit. System selection binds the service: start only its needed work in `onBind()`, stop and release in `onUnbind()`. For interactions, return the `Messenger` binder and handle documented GlyphToy events; a null binder is not an interactive implementation. Guide the user to the system Toy manager with a resolvable intent; installation alone does not select the Toy. Only one Toy is active at a time.

Phone (3) interaction capabilities differ from Phone (4a) Pro. The current kit describes the latter as AOD Toys only, without Glyph Touch, requiring AOD-support metadata. Recheck model constants and events from the kit. Its AOD event cadence is not a license to poll every second. Test user selection, deselection, higher-priority content, lock state, process/lifecycle changes, and rendering on actual LEDs. "Register" in the manager is a runtime operation; do not confuse it with obtaining partner enrollment.

Implement the adapter as explicit disconnected → connected → registered → rendering → closed states. Buffer only the latest valid domain snapshot while unavailable; do not replay expired animations on reconnect. Acquire frame dimensions and input capabilities from the verified kit/model contract. Handle commands through the same authorized domain handler as the main app; Toy selection and an SDK connection do not authenticate a user. Verify actual frame methods and event signatures against the pinned JAR/sample before compiling, rather than treating this prose as an ABI specification.

## Xiaomi, Ulefone, ASUS, DOOGEE, and future models

- **Xiaomi:** Product rear-screen cards, AI-created personal cards, notification integrations, and Super Island enrollment do not by themselves establish a rear-screen drawing SDK. Find a rear-display-specific documented contract, eligibility, template, and lifecycle. If not found, report **public arbitrary rendering not verified**, not **impossible**. Do not claim a Chinese partner path works internationally or carry access from one generation to the next.
- **Ulefone Armor 30/33 Pro:** Manufacturer documentation describes app use on the rear screen and mirroring with one touch-active screen. The ordinary adaptive app may be the correct solution. Do not infer simultaneous independent apps. Distinguish physical pixels from dp; test actual bounds, keyboard, rotation, state restoration, and touch handoff.
- **ASUS AniMe Vision / DOOGEE rear panels:** Custom animations and system features prove user functionality, not a public programmable service. Obtain an official developer contract before implementing an adapter. A laptop AniMe Matrix SDK is not automatically a ROG Phone SDK.
- **Flip/Razr cover displays:** Keep full app launch, manufacturer widgets, approved cards, and notifications separate. Screen-size adaptation proves only layout, not permission to appear on the cover.

## Microinterface design

Choose a primary answer, explicit freshness, and only a supported safe action. Design text for the physical surface; a matrix may need a simple progress shape rather than scrolling prose. Give any light pattern a learnable meaning and an inspectable textual counterpart in the app. Do not encode a critical warning only in color/animation.

Ask who sees the rear surface: the owner or another person. An intentional “show ticket” mode differs from automatically exposing private order details. Render real credentials with adequate contrast, quiet zone, expiry, and proven scanability; a tiny decorative QR does not prove access control. Remove account data on sign-out and stop stale output. Test ambient light, cover/case obstruction, viewing angle, device orientation, and battery cost; an emulator cannot establish these.

## Web boundary

A site can adapt when the OS opens a browser on that display. The web Window Management API, when available and authorized, concerns browser windows on exposed screens; it is not an OEM hardware API and cannot unlock a private panel. `window.getScreenDetails()` requires feature detection, permission, and a real multi-window use case. Do not add it just to support a narrow viewport. A PWA has no automatic Glyph Matrix or ActivityKit access.
