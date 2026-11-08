# Gmail structured experiences

## Select the actual mechanism

Gmail Copy code recognition, Summary Cards, Email Markup/Highlights, Promotions annotations, AMP for Email, and Gmail API are separate systems. Do not treat a summary visible in Gmail as an object your app can fetch, or a recognized OTP as a supported Schema.org type.

[Summary Cards](https://support.google.com/mail/answer/15195630?hl=en) extract useful information about orders, events, travel, bills, and promotions. Their availability depends on client support, settings, content, and current rollout. Sender data must be complete and consistent, but an ordinary email cannot command an arbitrary card layout or guarantee a summary. Smart-feature settings can be disabled, including regional defaults.

[Email Markup](https://developers.google.com/workspace/gmail/markup/overview) uses supported Schema.org entities and actions, including orders, parcel delivery, reservations, and certain quick actions. JSON-LD and Microdata are supported formats, not an unrestricted execution environment. Promotions annotations have their own supported data and placement. AMP is another separate runtime and eligibility process.

## Implementation sequence

1. Identify the supported entity or action that actually matches the product event. Consult its current official reference rather than building a schema from a guessed name. Avoid adding structured data when there is no corresponding supported feature.
2. Map properties from the same source data used for visible content. Make names, dates, time zones, currencies, order IDs, reservation details, destinations, and current state agree. Do not hide different promises in machine-readable data.
3. Generate structured data through a serializer. Prevent embedded values from terminating the container, for example a `</script>` sequence in JSON-LD. Escape HTML attribute values in Microdata. Validate the rendered MIME/HTML rather than only the source template.
4. Validate the actual supported schema and use Google's documented testing tools. Self-addressed tests and valid syntax are useful, but do not prove public sender eligibility or that every recipient sees the feature.
5. Check [registration with Google](https://developers.google.com/workspace/gmail/markup/registering-with-google), authentication, sender quality, and the action-specific requirements before a launch. Do not submit a registration or send review email without the relevant user instruction. Registration for email markup is different from verification of a Gmail OAuth app.
6. Preserve a useful ordinary HTML/plain-text message if a client ignores structured enhancements. Treat this as the required email design, not a claim that all clients support the special interface.

For secure actions, verify the action's current authentication and request-verification requirements. Keep human confirmation and server-side protection appropriate to the operation. Do not invent a one-click action to approve a payment, share a secret, or confirm a login solely because Gmail supports some actions.

## Evidence to report

Separate: valid data model, rendered markup, authenticated sender, registered/approved use, actual client rendering, and successful authorized action. Do not infer later stages from earlier ones. The Gmail API `Message` payload and `snippet` do not document a ready-made Summary Card/OTP extraction endpoint. If a new capability is proposed, verify current primary documentation and identify the supported contract before coding it.
