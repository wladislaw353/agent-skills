# Apple Watch, Wear OS, and phone-watch coordination

Use for a wrist glance, quick action, workout, or task that must continue without the phone. Documentation checked 2026-11-05; verify the installed SDK, library release status, watch OS, renderer, and companion requirements before coding. Read only the platform sections relevant to the task.

## Establish a real wrist use case

Before choosing APIs, apply the entry point's product-evidence and benefit checks. Trace every displayed value and command into the working app. Do not derive actions from the app's topic: a travel app need not support check-in, a habit log need not have a timer, and a health dashboard need not record workouts. The recipes below apply only to capabilities that exist or that the user explicitly assigned as new product work.

Explain why the wrist helps in a concrete situation and why a glance, mirrored notification, or existing phone flow is insufficient or sufficient. Start with the smallest useful option; a passive glance needs no invented action. Reuse real commands with their existing restrictions. Do not add dummy handlers, optimistic success, or new backend behavior to make a watch screen appear complete. If nothing benefits from wrist access, recommend no watch integration and continue the assigned work.

## Choose the owner and surface

| Need | Apple Watch | Wear OS | Implementation decision |
| --- | --- | --- | --- |
| Follow a task started on the phone | Mirrored Live Activity, custom small family where supported | Bridged Live Update where the OEM supports it | Test bridging; do not assume a watch app is installed |
| Check recurring information | WidgetKit Smart Stack widget / complication | Wear Widget, Tile, or complication data source | Persist a fresh snapshot; system scheduling is not a live stream |
| Start or operate a task independently | watchOS app and appropriate App Intent | Wear app with watch-specific Compose UI | Authentication, command execution, and storage must work on the watch |
| Record a workout | HealthKit session and live builder | Health Services ExerciseClient | Assign sensor/session ownership to the recording device |

Specify whether the watch is a viewer, a remote control, or the task owner. A phone timer shown on a watch and a watch timer are different implementations. Decide the authoritative clock, task ID, revision, and what a disconnected command means. Do not add a companion target merely to make a phone notification appear on a watch.

## Apple: Live Activities on the wrist

