# Apple Live Activities and Dynamic Island

Use for native iOS/iPadOS activity surfaces, including their presentations on paired devices. For phone widgets, Controls, Shortcuts, media, and Wallet, also consult `surface-recipes.md`; for Apple Watch implementation and synchronization, read [Watches](watches.md). Documentation checked 2026-11-05; recheck the installed Xcode SDK and deployment target before implementation. Do not turn preview API availability into a released-device promise.

## Choose the right contract

Dynamic Island is a presentation location. ActivityKit owns an activity's lifecycle; WidgetKit and SwiftUI supply its UI inside a widget extension. A website, PWA, or cross-platform JavaScript layer cannot invoke this just by drawing a capsule. Inspect native targets/bridges first; adding a new target is a scope decision.

A delivery in progress, a followed match, or a bounded workout can justify a Live Activity. A recurring daily summary fits a widget; an event that only needs one alert fits a notification. Use system media/call integrations for their established jobs. Do not create an indefinite promotional activity.

Current documentation distinguishes standard, scheduled, and transient activities. A transient activity ends when the person leaves its interaction context; it is not a replacement for background delivery tracking. Check the exact `Activity.request` overload and `ActivityStyle` availability before using newer modes.

## Implement in this order

1. **Project setup:** Add/reuse a Widget Extension and include its Live Activity configuration in the extension's widget bundle. Enable `NSSupportsLiveActivities` in the app target. Give the app and extension access to the same attribute type. Check signing, bundle IDs, deployment targets, and installed SDK declarations. See [Apple's integration guide](https://developer.apple.com/documentation/activitykit/displaying-live-data-with-live-activities).
2. **Data model:** Define `ActivityAttributes` with stable identity/context and a nested `ContentState: Codable, Hashable` for changing values. Keep public content small; do not put images, secrets, or the entire domain object in it. Map task ID to activity ID instead of treating them as interchangeable. [ActivityAttributes](https://developer.apple.com/documentation/activitykit/activityattributes) defines that division.
3. **Availability:** Check `ActivityAuthorizationInfo().areActivitiesEnabled`; observe `activityEnablementUpdates` when relevant. Normal notification authorization is not the same setting. Handle request failure, including concurrency/resource limits, without claiming the activity started. [ActivityAuthorizationInfo](https://developer.apple.com/documentation/activitykit/activityauthorizationinfo) exposes the relevant settings.
4. **Render all needed presentations:** Use `ActivityConfiguration(for:)` for Lock Screen content and a `DynamicIsland` configuration for compact leading/trailing, minimal, and expanded regions. Design each independently. Add a task-specific `widgetURL`; verify routing and authorization on arrival. Include VoiceOver labels and stale rendering. Follow [Live Activities HIG](https://developer.apple.com/design/human-interface-guidelines/live-activities).
5. **Start locally:** On a supported runtime and authorized user flow, use `Activity<Attributes>.request(attributes:content:pushType:)`, passing `ActivityContent(state:staleDate:)`. Catch errors at the caller and explain them through the existing product UI. The content-based API requires its own availability check; it is not identical to the original 16.1 overload.
6. **Update and finish:** Use `await activity.update(content)` and `await activity.end(finalContent, dismissalPolicy:)`. Set a useful final state, then choose `.immediate`, `.default`, or `.after(...)` intentionally. Reconcile `Activity<Attributes>.activities` after app relaunch; observe `activityUpdates` and `activityStateUpdates` where required. A user dismissal ends the presentation, not the order/workout itself.
7. **Actions:** Use `Button(intent:)` / `Toggle(..., intent:)` and the appropriate App Intent, including `LiveActivityIntent` for Live Activity operations. Put intent code in the targets required by its execution context. In `perform()`, call existing domain commands; check current account/state, idempotency, and authentication. A regular arbitrary button closure is not a reliable extension action. Consult [interactive widget and activity guidance](https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities) and [LiveActivityIntent](https://developer.apple.com/documentation/appintents/liveactivityintent).

## Minimal model and lifecycle pattern

Adapt this model to the existing domain; `taskID` is identity, while changing state belongs inside `ContentState`. Include it in both the app and widget-extension targets.

```swift
import ActivityKit

struct TaskAttributes: ActivityAttributes {
    let taskID: String

    struct ContentState: Codable, Hashable {
        let revision: Int
        let publicSummary: String
        let isComplete: Bool
    }
}
```

The following is a lifecycle fragment, not a complete app. Run the content-based calls in a context available on iOS 16.2+ (or the relevant supported target). Check `ActivityAuthorizationInfo().areActivitiesEnabled` before request. `initialState`, `nextState`, `finalState`, and `freshUntil` come from validated domain data; do not manufacture a successful state to satisfy the example.

```swift
let activity = try Activity<TaskAttributes>.request(
    attributes: TaskAttributes(taskID: taskID),
    content: ActivityContent(state: initialState, staleDate: freshUntil),
    pushType: nil // Use .token only with the intended APNs integration.
)

await activity.update(
    ActivityContent(state: nextState, staleDate: nextFreshUntil)
)

await activity.end(
    ActivityContent(state: finalState, staleDate: nil),
    dismissalPolicy: .default
)
```

Catch request errors in the caller; persist the task/activity association and reconcile it on launch. The extension still needs `ActivityConfiguration(for: TaskAttributes.self)` and `DynamicIsland` with `DynamicIslandExpandedRegion` plus compact/minimal closures. Render `context.isStale` in every relevant presentation. These calls do not install a widget extension, schedule a background loop, or provide an action handler. Use the procedure above to complete those pieces, then build the actual app and extension together.

## Updates from the server

Add this only when the task needs it and the backend/APNs work is authorized. A local deterministic timer may not need remote updates. Never promise extension networking or continuous background execution.

For a token-based integration:

- Enable Push Notifications for the app. Request the activity with `pushType: .token` and observe `activity.pushTokenUpdates` asynchronously; the token may not be immediately available and may rotate.
- Send the token through an authenticated backend endpoint associated with account, task, activity, environment, and app version. Do not log tokens as analytics. Remove stale registrations and handle APNs errors.
- Use `apns-push-type: liveactivity` and `apns-topic: <app-bundle-id>.push-type.liveactivity`. Match APNs sandbox/production and signing configuration.
- Supply `aps.timestamp`, `event` (`update` or `end`), and `content-state` matching the Swift Codable model. Set `stale-date` and final `dismissal-date` when appropriate. APNs envelope times are Unix seconds; do not assume a Swift `Date` inside custom state has the same encoding. Use the decoding strategy documented by Apple and verify a fixture against the actual model.
- Keep **ordinary APNs device tokens**, **per-activity update tokens**, and **push-to-start tokens** separate. Remote start uses `pushToStartTokenUpdates` and a start payload with the attributes/type and alert requirements. Do not send an update payload to a start token.
- Broadcast channels are another transport for shared events on supported releases. They need separate capability/configuration and are inappropriate for mixing personalized order data into a shared audience. Do not enable them merely because they exist.
- Prefer priority 5 for nonurgent changes; use priority 10 selectively. `NSSupportsLiveActivitiesFrequentUpdates` and `frequentPushesEnabled` do not grant unlimited throughput or delivery guarantees.

Use [Apple's APNs integration contract](https://developer.apple.com/documentation/activitykit/starting-and-updating-live-activities-with-activitykit-push-notifications) for current payloads, budgets, and start/broadcast support. These instructions are a checklist, not a replacement for that protocol specification.

## Freshness, limits, and paired devices

At the documentation check date, standard activities have an eight-hour active limit, potentially four further hours of Lock Screen retention, and a combined 4 KB static/dynamic content budget. Reverify before using these numbers in a product requirement. The rendering sandbox cannot fetch arbitrary network data or location updates. Keep assets appropriately sized.

`staleDate` changes freshness; it does not invent a new ETA or automatically write your stale-state message. Render `context.isStale` deliberately, and stop implying live tracking. Use system timer text for known times instead of per-second pushes. An ETA remains a forecast.

Use `.supplementalActivityFamilies([.small])` and `@Environment(\.activityFamily)` when the installed SDK supports a custom small layout for Apple Watch/CarPlay. Consult [custom activity views](https://developer.apple.com/documentation/activitykit/creating-custom-views-for-live-activities). Account for StandBy, limited Dynamic Island width, and macOS presentation where relevant. A mirrored Live Activity is not a standalone watchOS or macOS application. CarPlay disables Live Activity buttons and toggles; essential meaning must remain without them.

## Proof before completion

Check signing/build, disabled activities, no-island devices, each supported layout, multiple activities, long text, VoiceOver, Lock Screen privacy, `isStale`, final dismissal, relaunch/reconciliation, and authenticated deep links. For remote updates, test real APNs start/update/end, token rotation/invalidity, payload decoding, delayed/out-of-order messages, offline state, and denied frequent updates. Test force-quit separately from ordinary suspension. A simulator screenshot is layout evidence, not proof of APNs delivery or paired-device behavior. If a device/SDK is unavailable, state exactly which checks remain.
