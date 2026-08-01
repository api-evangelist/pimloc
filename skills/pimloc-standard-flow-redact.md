---
name: Redact a video with Secure Redact (Standard Flow)
description: Fully automated redaction of a video/image — upload by URL, wait for completion, download the redacted media. No UI review.
api: openapi/secureredact-openapi.yml
operations: [fetchToken, uploadMedia, fetchMediaInfo, downloadMedia]
---

# Redact a video with Secure Redact (Standard Flow)

Use this flow to automatically blur faces, license plates, screens, text or full
bodies in a video or image with no manual review.

## Steps

1. **Get a token.** Call `fetchToken` (`GET /api/v3/token`) with HTTP Basic auth
   using your `client_id` and `client_secret` from the Secure Redact account
   page. Read `token` from the response — it is a short-lived JWT.
2. **Upload the media.** Call `uploadMedia` (`POST /api/v3/media`) with the JWT
   as a bearer token. Provide `media_path` (a URL Secure Redact can download
   from, e.g. an S3 presigned URL) and the detection flags you need
   (`detect_faces`, `detect_license_plates`, `detect_screens`, `detect_text`,
   `detect_people`). Supply a `state_callback` URL to receive progress webhooks,
   or poll in the next step. Set `default_redact=true` to redact all detections.
3. **Wait until detected/completed.** Either receive the `state_callback`
   webhook, or poll `fetchMediaInfo` (`GET /api/v3/media/{media_id}`) until
   `status` is `completed`.
4. **Download.** Call `downloadMedia` (`GET /api/v3/media/{media_id}/download`)
   to retrieve the redacted file.

## Rules

- All non-token calls use the JWT bearer token; only `fetchToken` uses Basic auth.
- Errors are `{ "error": "<message>" }`; `403` means invalid/expired auth.
- Accounts are metered in processing minutes — check remaining minutes with
  `fetchAccount`.
