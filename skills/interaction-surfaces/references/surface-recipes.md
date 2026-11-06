# Other surfaces: implementation entry points

Read only rows relevant to the assigned flow. Select a documented API available in the actual project; check current versions, permissions, templates, and policies. These are implementation routes, not instructions to add every integration. Reuse existing domain commands/data and the verification contract.

## Apple

| Surface / job | Concrete implementation route | Crucial implementation check |
| --- | --- | --- |
| Recurring widget or watch complication | WidgetKit configuration, supported families, timeline provider; App Intents for configuration/actions | Use a real snapshot/cache and a timeline fitting freshness; a requested reload is not a guaranteed timer |
| Control Center / Lock Screen Control | Supported WidgetKit `ControlWidget`, `ControlWidgetButton` or `ControlWidgetToggle`, appropriate App Intent/value provider | Model pending versus confirmed state; guard availability and authentication; do not assume all targets support Controls |
| Siri / Shortcuts / system action | App Intents and `AppShortcutsProvider`; model parameters/entities, useful titles, and `perform()` | Resolve the intended object, preserve user confirmation where needed, and return the actual result |
| Ordinary local/remote notification | UserNotifications, categories/actions, notification service extension only if needed | Check authorization, deep link, action context, foreground presentation, redaction, and expiry; do not mix this token with ActivityKit tokens |
| Audio / video | AVFoundation, Now Playing info, `MPRemoteCommandCenter`; AVKit Picture in Picture when appropriate | Keep play/pause/seek state synchronized with the real player; handle interruptions and audio routes |
| Calls | Appropriate CallKit/communications integration and VoIP transport only for the allowed use case | Incoming-call UI is not a generic urgent notification mechanism |
| Ticket / loyalty pass | PassKit pass format and signed `.pkpass`, issuer backend, pass update registration when needed | Certificate/team identity, valid barcode, expiry/revocation, account ownership; key/payment/ID products have separate eligibility |
| Lightweight arrival task | App Clip target and invocation setup when warranted | Finish the small task; do not quietly add a separate native target to a website project |
| Cross-device continuation | `NSUserActivity`/Handoff or supported universal links with restorable object context | Authorize on the receiving device; don't transmit secrets as public URL parameters |
| Standalone workout/watch task | watchOS target; HealthKit workout session/builder and relevant permissions; WatchConnectivity for appropriate communication | Phone independence, sensor ownership, session recovery, offline recording, and later sync are separate requirements |

