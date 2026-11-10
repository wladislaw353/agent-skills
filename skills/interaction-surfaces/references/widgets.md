# Create widgets that solve a recurring job

Use when a repeated glance or command can save a person effort. Read the relevant platform section after defining the job. A widget is user-selected persistent access, not a small advertisement or a substitute for every notification. Checked 2026-11-06; recheck current SDK APIs, host capabilities, and library release status.

In a broad project review, use [scenario discovery and selection](project-opportunities.md) first. Explain the proposed widget's real job, dependencies, and effort, then let the person choose it. An already requested widget implementation does not need another selection step.

## Proactively choose a useful widget

During relevant product work, inspect recurring tasks and trace existing data/actions using the entry point's evidence and benefit checks. Consider a candidate only when persistent access makes an actual job easier; no widget is a valid outcome. Implement it when native targets/capabilities already exist and the assignment authorizes it. Adding an extension, watch target, SDK, or backend that expands scope needs the same scope decision as any other feature. Do not add every possible widget or invent a user need.

For each candidate, define the repeatable question, minimum truthful answer, safe next action, configuration, freshness, and why opening the app is avoidable. Prefer a concise decision in the task discussion to a mandatory design document.

The following are conditional examples, not proof that an app in that category supports the listed action. For example, a task list without completion cannot acquire a completion command merely to fill a widget.

| Product job | Candidate | Useful content/action | Fit check |
| --- | --- | --- | --- |
| Tasks / habits | Today / next task | Relevant item, actual progress, complete a chosen item | Completion must target the correct item/account |
| Travel / delivery | Next trip / current order | Event time, phase, age, exact details link | Live monitoring may fit a Live Activity/Update better |
| Smart home | Selected room/device | Confirmed status, explicit set-state action | Connectivity/authentication; never pretend an unconfirmed door is locked |
| Focus / timer | Current session | Known remaining time, supported pause/resume | Widget refresh scheduling is not a per-second timer service |
| Notes / capture | Start one capture task | Real shortcut into the input/capture flow | Privacy and microphone/camera permission remain in the owning app flow |
| Account / finance / health | User-selected summary | Minimal redacted snapshot | Public placement and stale data can make a widget inappropriate |

Rank by frequency, effort saved, reach, attention cost, privacy, and maintenance. If no honest data is available, show the designed unavailable state rather than a demo success. Do not fill every size with extra statistics merely because there is room.

## Specify behavior before drawing the happy state

- Separate widget instance identity, configuration, account, and domain object. Two widgets can point at different objects. Widget deletion is not object deletion.
- Define preview/sample, unconfigured, empty, current, stale, offline, signed-out, pending command, failure, and completion states as relevant. Gallery preview data must never leak into the real widget repository.
- Choose the smallest size's one primary answer first. Larger sizes may add useful context/actions; they should not require different domain truth. Show units, context, and freshness even if supporting labels disappear in compact rendering.
- Keep real commands idempotent and account-bound. “Set paused” is safer across concurrent surfaces than an ambiguous toggle. Revalidate permissions/state in the handler; a visible widget is not authorization.
- Decide whether a tap executes a supported action or opens the exact task in the app. Do not render a nonfunctional checkbox to suggest interactivity on an unsupported host.
- Persist shared data before requesting a host update. Distinguish background work, data storage, rendering, and host scheduling. A reload request is not an impression or an update delivery guarantee.

## Apple WidgetKit implementation

