# Email implementation and delivery

## Brief and copy

Identify the triggering event, recipient relationship, sender, primary action, destination, language, expiry, and supported clients from the project. A receipt, an OTP, a reservation change, and a promotional offer need different priorities. Reuse approved copy and factual policies. Do not manufacture pricing, deadlines, discounts, consent, or personalization data.

Make the subject identifiable without leaking a sensitive secret onto a lock screen. Use a preheader that adds context rather than repeating the subject or exposing hidden layout text. One primary task can have a clear button plus a readable alternate link when that suits the task. Give amounts, dates, time zones, booking references, and state changes unambiguous labels. Avoid false urgency and decorative text that buries the useful content.

## Template mechanics

- Prefer existing email components and a supported compiler. Edit the source and run its renderer/inliner instead of hand-editing generated output.
- Use semantic headings, paragraphs, meaningful link text, text alternatives, and a logical reading order. Give decorative images empty alt text. Mark layout tables `role="presentation"` without stripping semantics from genuine data tables.
- Use robust email layouts suitable for the target clients. Inline critical styles when the pipeline requires it. Fluid widths, sensible maximum content width, readable text, and touch-friendly links should work before optional media queries. Use client-specific conditional markup only where the declared clients need it.
- Keep important content in text, including codes, prices, and deadlines. Never rely on a background image, animation, custom font, icon, or color alone to convey it. Preserve contrast, long translations, large text, RTL where supported, image-blocked mode, and dark-mode legibility.
- Produce a genuine `text/plain` alternative through the project's MIME/provider API. An HTML `<pre>` is not a plain-text MIME part. Keep essential content and links consistent across alternatives. Configure UTF-8 and language appropriately.
- Use absolute destinations and hosted image URLs from confirmed project configuration. Supply intrinsic dimensions for assets where applicable. Check actual URLs, response content, image availability, and any link rewriting performed by the provider.
- Escape dynamic text and attribute values. Allowlist application-owned action origins and permitted URL schemes. Keep untrusted data out of raw markup, script-like content, and provider template expressions.

Do not insert ordinary browser JavaScript or expect browser form behavior in emails. JSON-LD email markup is a conditional structured-data mechanism, not permission to run scripts. AMP for Email is a separate integration requiring an actual product need, eligibility, a maintained implementation, and ordinary HTML/plain-text alternatives.

## Sending path

Inspect how the application selects recipients, builds content, invokes the provider, records message IDs, and handles asynchronous events. Reuse the current provider rather than adding a new service by default. Distinguish queued, accepted, delivered to the receiving server, bounced, complained, and read. Open pixels and click tracking can be blocked, proxied, or triggered by scanners, so do not use them as proof a human read or intentionally activated something.

For event-triggered mail, use the project's idempotency key or outbox to avoid duplicate sends during retries. Bound retryable failures, distinguish permanent failures, and ensure time-sensitive mail is still valid when sent. Do not swallow a provider error or claim success from a failed request. Keep keys server-side. Redact secrets, message bodies, and unnecessary recipient data in diagnostic output.

Authentication and reputation live outside the template. Check the actual provider's SPF, DKIM, DMARC alignment, TLS, sending-domain, bounce, and complaint configuration when in scope. Gmail's requirements vary by sender volume and message category. Marketing/subscription mail may require both a visible unsubscribe path and standards-compliant one-click unsubscribe headers. Do not implement those headers as a GET action that merely changes state, or assume a visible footer link satisfies them. Check the provider and current requirements before changing sending behavior. Do not add promotional unsubscribe mechanics indiscriminately to account-security mail.

Primary reference: [Gmail email sender guidelines](https://support.google.com/mail/answer/81126?hl=en). Verify the current rules for the actual receiving services and sending provider rather than generalizing one provider's policy.

## Verification

Render with realistic short/long values, missing optional fields, long translated names, URLs, amounts, and dates. Inspect desktop and narrow views. Validate the output HTML, plain-text parity, accessible names, text contrast, image-blocked mode, and actual asset/link responses. Check dark mode and the target clients in a real mailbox or client-testing tool when available. State the evidence boundary if only a browser preview was possible.

For OTP and account actions, use isolated non-production examples for screenshots and previews. Verify expiry, repeat requests, replay rejection, correct account/action binding, and network failures with the application owner. Mail-security scanners can prefetch links, so loading a URL must not silently perform a sensitive action or consume the only token. A meaningful confirmation and server-side policy must handle the actual operation.

Return the editable template changes, rendered output when useful, provider/configuration dependencies, and concise verification results. Keep scratch previews and fixtures outside the repository unless they are requested deliverables. Do not add a generic report or a new delivery pipeline to a simple template edit.
