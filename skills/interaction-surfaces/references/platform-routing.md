# Resolve platform and access before implementation

Use this only when the surface family is unclear. It is not a compatibility database. Inspect native targets, browser capabilities, SDK/dependency versions, runtime, region, and audience. Documentation, app eligibility, device support, and observed rendering are separate claims.

## Route by the actual job

| Situation | Read next | Resolve first |
| --- | --- | --- |
| Native Apple ongoing task / Dynamic Island | [Apple Live Activities](apple-live-activities.md) | ActivityKit target, supported presentations, local versus APNs updates |
| Android ongoing task / capsule | [Android Live Updates](android-live-updates.md) | Task eligibility, notification permission/channel, promotion, OEM behavior |
| Rear panel, cover screen, external display, LED matrix | [Secondary displays](secondary-displays.md) | Ordinary app, Presentation, activity launch, Window Area, OEM SDK, or system-only template |
| Apple Watch / Wear OS glance, workout, or independent task | [Watches](watches.md) | Mirrored versus local ownership, watch target, sensor access, transport, and offline behavior |
| User-configured recurring glance or short command | [Useful widgets](widgets.md) | Real recurring job, widget instance/configuration, provider/host, truthful data, and supported actions |
| Widget, command, media, Wallet, PWA, in-app message | [Surface recipes](surface-recipes.md) | Native/browser boundary, ownership, update and action contract |
| Stale data, duplicate actions, lifecycle or cross-device inconsistency | [Interaction contract](interaction-contract.md) | Domain truth versus freshness, visibility, and command outcome |

A website can expose task status, permitted Web Push and browser capabilities, and links to issued passes. A PWA or native wrapper does not automatically supply ActivityKit, native styles, watch targets, or vendor hardware access. For iOS/iPadOS web push, check [WebKit's Home Screen web-app guidance](https://webkit.org/blog/13878/). Do not quietly introduce a new native target.

## OEM starting points, not guarantees

| Family | Investigate | Evidence that is insufficient |
| --- | --- | --- |
| Samsung | Now Bar / Live notifications, app settings and exact One UI build | A system app appearing in Now Bar |
| Xiaomi / Redmi / POCO | Super Island / HyperIsland program and market eligibility | Chinese enrollment assumed to cover Global/EEA or rear-screen drawing |
| OPPO | Fluid Cloud templates and approved transports | A partner push channel assumed open to every app |
| OnePlus / realme | Exact Fluid Cloud / Live Alerts / Mini Capsule implementation | Shared branding assumed to mean a shared SDK |
| vivo / iQOO | Atomic Notifications / Origin Island scenario and transport | One provider's quota assumed universal |
| HONOR | Magic Live / YOYO scenario admission and approved templates | Capsule hardware assumed to allow arbitrary UI |
| Huawei | EMUI versus native HarmonyOS / Live View Kit | HarmonyOS NEXT treated as another Android library |
| Nothing | Model-specific Glyph Interface versus Glyph Matrix kit | Public kit assumed to allow unrestricted background execution |
| ASUS, DOOGEE, Ulefone and other hardware | Exact display mode, developer contract, or ordinary app behavior | User animation editor assumed to be a developer SDK |

Do not install multiple OEM SDKs just to complete a brand checklist. Require a useful scenario, verified access, supported distribution, and meaningful target audience. If access is not documented, label it unverified; do not claim either universal support or impossibility. Complete independent authorized work while identifying the missing prerequisite.
