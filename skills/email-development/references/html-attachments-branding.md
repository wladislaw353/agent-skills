# HTML compatibility, attachments, and logos

## Declare a client contract

There is no universal email HTML allowlist. Distinguish valid HTML, a mailbox sanitizer's allowed markup, and rendering support. Record target clients and versions: Gmail web/iOS/Android (including account type), Apple Mail, Outlook.com/new Outlook, and classic Windows Outlook are different targets. Do not infer classic Outlook support from a browser screenshot.

Use this conservative baseline; verify exceptions against current client documentation and real received mail:

| Content | Baseline | Conditional or unsuitable as a core dependency |
| --- | --- | --- |
| Document | `html`, `head`, charset/viewport `meta`, `title`, `body` | Clients may rewrite the document/head |
| Text | `h1`–`h6`, `p`, `span`, `div`, `strong`, `em`, `br`, `ul`, `ol`, `li` | Reset margins/spacing; div support does not imply flex/grid support |
| Layout | `table`, `tbody`, `tr`, `td`; presentation role on layout tables | Preserve `th`, captions and associations for actual data tables |
| Actions | `a` with verified absolute HTTPS destination | No forms, inputs, iframe, object, embed, executable script or event handlers in ordinary email |
| Images | `img` with alt text, width/height and tested PNG/JPEG assets | SVG, WebP, AVIF, data URLs, canvas, video/audio, picture/srcset and background images need specific client evidence; never essential-only content |
| Styling | Inline critical typography, spacing, colors; explicit table/image widths | Style blocks/media queries are enhancements. Do not depend on external stylesheets, web fonts, flex/grid, positioning or animation across untested clients |

This is an engineering baseline, not a claim every listed feature renders identically. Use VML/MSO conditional code only when classic Outlook actually needs it, preserving the existing compiler's handling. An animated GIF needs a meaningful first frame. JSON-LD is a narrowly scoped non-executable script exception; AMP requires a separate supported MIME part and eligibility.

### Preflight on generated HTML

- Parse rendered output; inventory tags, attributes and CSS. Compare against the declared baseline; document and test every conditional feature. Reject executable scripts, `on*` handlers, unsafe URL schemes and untrusted raw HTML.
- Inspect final output after inlining and provider transformations. Check valid nesting, escaped data, absolute links, no unresolved template tokens, reasonable total HTML/MIME size, and no clipping of essential content.
- Confirm text and CTA remain usable without images, media queries, remote fonts and optional markup. Check long translations, zoom, keyboard/screen reader order, narrow widths and light/dark mode.
- Test representative received messages in the named clients; distinguish simulated/browser previews from actual compatibility evidence. Do not claim “all clients.”

[Gmail CSS support](https://developers.google.com/workspace/gmail/design/css) is Gmail-specific, not an Outlook specification.

## Attachments and MIME

Use the existing provider's MIME/attachment API. A downloadable file and an inline image are not interchangeable.

- Supply verified bytes, truthful MIME type, safe filename and intended disposition. PDFs use `application/pdf`; PNGs use `image/png`; calendar semantics are in [calendar invitations](calendar-invitations.md). Reject empty/corrupt files and header/path injection. For non-ASCII filenames, use the library's standards-compliant encoding rather than concatenating MIME headers.
- Keep text/plain and text/html alternatives consistent. Use `multipart/related` to associate HTML with CID resources and `multipart/mixed` for independent attachments. Let the mail library build boundaries and transfer encodings. Inspect the resulting `.eml`, not only a template object.
- For CID, reference `cid:<id>` in HTML and match the MIME part's `Content-ID: <id>` exactly (without angle brackets in `src`). Use unique IDs and the correct image type. Test forwarding and reply behavior; “inline” is a hint, not a promise the client hides the attachment.
- Enforce provider and recipient limits using encoded total message size; base64 adds roughly one third before line/header overhead. Do not universalize one provider's attachment limit. Use an authenticated download only when authorized and supported by the product; never silently replace an attachment with a public URL.
- Only attach the current recipient's document. Verify authorization, version, language, amounts and booking reference. Scan untrusted uploads according to project policy. Avoid executable/macro-bearing content. Do not fetch arbitrary attachment URLs or local paths supplied by users.
- In body and plain text, name the attachment and explain its purpose. If generation fails, report failure; do not send “attached” with no file. Do not add unsolicited attachments to OTP messages.

See [multipart/related, RFC 2387](https://www.rfc-editor.org/rfc/rfc2387) and [Content-ID URLs, RFC 2392](https://www.rfc-editor.org/rfc/rfc2392).

## Three distinct logo placements

1. **Inside the email:** reuse the approved brand asset. Prefer a tested PNG/JPEG rendition for broad compatibility. Set display dimensions and meaningful brand alt text; use adequate pixel density and bounded file size. Test a transparent mark against dark-mode transformations; do not assume the website SVG is a universal email asset. Use either the project's stable public HTTPS asset (correct image content type, no cookies/auth, accessible to image proxies) or a correctly packaged CID image. Neither guarantees display with images blocked. Keep the sender/service name in text. Do not silently switch between hosted and CID delivery.
2. **Gmail Promotions:** `Organization.logo` is annotation data with an HTTPS URL, not a CID image or a general mailbox avatar setting. Follow [Gmail structured data](gmail-structured-data.md).
3. **Sender identity/BIMI:** not controlled by `<img>` or arbitrary schema. Check the actual sending domain, DMARC enforcement, required SVG profile, certificate and mailbox-provider requirements. Gmail currently describes VMC or CMC certificates; its checkmark is associated with VMC. Follow [Google's BIMI setup](https://knowledge.workspace.google.com/admin/security/set-up-bimi). DNS/certificate work requires task authorization; a template task does not authorize buying certificates or changing authentication.
