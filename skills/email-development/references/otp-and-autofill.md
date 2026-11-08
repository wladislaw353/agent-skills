# Verification emails and OTP autofill

## Separate the responsibilities

A client recognizing a code, copying it, suggesting it in a keyboard, filling a field, and a server verifying an action are different events. OTP can mean address confirmation, passwordless sign-in, a second factor, or transaction confirmation. Identify the purpose before choosing content, APIs, or measurements.

For an OTP email, make the service and requested action clear. Show one prominent code in selectable text, preserve leading zeros, include the real validity period, and explain an unsolicited request. Avoid adjacent lookalike order numbers, marketing clutter, and a code only in an image. Keep the HTML, plain-text part, subject, preheader, locale, and server policy consistent. Do not copy a secret into the subject or preview by default.

Example content structure, with placeholders resolved from real application data:

```text
Subject: Your sign-in code for {{service_name}}
Preview: Complete the sign-in you requested.

{{code}}

Use this code to sign in at {{verified_domain}}.
It expires {{expiry_description}}. Do not share it with anyone.
If you did not request this, do not approve the sign-in.
```

This is a content outline, not a provider template language or an instruction to include every placeholder literally. A short code's expiry, entropy, attempt limits, and resend rules are application policy, not email formatting choices.

## Gmail observations versus public APIs

Reports in September 2026 describe a mobile Gmail Copy code shortcut, sometimes in the inbox. Treat recognition as client-controlled and rollout-dependent. Clear content is helpful but no documented email attribute guarantees the shortcut. Do not label an illustrative mockup as a real Gmail screenshot. Distinguish that observation from published API contracts and recheck the current state before making availability claims.

The documented [Gmail Message resource](https://developers.google.com/workspace/gmail/api/reference/rest/v1/users.messages) exposes mail data. `snippet` is a short text excerpt, not a structured OTP result. Do not invent a Gmail API method for retrieving the client-recognized code or Summary Card. Reading a mailbox with OAuth is a separate sensitive-data product, not a prerequisite for improving your own verification email. Check [Gmail scopes](https://developers.google.com/workspace/gmail/api/auth/scopes) before proposing access. Whole-mailbox `gmail.readonly` is a restricted scope. Add-on current-message access has different conditions, so do not conflate every mail scope.

## The receiving website

When the assignment includes the verification form, use one accessible text input with an actual label, `autocomplete="one-time-code"`, and `inputmode="numeric"` only for numeric codes. `type="number"` is unsuitable for a code identifier. Match pattern/length to the service contract, permit paste, support whole-code insertion, and expose errors without jumping focus. A visual row of cells must not break the underlying editing/autofill behavior.

[WebOTP](https://developer.chrome.com/docs/identity/web-apis/web-otp) receives specially formatted, origin-bound **SMS**, not email. Detect support using `OTPCredential`, run in the required secure context, start listening before requesting the expected SMS, and use an `AbortController`. Abort on manual entry, submission, leaving, component unmount, or a bounded wait. Do not overwrite a typed code with a late result. The UI wait timeout and server OTP expiry are separate. Keep manual entry available for unsupported browsers, denied permission, or no matching message. Do not silently auto-submit an ambiguous or sensitive action.

The ordinary top-level SMS binding format is:

```text
Your Example sign-in code: 004291

@example.com #004291
```

The exact domain, code, and SMS format must match the actual service. This is not an email markup standard. Embedded frames and cross-device browser flows have additional conditions in current documentation. Safari's system AutoFill support does not imply support for the same WebOTP JavaScript API.

## Email-specific AutoFill on Apple

Apple documented Mail-to-Safari one-time verification-code AutoFill in its [iOS 17 feature guide](https://www.apple.com/mideast/ios/ios-17/a/pdf/iOS_All_New_Features_en-KW.pdf), page 6. An account configured in Apple Mail is a different path from using the Gmail app, even if both access the same mailbox. Distinguish this established email flow from iOS 26 notification-based AutoFill. Neither gives the receiving website general mailbox access. Verify the actual mail client, account configuration, destination field, settings, and supported OS rather than promising recognition for every email.

## Native receiving apps

- **Apple:** use `UITextContentType.oneTimeCode` or SwiftUI `.textContentType(.oneTimeCode)`. Add `.keyboardType(.numberPad)` only for numeric codes. Apple documents [domain-bound SMS AutoFill](https://developer.apple.com/documentation/security/enabling-autofill-for-domain-bound-sms-codes). App/domain association and platform behavior require actual configuration and testing. The [iOS 26 feature list](https://www.apple.com/os/pdf/All_New_Features_iOS_26_Sept_2025.pdf) documents verification-code AutoFill from app notifications. This does not grant an app general access to Gmail, other inboxes, or other apps' notification content. Check settings and actual notification availability. Distinguish SMS/email delivery from TOTP generated by a password manager.
- **Android with Google Play services and editable SMS:** use [SMS Retriever](https://developers.google.com/identity/sms-retriever/overview), including the correct application hash for the package and signing certificate. Check the release/store signing path, receiver registration, protected broadcasts, statuses, timeout, lifecycle, and server verification. `SmsRetriever.getClient(context).startSmsRetriever()` starting successfully does not mean verification succeeded.
- **Android without control of the SMS format:** [SMS User Consent](https://developers.google.com/identity/sms-retriever/user-consent/overview) may let the user approve access to one matching message. `startSmsUserConsent(senderAddress)` is a bounded flow with message/sender eligibility rules, not permission to read an arbitrary history. Its documented code-shape and wait-window rules are not your own code policy. Handle refusal with manual entry.
- **Autofill providers:** [SMS Code Autofill](https://developers.google.com/android/reference/com/google/android/gms/auth/api/phone/SmsCodeAutofillClient) works only for the current user-designated autofill service. Ordinary apps cannot become universal collectors by calling it. Android 15 [redacts detected OTP notifications from untrusted notification listeners](https://developer.android.com/about/versions/15/behavior-changes-all), even when general notification access exists. Do not propose bypassing that protection.

These integrations require the app/site scope to be part of the assigned task. Do not add native applications or a mailbox connector merely to improve an email template.

## Matching, state, and acceptance

Bind the issued code to the actual account, action, attempt, destination, expiry, and replay policy. The server must enforce validity, attempt/rate limits, resend behavior, and atomic consumption. Never accept the most recent six digits from an unrelated message or treat a display name as proof of origin. For an authorized cross-service assistant, verify the target origin/application and expected request, obtain the needed permissions, and decline ambiguous matches rather than guessing.

Do not log codes, tokens, full message bodies, or sensitive field values in analytics or errors. Measure requested, accepted, delivered, suggested, filled, and verified as distinct events without their secrets. Verify leading zeros, paste, old and new codes, simultaneous attempts, wrong account/origin/action, duplicate use, timeouts, permission refusal, manual entry during waiting, offline state, and release-signed native apps. Report real device/client coverage separately from documentation review and simulated tests.
