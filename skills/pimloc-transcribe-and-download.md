---
name: Transcribe audio and download a Secure Redact transcript
description: Upload media with audio transcription enabled, then download the transcript once processing reaches the detected state.
api: openapi/secureredact-openapi.yml
operations: [fetchToken, uploadMedia, fetchMediaInfo, downloadMediaTranscript]
---

# Transcribe audio and download a Secure Redact transcript

Use this flow to get a text transcript of the audio in a media item.

## Steps

1. **Get a token.** Call `fetchToken` (`GET /api/v3/token`) with HTTP Basic auth
   (`client_id` / `client_secret`); use the returned JWT as a bearer token.
2. **Upload with transcription on.** Call `uploadMedia` (`POST /api/v3/media`)
   with `transcribe_audio=true` and, optionally, `transcribe_language` (an ISO
   639 code to transcribe from into English).
3. **Wait for detected.** Poll `fetchMediaInfo` (`GET /api/v3/media/{media_id}`)
   until `status` is at least `detected`.
4. **Download the transcript.** Call `downloadMediaTranscript`
   (`GET /api/v3/media/{media_id}/transcript/download`) with `redact_type` and
   `file_type` query parameters.

## Rules

- The transcript is only available if `transcribe_audio` was `true` on upload and
  the media state is at least `detected`.
- Use the user access token for media that belongs to an Enterprise user.
- Errors are `{ "error": "<message>" }`.
