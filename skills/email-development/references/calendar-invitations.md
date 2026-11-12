# Calendar attachments and invitations

## Choose the intended operation first

[RFC 5545](https://www.rfc-editor.org/rfc/rfc5545.html) defines iCalendar data. [RFC 5546](https://www.rfc-editor.org/rfc/rfc5546.html) defines iTIP scheduling operations. [RFC 6047](https://www.rfc-editor.org/info/rfc6047/) binds those operations to email (iMIP). A file named `event.ics` alone does not implement invitation, acceptance, automatic insertion, or cancellation.

| Product intent | Calendar contract |
| --- | --- |
| User imports an event | Standalone iCalendar attachment; no METHOD needed. No RSVP or automatic synchronization promise |
| Organizer publishes event information | iTIP PUBLISH; follow its property constraints, not REQUEST defaults |
| Organizer invites or reschedules | REQUEST with real ORGANIZER and intended ATTENDEE identities |
| Attendee responds | REPLY for the existing UID and attendee, with appropriate PARTSTAT |
| Organizer cancels | CANCEL for the existing UID, appropriate sequence and cancellation state |

Decide using the source booking/event state. Never turn an informational receipt into an RSVP workflow just to obtain calendar buttons. Describe a plain attachment as “Add this event using the attached calendar file,” not “We added it to your calendar.”

## Generate valid event data

Use the project's maintained calendar serializer. Resolve UID, organizer, attendee, timezone, event revision and event times from real data; fail clearly if required data is unavailable.

- Emit VCALENDAR with VERSION:2.0 and a product identifier, containing the intended component. For a VEVENT, retain a stable globally unique UID and UTC DTSTAMP. Supply DTSTART and other properties required by the selected method. Use DTEND or DURATION, not both; DTEND is exclusive. For all-day events use VALUE=DATE, not local midnight disguised as UTC.
- For a fixed instant use a correctly converted UTC value ending in Z, or local time with TZID and a matching VTIMEZONE definition. Do not append Z to local clock time. Avoid floating times unless the product explicitly intends them. Test daylight-saving transitions and recurring-event timezone rules.
- Serialize with CRLF line endings, UTF-8, and line folding at 75 octets as recommended by RFC 5545; continuation starts with space/tab. Fold at UTF-8 boundaries, not character counts. Escape TEXT backslashes, commas, semicolons and newlines. Use the correct parameter/URI encoding, not HTML escaping. Reject CR/LF injection into identifiers/parameters.
- Keep UID across retries, updates and cancellation. Set SEQUENCE according to iTIP revision rules; significant organizer changes and cancellations advance the revision. Do not increase it for transport-only retries. Preserve RECURRENCE-ID for a specific occurrence; do not cancel the entire recurring series by accident.
- In iMIP use mailto URIs for ORGANIZER/ATTENDEE. RSVP=TRUE is an invitation request; PARTSTAT reflects the attendee's real state. Do not mark them accepted because a purchase completed. Include only authorized attendee details and avoid leaking a guest list.

## Package the actual message

For an invitation, the MIME part might declare:

```text
Content-Type: text/calendar; charset=UTF-8; method=REQUEST
Content-Disposition: attachment; filename="event.ics"
```

The calendar must also contain METHOD:REQUEST. For other iMIP operations both method values must agree. A `text/calendar` part without a method is not iMIP. The filename and disposition do not override the media type. Use the provider's calendar API when it supplies the correct MIME and avoid duplicating the same event via both a generic attachment and a special calendar option.

Keep a human-readable alternative with the same title, time, timezone, location and action. Use suitable transfer encoding for non-ASCII content; the mail library should encode the UTF-8 data. Inspect the delivered raw MIME to confirm the provider preserved the calendar type and method. Different event choices are not MIME alternatives of the same invitation.

## Trust and lifecycle checks

A forged calendar reply must not mark a booking confirmed. Verify the real organizer's authority for updates/cancellations and the attendee's identity for replies, plus UID/revision/recurrence matching. RFC 6047 specifies S/MIME authentication; common unsigned provider invitations are not evidence of full compliance with that authentication requirement. SPF/DKIM transport checks alone do not prove calendar organizer authorization. State the integration's actual trust model instead of claiming universal RFC compliance.

Round-trip generated data through an independent parser. Check Unicode, long folded lines, escaped commas/newlines, invalid dates, DST boundaries, all-day exclusive end dates, duplicate delivery, rescheduling, cancellation, reply matching and stale revisions. Exercise Gmail/Google Calendar, Apple Mail/Calendar and relevant Outlook versions using authorized test recipients. A successful download or syntactically valid ICS does not prove RSVP/update UI works. Report parser, MIME and actual client results separately.