1. Inspect existing app/extension targets, signing, and deployment range. Add/reuse a Widget Extension and include its widget in `WidgetBundle`. Define a stable widget `kind`. Use `StaticConfiguration` with `TimelineProvider` or `AppIntentConfiguration` with `WidgetConfigurationIntent` and `AppIntentTimelineProvider` for configurable content. See [creating a widget extension](https://developer.apple.com/documentation/widgetkit/creating-a-widget-extension) and [configuration](https://developer.apple.com/documentation/widgetkit/making-a-configurable-widget).
2. Define a `TimelineEntry` carrying display-safe snapshot data and a date. Implement placeholder, snapshot, and timeline using the appropriate provider protocol. Placeholder/snapshot is not a license to ship sample balances or fake tasks. Read the selected object/configuration in the real provider path.
3. Select supported `WidgetFamily` values and adapt the view with the family environment. Inspect the current target's container/background, rendering-mode, and content-margin APIs. Home/Lock Screen, StandBy, desktop, and watch contexts differ; a phone widget is not automatically a watch extension.
4. If the app and extension share local state, use a properly entitled App Group container or the app's existing supported data architecture. Don't assume ordinary `UserDefaults.standard` is shared across processes. Handle migration, sign-out, and file protection; fetching content must fit extension execution constraints.
5. Use `Link` / `widgetURL` for appropriate object-specific navigation. For supported interactive widgets, use `Button(intent:)` / `Toggle(..., intent:)` and the correct App Intent. Put the command in its actual execution targets and use the existing domain operation; an arbitrary SwiftUI button closure is not the widget action contract. Follow [interactive widget guidance](https://developer.apple.com/documentation/widgetkit/adding-interactivity-to-widgets-and-live-activities).

Timeline construction pattern, with project-defined `entry` and an honest next refresh request:

```swift
let timeline = Timeline(entries: [entry], policy: .after(nextRefreshDate))
// After committing changed shared data in the app:
WidgetCenter.shared.reloadTimelines(ofKind: widgetKind)
```

This fragment is not a full provider. Choose `.atEnd`, `.after`, or `.never` based on the content contract. Future timeline entries can represent predictable changes; they cannot predict a driver's real location. Use supported system date/timer views for known time rather than forcing a reload every second. Respect budgets and inspect [timeline freshness](https://developer.apple.com/documentation/widgetkit/keeping-a-widget-up-to-date). A requested date is not an exact background alarm.

Give configurable entities a clear selection and real lookup path; handle deleted items and unavailable accounts. On sign-out, remove private shared data and request relevant widget reloads. Test App Intent errors, multiple widget configurations, locked-device actions, and the actual smallest family. A Control Center `ControlWidget` uses a separate supported Control/App Intent contract; it is not merely a tiny timeline widget.

## Android App Widgets with Glance or RemoteViews

Keep the project's supported implementation. Jetpack Glance uses composable syntax but renders into App Widget/RemoteViews constraints; it is not unrestricted Compose UI. Existing RemoteViews widgets can remain appropriate. Follow [Glance setup](https://developer.android.com/develop/ui/compose/glance/create-app-widget) and [lifecycle/update guidance](https://developer.android.com/develop/ui/compose/glance/glance-app-widget).

1. Implement `GlanceAppWidget.provideGlance(context, id)` and render with `provideContent`. Add a `GlanceAppWidgetReceiver` returning that widget. Register the receiver, `android.appwidget.action.APPWIDGET_UPDATE`, and `android.appwidget.provider` metadata referencing the `appwidget-provider` XML. Use the actual manifest/export rules for the installed platform and Glance version.
2. Define minimum/resize dimensions, supported resize directions, descriptions and real picker previews. Use `SizeMode.Responsive` with meaningful `DpSize` variants or `SizeMode.Exact` when required, then render from `LocalSize` as appropriate. Glance imports and components differ from normal Compose. Inspect [Glance UI sizing](https://developer.android.com/develop/ui/compose/glance/build-ui); launcher cell dimensions are not fixed physical phone pixels.
3. Persist domain data outside the transient widget/receiver object. Use per-instance configuration and appropriate Glance state for presentation settings; don't create an independent source of truth for order status in every widget. Resolve Android `appWidgetId` and `GlanceId` through documented manager APIs rather than treating them as interchangeable.
4. For navigation, use `actionStartActivity` with an explicit task destination. For supported background actions, inspect `actionRunCallback` / `ActionCallback.onAction` and real action parameters. Run short work in its allowed context; schedule legitimate longer work using the existing worker infrastructure. Do not put a network loop inside the composition.
5. After persistence/state changes, call `update(context, glanceId)` for an instance or `updateAll(context)` when the affected instances warrant it. `updateAppWidgetState` alone does not update host rendering. Avoid broad refreshes that multiply network work for every configured size/instance.

Illustrative structure; the snapshot, resources, configuration, and action handler belong to the project:

```kotlin
class TaskWidget : GlanceAppWidget() {
    override suspend fun provideGlance(context: Context, id: GlanceId) {
        val snapshot = loadWidgetSnapshot(context, id)
        provideContent { TaskWidgetContent(snapshot) }
    }
}
class TaskWidgetReceiver : GlanceAppWidgetReceiver() {
    override val glanceAppWidget: GlanceAppWidget = TaskWidget()
}
```

The fragment does not define scheduling or manifest metadata. Keep network/database loading out of repeated size-bucket rendering. Downsample large images and share a decoded bitmap where appropriate; verify URI permissions for the host rather than assuming app-private files are readable by a launcher. Use accessible descriptions and supported themed colors. An App Widget does not require notification permission just because it is glanceable.

For user pinning, check `AppWidgetManager.isRequestPinAppWidgetSupported` and use `requestPinAppWidget` in a deliberate user flow. The host/user controls placement. If a configuration activity is required, follow the current configuration action, widget-ID, result, and initial-update contract; a successful configuration result without usable initial content is incomplete. Do not auto-pin a widget or invent a successful placement event.

Android update intervals and WorkManager scheduling have independent limits; neither is a continuous timer. Select updates from meaningful data changes and the permitted scheduling budget. Implement removal/cleanup of per-instance resources without deleting domain objects. Verify restore/ID remapping, launcher process restart, resize, account switch, offline updates, and multiple host implementations.

## Watch, cover, and web boundaries

For Apple Watch widgets/complications, Wear Widgets/Tiles, and watch-face data sources, follow [Watches](watches.md); they have distinct targets, manifests, renderers, and capabilities. A launcher widget does not automatically become a Flip cover widget. Resolve that OEM contract through [Secondary displays](secondary-displays.md).

On a website, distinguish an in-page dashboard/support widget from an OS home-screen widget. A PWA manifest shortcut or Web Push subscription does not provide generic WidgetKit/Glance installation. Build useful responsive web panels with semantic controls and real state; use a native bridge/target only when it exists and is authorized. Do not promise OS placement from ordinary HTML.

## Acceptance checks

Add/configure the widget, verify the smallest/largest supported sizes, resize it, and follow its exact task link. Test multiple instances with different objects/accounts, stale/no-data state, signed-out state, action retries/failure, deletion/restoration, text scaling, dark/accented/dimmed contexts, and accessibility. Verify app-to-widget data change plus host refresh on the target OS/launcher. Record simulator versus physical-device evidence separately. Measure repeated work saved and successful commands; user placement and host rendering are different from analytics impressions.