Follow [the ActivityKit reference](apple-live-activities.md) for the phone-owned lifecycle. On supported SDKs, add `.supplementalActivityFamilies([.small])` to `ActivityConfiguration`, branch on `@Environment(\.activityFamily)`, and render a concise small view. Keep an intentional destination for the tap: the relevant watch task if available, or the supported phone-opening path. Verify with [Apple's custom activity views](https://developer.apple.com/documentation/activitykit/creating-custom-views-for-live-activities).

Do not start a duplicate watch workout simply because its Live Activity appears in the Smart Stack. Mirroring does not provide a watch sensor pipeline, independent networking, or guaranteed interactive controls on every presentation. Test phone disconnection and activity expiry on the paired device.

## Apple: WidgetKit complications and Smart Stack

1. Reuse or, when authorized, add the watchOS app and watchOS Widget Extension. An iOS widget extension alone is not a watch complication. Include the configuration in the watch extension's `WidgetBundle`.
2. Choose `StaticConfiguration` or `AppIntentConfiguration`, an appropriate `TimelineProvider` / `AppIntentTimelineProvider`, and the supported accessory families: circular, rectangular, inline, or corner as appropriate. Implement preview/snapshot and real timeline data separately. Use the family/environment to adapt layout.
3. Share the watch app's local snapshot with its extension using an entitled App Group container when needed. App Groups do not synchronize an iPhone and Apple Watch. Write received data locally before requesting `WidgetCenter.shared.reloadTimelines(ofKind:)`; reload requests do not guarantee immediate rendering.
4. Provide the correct task link and supported App Intent actions. Verify accented/full-color rendering, dimmed state, privacy, and VoiceOver. Keep important units and state readable without animation.

Use [Apple's accessory widget and complication guide](https://developer.apple.com/documentation/widgetkit/creating-accessory-widgets-and-watch-complications). For an existing ClockKit implementation, plan migration for the supported OS range; do not rewrite unrelated watch code automatically.

## Apple: WatchConnectivity transport

Configure `WCSession.default` in both apps: check `isSupported()`, assign a retained delegate, `activate()`, and handle asynchronous activation/errors. On iOS, handle watch switching through `sessionDidBecomeInactive` / `sessionDidDeactivate` and reactivate as required. See [WCSession](https://developer.apple.com/documentation/watchconnectivity/wcsession).

| API | Use it for | Do not assume |
| --- | --- | --- |
| `updateApplicationContext(_:)` | Latest replaceable state | Delivery of every intermediate state |
| `transferUserInfo(_:)` | Queued background information | Immediate execution or a confirmed business outcome |
| `sendMessage(_:replyHandler:errorHandler:)` | Immediate interaction with a reachable counterpart | Offline queuing or success merely because the message was sent |
| `transferFile(_:metadata:)` | File transfer with lifecycle callbacks | Continuous foreground connectivity |

Check activation and method-specific reachability/installation conditions. Version payloads; include task/account identity and command IDs. Persist received state and dispatch UI work appropriately. Explicitly define retry/expiry for pending commands. Use the existing authenticated backend for authorized independent operation; do not invent a cloud fallback when the paired app is unavailable.

## Apple: independent workout lifecycle

Enable HealthKit and the appropriate workout background capability in the actual watch target. Provide health usage descriptions and request only the data types the task needs. Construct `HKWorkoutConfiguration`, `HKWorkoutSession(healthStore:configuration:)`, its `associatedWorkoutBuilder()`, and `HKLiveWorkoutDataSource`. Assign session and builder delegates before collection. Start the session with `startActivity(with:)` and the builder with `beginCollection(withStart:)` using the same logical start time.

Drive pause/resume and UI from session state and delegate data. With the current stopped-state flow, call `stopActivity(with:)`, wait for `.stopped`, then `endCollection(withEnd:)`, `finishWorkout`, and `session.end()`; check errors and the saved result. Verify the sequence for the deployment target in [running workout sessions](https://developer.apple.com/documentation/healthkit/running-workout-sessions). Recover interrupted sessions when supported. A missing sample is unavailable data, not a zero heart rate.

For supported companion workout mirroring, inspect `startMirroringToCompanionDevice` and remote-session commands on [HKWorkoutSession](https://developer.apple.com/documentation/healthkit/hkworkoutsession). Keep one recording owner; do not save the same workout independently on both devices.

## Wear: local and bridged Live Updates

The [Wear Live Updates guide](https://developer.android.com/training/wearables/notifications/live-updates) documents support from Wear OS 7. Local watch updates use the notification APIs in [Android Live Updates](android-live-updates.md). Phone-to-watch bridging is OEM-controlled, not guaranteed. **MetricStyle is not supported on Wear: the notification can appear without its metrics.** Put the essential answer in supported text/time fields and verify it on the watch. Use system time rendering rather than per-second pushes; frequent changes can cause data to be hidden to conserve battery.

For a targeted older Wear implementation, follow the [Ongoing Activity API](https://developer.android.com/training/wearables/notifications/ongoing-activity): build an ongoing notification, create `OngoingActivity.Builder(context, notificationId, notificationBuilder)`, configure its status/touch intent and icons, call `apply(context)` before posting, and update/end it with the task. This is a deliberately supported route, not an automatic additional duplicate. A notification does not itself grant background execution.

If the watch app posts locally, explicitly manage phone notification bridging to avoid duplicate task alerts. Check `NotificationCompat.Builder.setLocalOnly`, `NotificationCompat.WearableExtender.setDismissalId`, and the platform bridging controls for the chosen ownership model; do not disable unrelated alerts.

## Wear: Tiles versus Wear Widgets

**Tiles:** Use `androidx.wear.tiles.TileService` and ProtoLayout, not ordinary Compose UI. Implement `onTileRequest` and the resource callback required by the installed Tiles version. Register the service with `com.google.android.wearable.permission.BIND_TILE_PROVIDER`, its binding action, and preview metadata. Return a timeline from cached state, and use `TileService.getUpdater(context).requestUpdate(MyTileService::class.java)` after meaningful data changes. The tile is not a scrolling app or a place for a long network job. See [Tiles](https://developer.android.com/training/wearables/tiles) and [TileUpdateRequester](https://developer.android.com/reference/androidx/wear/tiles/TileUpdateRequester).

**Wear Widgets:** The newer route uses `GlanceWearWidgetService`, `@AssociateWithGlanceWearWidget`, `GlanceWearWidget.provideWidgetData`, `WearWidgetDocument`, and Remote Compose. It is separate from phone `GlanceAppWidget`. Follow the [current setup guide](https://developer.android.com/training/wearables/widgets/get_started) for SDK/renderer prerequisites and release status; the checked guide uses alpha libraries, so do not silently migrate a stable Tile.

Provide `<wearwidget-provider>` XML with supported containers/previews and the `androidx.glance.wear.widget.provider` metadata. Register the documented service binding action and permission. Existing Tiles require the migration-specific registration, rather than blindly copying the new-app manifest. Supply the document background to the host, adapt to `WearWidgetParams`, and request changed content with `triggerUpdateAll(context)` or the supported instance update. Test small, large, and supported full-screen presentation; renderer availability is a separate check from the OS version.

## Wear: complication data sources

Implement `ComplicationDataSourceService` or its coroutine variant. Register the service's update action, `com.google.android.wearable.permission.BIND_COMPLICATION_PROVIDER`, supported types, and appropriate update metadata. Handle `onComplicationRequest`, return the requested type through the listener, and implement `getPreviewData` for the chooser. Use `ShortTextComplicationData`, `RangedValueComplicationData`, or another appropriate supported type with an accessible content description and task-bound tap intent.

Request data changes with `ComplicationDataSourceUpdateRequester`; respect scheduling and validity instead of inventing a per-second background refresh. On sign-out, send the designed no-data state rather than preserving private cached data. A complication data source is not a watch face; creating a face uses a separate contract. See [ComplicationDataSourceService](https://developer.android.com/reference/androidx/wear/watchface/complications/datasource/ComplicationDataSourceService).

## Wear: exercise and synchronization

Use `HealthServices.getClient(context).exerciseClient`. Check `getCapabilitiesAsync()` before choosing exercise type/data types. Obtain target-specific health/activity/location permissions and configure the appropriate foreground-service execution when required. Build `ExerciseConfig`, register an `ExerciseUpdateCallback`, call `startExerciseAsync`, and handle actual updates, pause/resume, and errors. On stop, use `endExerciseAsync`, retain the final data, and release callbacks. Reconcile with `getCurrentExerciseInfoAsync()` after reconnect/relaunch. Ending collection does not automatically save a workout to the app's backend. See [ExerciseClient](https://developer.android.com/reference/androidx/health/services/client/ExerciseClient).

Use [Data Layer](https://developer.android.com/training/wearables/data/overview) for actual phone-watch communication, not as a mandatory proxy for every network request. Verify Google Play services availability and matching package/signing identity. `Wearable.getDataClient()` handles synchronized data items; `getMessageClient()` sends messages to reachable nodes; `getCapabilityClient()` discovers an app capability. Use `WearableListenerService` or lifecycle-managed listeners as appropriate. A successful transport call is not a confirmed remote command: correlate application acknowledgements and task revisions. Persist offline work only when the product supports it. Independent watches use the authorized backend directly for their own account data.

## Device verification

Use the shared [interaction contract](interaction-contract.md), plus these wrist-specific checks:

- Phone app absent, phone off, Bluetooth off, watch online independently, and both devices offline.
- Mirrored versus locally posted cards; no duplicate alerts and no missing core meaning when metrics/actions are omitted.
- Widget/Tile/complication added, removed, reconfigured, and restored; multiple instances open the correct object.
- Round/small screens, large text, VoiceOver/TalkBack, rotary/crown input, dimmed/AOD rendering, and touch targets during movement.
- Revoked sensor permissions, another workout active, session recovery, and save failure; UI must reflect the actual recording state.
- Delayed command reply, repeated tap, switching accounts/watches, and terminal events arriving before old progress.

Other watch ecosystems require their own SDK and app type. For example, Garmin Connect IQ is not Wear OS; do not transplant Android services or Apple entitlements. Resolve that platform's official contract before implementation. Report emulator/layout evidence separately from physical sensor, power, bridging, and offline evidence.
