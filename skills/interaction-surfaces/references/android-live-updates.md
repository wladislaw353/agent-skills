# Android Live Updates and OEM integration

Read for ongoing Android tasks. Documentation checked 2026-11-05. Recheck target/compile SDK, AndroidX Core, OS build/QPR, and vendor firmware. The checklist targets current documentation, not one frozen minimum version. For watch-local and bridged notifications also read [Watches](watches.md).

## Build a platform notification first

1. Define the user-started task, meaningful states, end condition, and task-specific `PendingIntent`. Apply the [Live Update criteria](https://developer.android.com/develop/ui/views/notifications/live-update); ordinary background work, marketing, and long passive waits do not automatically qualify.
2. Create the notification channel on supporting releases. Check channel/app settings and request `POST_NOTIFICATIONS` in context on runtimes requiring it. Separately declare `POST_PROMOTED_NOTIFICATIONS` and inspect `NotificationManager.canPostPromotedNotifications()` where available. Promotion is not another ordinary runtime permission prompt.
3. Build using the installed `NotificationCompat.Builder` or platform API. Set a valid small icon, content title, ongoing state, and `setRequestPromotedOngoing(true)` where supported. Provide concise `setShortCriticalText()` when useful. Use an intentional alert policy such as `setOnlyAlertOnce(true)` for repeated progress changes; confirm that urgent domain events still get their designed treatment.
4. Choose a supported style. For stages, use [ProgressStyle](https://developer.android.com/about/versions/16/features/progress-centric-notifications), with meaningful progress segments/points. For measured values, inspect [MetricStyle](https://developer.android.com/develop/ui/views/notifications/metric-style) and runtime support. Do not pretend arbitrary RemoteViews/Compose layouts qualify for promoted presentation.
5. Use one stable notification ID per task; call `NotificationManagerCompat.notify()` to update it. Distinguish tasks/accounts to avoid replacing another activity. On completion, transition to the explicitly designed final notification or remove it; never leave an endless active card.
6. Keep `hasPromotableCharacteristics()`, `canPostPromotedNotifications()`, and the observed `FLAG_PROMOTED_ONGOING` separate. None proves the user saw a chip. Do not write analytics as though requesting promotion guaranteed display.
7. Connect any live updates to the existing source of truth. FCM/OEM push, local work, and foreground services have independent restrictions. A notification API does not keep the app running or guarantee push execution. Use a foreground service only for a qualifying task/type, with the required lifecycle and permissions; do not create one merely to keep the chip visible.

Use current symbol references to choose platform versus AndroidX methods and guards. Do not write a method on a compatibility class because a similarly named platform method exists. Ordinary notifications are a distinct designed path, not silent permission bypass; preserve a pre-existing primary app flow and obtain authorization for additional fallback channels.

## Concrete construction and promotion diagnostics

Declare these separately in the application manifest:

```xml
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
<uses-permission android:name="android.permission.POST_PROMOTED_NOTIFICATIONS" />
```

Create `NotificationChannel` on API 26+, then inspect the actual existing channel's importance; recreating a channel does not override a user's settings. Request notification permission with the app's existing permission flow on applicable API/target combinations. `NotificationManagerCompat.areNotificationsEnabled()` and channel state answer different questions from promotion eligibility.

This construction fragment assumes a created channel, installed AndroidX Core with these symbols, localized public-safe strings, a real icon, and a task-specific immutable navigation `PendingIntent`. Supply those inputs from the project; do not ship demo values as domain data.

```kotlin
val builder = NotificationCompat.Builder(context, channelId)
    .setSmallIcon(R.drawable.ic_task_status)
    .setContentTitle(publicTitle)
    .setContentText(publicSummary)
    .setContentIntent(openTaskIntent)
    .setOngoing(true)
    .setRequestPromotedOngoing(true)
    .setOnlyAlertOnce(true)
    .setVisibility(NotificationCompat.VISIBILITY_PRIVATE)

shortCriticalText?.let { builder.setShortCriticalText(it) }
// Apply the selected compatible style before build().
val notification = builder.build()
// Only after the actual runtime permission/channel checks:
NotificationManagerCompat.from(context).notify(taskTag, taskNotificationId, notification)
```

Use `(tag, id)` consistently for subsequent `notify` calls and `cancel(tag, id)`. Give `PendingIntent` a distinct identity per task, not merely different extras. Use mutable intents only where the action API requires them, such as supported remote input.

For stages, the [AndroidX ProgressStyle API](https://developer.android.com/reference/androidx/core/app/NotificationCompat.ProgressStyle) supplies `setProgressSegments`, `setProgressPoints`, and `setProgress`. Segment lengths sum to the progress maximum. A point's position and the current value use that same scale. If only the phase is known, describe the phase instead of inventing a numerical percentage. Use `NotificationCompat.ProgressStyle` with Compat Builder; platform `Notification.ProgressStyle` belongs to the platform builder.

For [MetricStyle](https://developer.android.com/develop/ui/views/notifications/metric-style), use `Notification.MetricStyle` with `Notification.Metric(value, label)` and the appropriate `MetricValue` subclass, such as `FixedInt`, `FixedFloat`, or `TimeDifference`. Inspect the exact value constructor and unit options in the target SDK; there is no `Metric.Builder` in the checked API. Put the essential metric first; collapsed presentation may omit others and units. Do not invent a Compat equivalent. **Wear OS does not currently render MetricStyle metrics**; provide a meaningful watch presentation through the watch guide.

| Check | Meaning | Implementation consequence |
| --- | --- | --- |
| Supported standard/BigText/Call/Progress/Metric style, ongoing, title | Notification shape may qualify | No custom RemoteViews, group summary, `setColorized(true)`, or `IMPORTANCE_MIN` |
| `hasPromotableCharacteristics()` | Structural eligibility | Does not answer the app-setting or device-rendering question |
| `canPostPromotedNotifications()` | App promotion setting on supported runtime | Offer the relevant system setting only in an explicit user flow |
| `Settings.ACTION_MANAGE_APP_PROMOTED_NOTIFICATIONS` | Settings destination | Supply this app's package using the documented extra; check resolution/availability |
| `FLAG_PROMOTED_ONGOING` on the posted notification | Observed promoted status | Inspect own active notifications where supported; still not an impression metric |

Compile/API guards must follow each symbol's availability. AndroidX method availability, platform style availability, and OEM chip rendering are separate checks. Use the [Builder reference](https://developer.android.com/reference/androidx/core/app/NotificationCompat.Builder) rather than raising dependencies blindly.

## Time, actions, and privacy

Use `setWhen`, `setUsesChronometer`, and the supported countdown option for deterministic time; preserve the correct time base and paused state. A changing forecast must come from valid domain data. Define freshness and stop promising an ETA when it is no longer justified. Avoid per-second network updates and repeated alert vibration.

Use explicit task-bound pending intents, suitable mutability flags, and direct activity destinations for navigation; avoid notification trampolines. Check account and current authorization on execution. A dismissed notification does not cancel the order. Dismissal/visibility can differ by OS: test the actual behavior instead of forcing recreation.

Set public/private/secret visibility and safe public content deliberately. Lock Screen and AOD are not private workspaces. Sensitive commands need the platform's authentication flow; hiding text alone does not secure an action.

## OEM adapter procedure

For each meaningful vendor audience, record:

- Exact model, market/ROM, OS build, API/SDK version, and supported scenario.
- Official entry point, app/package registration, approval/allowlisting, and regional eligibility.
- Local-notification extras versus server push versus partner transport; who may start/update/end.
- Template schema, allowed actions, limits/freshness, dismissal semantics, and final state.
- Expected base Android behavior and separately observed OEM presentation.

Use the vendor's documented fields and fixtures; never invent extras from a visual example or copy a partner provider's quota as an OS-wide rule. Keep OEM formatting behind a domain adapter. A Xiaomi Super Island approval does not grant rear-screen drawing; use `secondary-displays.md` for that question. If no public integration can be verified, state the uncertainty and complete the authorized platform path without brand-specific false promises.

Concrete entry points to reverify for the selected audience:

- **Samsung:** Start with the standard notification/media implementation, then verify Now Bar in the [documented device settings](https://www.samsung.com/my/support/mobile-devices/how-to-use-the-now-bar-on-the-lock-screen-of-your-samsung-galaxy-device/). Do not assume an unrestricted Now Bar drawing SDK.
- **Xiaomi:** The [Super Island developer entry](https://dev.mi.com/xiaomihyperos/ability/XiaomiHyperIsland) and [admission guide](https://dev.mi.com/xiaomihyperos/documentation/detail?pId=2140) lead to app/scenario approval, local notification payloads, and supported MiPush transport. Obtain the current payload schema and region eligibility before emitting OEM extras.
- **OPPO / vivo:** The documented EMAS channels for [Fluid Cloud](https://help.aliyun.com/zh/document_detail/2997310.html) and [Origin Island](https://help.aliyun.com/zh/document_detail/3030718.html) describe partner access and lifecycle operations. They are provider-specific contracts. Verify direct OEM access separately; do not copy their quotas into a universal Android limit.
- **HONOR:** Resolve approved scenarios and templates through [Magic Live / YOYO documentation](https://developer.honor.com/cn/docs/white_paper/guides/introduction). Package eligibility and a submitted template precede claims of capsule support.
- **OnePlus / realme / other vendors:** A named Fluid Cloud, Mini Capsule, or Live Alerts feature is not an integration contract. Test the standard path; require official app-specific access evidence before implementing an OEM adapter.
- **Huawei:** Distinguish EMUI Android behavior from the separate native HarmonyOS Live View Kit. **Nothing:** use the model-specific Glyph instructions in [Secondary displays](secondary-displays.md), not notification extras invented for an island.

## Validation

Compile with the project's SDK/dependencies; test permissions/channels on and off, requested versus actual promotion, no connection, killed process versus force-stop, multiple activities, user dismissal, stale/end state, short text truncation, private Lock Screen content, deep links, battery saver, and vendor rendering. Test at least the targeted standard and OEM combinations; report uncovered combinations. An emulator demonstrates the emulated system only, not Now Bar/HyperIsland on a physical vendor device.