Start at [WidgetKit](https://developer.apple.com/documentation/widgetkit), [App Intents](https://developer.apple.com/documentation/appintents), and [PassKit](https://developer.apple.com/documentation/passkit); follow the exact type documentation for the installed SDK. For Live Activities use `apple-live-activities.md`. For watch targets, complications, workout lifecycle, and WatchConnectivity, follow [the watch implementation guide](watches.md).

## Android / Wear OS

| Surface / job | Concrete implementation route | Crucial implementation check |
| --- | --- | --- |
| Home-screen widget | `AppWidgetProvider`/RemoteViews or Jetpack Glance `GlanceAppWidget`, receiver/provider metadata, sizing and update hooks | Glance's supported components differ from Compose; persist state, update after actions, test resizing and launcher variants |
| Quick Settings command | `TileService`, tile metadata, `onStartListening`, `onClick`, state update | User must add the tile; no perpetual work while not listening; locked-state action/authentication behavior |
| Launcher action | ShortcutManager/ShortcutManagerCompat and explicit deep links | Static/dynamic/pinned shortcuts have different lifecycles; remove stale account-specific entries |
| Conversation / reply | MessagingStyle, appropriate conversation shortcut/Person metadata, `RemoteInput`, eligible bubbles | Reply authorization, send failure, actual conversation identity; no fake conversation for generic app promotion |
| Audio / media | Media3 session/service and player integration | Controls track playback, not independently stored UI flags; audio focus, headphones, interruption, foreground-service rules |
| Video / call PiP | `PictureInPictureParams` with appropriate aspect/actions and lifecycle; proper call APIs separately | Preserve active session; hide irrelevant UI; test enter/exit/resize and privacy |
| Wallet pass | Google Wallet API for permitted pass classes/objects and signed save flow | Issuer setup, user/account mapping, object update and real scan validation; Wallet is not an arbitrary widget |
| Watch glance / quick task | Supported Wear Widgets or Tiles, ComplicationDataSourceService for watch-face data, watch app for deeper interaction | Renderer/library availability, data freshness, tap behavior, distinct autonomous versus phone-dependent operation |
| Watch workout | Health Services ExerciseClient and appropriate watch execution/permissions | Record on the intended device, survive disconnection as designed, and reconcile session/data ownership |
| Phone-watch sync | Wearable Data Layer for supported paired-device app communication, backend network for true independent operation | Node availability, package/signing requirements, eventual delivery; no guaranteed immediate message when disconnected |

Use [Glance](https://developer.android.com/develop/ui/compose/glance), [Quick Settings](https://developer.android.com/develop/ui/views/quicksettings-tiles), [Wear OS surfaces](https://developer.android.com/training/wearables/user-interfaces), and [Google Wallet](https://developers.google.com/wallet). For live notifications/OEM islands use `android-live-updates.md`; for secondary displays use `secondary-displays.md`. Follow [the watch implementation guide](watches.md) for concrete Wear services, Live Updates, Health Services, and Data Layer integration.

## Web, support widgets, and external channels

- **Inline feedback:** Bind errors to fields; preserve input; choose focus after submission. Use an appropriate live region for new results instead of moving focus after every update.
- **Toast/banner/dialog:** Decide whether the information needs persistent recovery or a blocking decision. Verify keyboard, focus restoration, dismissal, reduced motion, and independent scroll areas. Reuse the site's components; do not stack multiple announcement systems.
- **Support/AI widget:** Preserve context and history, avoid covering primary actions/keyboard, separate advice from proposed and executed actions, and connect escalation only to an existing real channel. Do not fabricate an available human agent.
- **Web Push:** Use a service worker, Push API subscription, Notification permission, and the authorized server transport. Request permission after a meaningful user action; handle subscription changes/revocation and notification clicks into the exact task. On iOS/iPadOS, verify applicable Home Screen web-app rules. Delivery is not guaranteed and push does not grant native Live Activities.
- **PWA shortcut/share/badge:** Use supported manifest shortcuts, Web Share, Badging, or installation capabilities only after feature detection. Keep the core task usable as a web page; don't claim OS-global control from browser APIs.
- **Email/SMS/messenger:** Treat each as an explicit communication channel with consent, preferences, identity mapping, cost, rate limits, expiry, deduplication, and real delivery states. Building an adapter does not authorize sending messages.
- **Web multi-screen:** Only consider the Window Management API for an actual window-placement job on exposed screens. It is separate from responsive CSS and does not unlock a phone's vendor-owned rear panel.

## Physical and less common surfaces

For Garmin, use the appropriate Connect IQ type (data field, widget, app, watch face, audio provider) and device capability declarations; do not translate Wear OS APIs by analogy. For car displays, choose the permitted app category/template or supported passive presentation before designing interaction. Native CarPlay/Android Auto access is separate from a Live Activity appearing on a car dashboard.

For BLE, NFC, UWB, haptics, lights, rings, and glasses: identify the actual device/SDK, discovery or pairing path, permissions, command/data contract, foreground/background rules, disconnection, power budget, and observation method. Sensor access does not mean UI control. Provide a learnable signal with a visible/textual equivalent for critical information. A proximity signal is not identity or authorization. Inspect the real hardware before claiming reach or reliability; use `secondary-displays.md` for Glyph and rear-screen SDKs.
