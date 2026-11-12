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

## Generate the email's markup, not website SEO

Use [Google's JSON-LD format](https://developers.google.com/workspace/gmail/markup/reference/formats/json-ld): an actual `<script type="application/ld+json">` in the email HTML. It contains data, not executable JavaScript. In particular, do not copy a site's BlogPosting/Organization graph and assume Gmail supports its features. The documented Person example demonstrates syntax, not a Person inbox action.

Build a typed object from validated domain data, then serialize it once. For JavaScript generators, the embedding primitive can be:

```js
function emailJsonLd(data) {
  const json = JSON.stringify(data);
  if (json === undefined) throw new Error("Missing email structured data");
  const safe = json
    .replace(/</g, "\\u003c")
    .replace(/>/g, "\\u003e")
    .replace(/&/g, "\\u0026")
    .replace(/\u2028/g, "\\u2028")
    .replace(/\u2029/g, "\\u2029");
  return '<script type="application/ld+json">' + safe + '</script>';
}
```

This helper only escapes the HTML embedding boundary; validate required properties, types, finite prices, dates, allowed action origins and recipient authorization separately. Do not HTML-entity-encode the JSON (`&quot;` is not a JSON quotation mark in a raw-text script). Do not concatenate user strings into JSON. Omit absent optional properties; a missing required field is an upstream data error, not permission to invent a value. JSON must have no comments, trailing commas or template placeholders. Resolve raw URLs before serializing; avoid double HTML escaping. Ensure the renderer/inliner/provider preserves the data block.

For [EventReservation](https://developers.google.com/workspace/gmail/markup/reference/event-reservation), map the real reservation number and status, ticket holder, event name/start time and venue/address fields using Google's required-property table. Do not infer a postal address or confirmed status from a tour title. Other supported order/delivery/reservation types have different contracts. Read the chosen type before implementing it. JSON-LD event data does not replace an ICS invitation.

## Promotions: choose annotations deliberately

Read the [Promotions overview](https://developers.google.com/workspace/gmail/promotab/overview), [field reference](https://developers.google.com/workspace/gmail/promotab/reference) and [best practices](https://developers.google.com/workspace/gmail/promotab/best-practices). Use annotations only for an actual promotional message, not to decorate a verification code or transactional security message. They do not force delivery into a tab or guarantee visibility.

- `Organization.logo`: verified, publicly fetchable HTTPS brand image. This is different from the body logo and BIMI.
- `DiscountOffer`: accurate description, real discountCode, and applicable availabilityStarts/availabilityEnds in ISO 8601 with explicit timezone. A promo code is not an OTP. Never invent an offer or extend its expiry in metadata.
- `PromotionCard`: image and destination URL are required; headline, price, priceCurrency, discountValue and position are optional. A discountValue is an amount deducted from price, not a percentage. Check currency and original-versus-final price against the visible offer.
- For carousels, the current guide permits up to ten unique images. Use PNG/JPEG with supported 4:5, 1:1 or 1.91:1 proportions, consistent within a carousel; account for center cropping. Validate actual public image bytes/type, availability and destination. Do not use CID, login-only or short-lived image URLs for annotations.

The outline below is an input shape, not ready-to-send content: replace the uppercase variables with validated campaign data and omit unused objects. Serialize the array through `emailJsonLd` and place it in the HTML head per the Promotions guide.

```js
const annotations = [
  { "@context": "http://schema.org/", "@type": "Organization", logo: BRAND_LOGO_HTTPS },
  {
    "@context": "http://schema.org/", "@type": "DiscountOffer",
    description: OFFER_DESCRIPTION, discountCode: PROMO_CODE,
    availabilityStarts: START_ISO_WITH_OFFSET, availabilityEnds: END_ISO_WITH_OFFSET
  },
  {
    "@context": "http://schema.org/", "@type": "PromotionCard",
    image: PRODUCT_IMAGE_HTTPS, url: OFFER_HTTPS, headline: OFFER_HEADLINE
  }
];
```

Do not merge transactional Actions registration, Promotions eligibility and BIMI setup into one approval process. Check the current requirements of the chosen feature. Google's quality filters and display frequency limits can suppress valid Promotions annotations.

## Structured-data acceptance checks

1. Parse each final JSON-LD block with a JSON parser. Test quotes, ampersands, Unicode and a literal `</script>` in user-controlled data: it must round-trip without creating another HTML element.
2. Validate the chosen Google-supported type and its required fields; generic Schema.org validity is insufficient. Compare values with visible HTML, plain text, attachments and current backend state.
3. Check the actual raw message after provider processing. Use [Google's schema testing procedure](https://developers.google.com/workspace/gmail/markup/testing-your-schema); do not substitute a website rich-results test.
4. Verify authenticated sender/feature eligibility separately. Google documents same-account Gmail self-tests for Email Markup; those do not establish approval for production recipients. Forwarded messages may lose markup, so use a directly sent authorized test.
5. Report syntax, field validation, provider preservation, eligibility and observed client display separately. Never claim all recipients will see a card because JSON parsed successfully.
