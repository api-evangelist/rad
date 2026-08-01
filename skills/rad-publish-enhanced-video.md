---
name: Publish an AI-enhanced video to Rad TV
description: Upload or ingest a video, transcode it with optional AI upscaling and audio upmixing, and publish it on a Rad TV channel.
api: graphql/rad-api.graphql
operations: [verifyConnection, publishVideo, submitContentForProcessing, waitForProcessing, publishContent]
---

# Publish an AI-enhanced video to Rad TV

Use the Rad TV GraphQL API (`POST https://api.rad.live/graphql`) to publish a video, optionally
enhanced to 4K/8K with AI upscaling and 5.1/7.1 surround audio.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. An active subscription is required;
AI enhancement (`enhance: true`) requires **Creator+**. Confirm access first with the
`verifyConnection` query.

## Fast path (single call)
Call `publishVideo` with a `PublishVideoInput`:
- Provide `sourceUrl` (remote video) OR `filename` + `size` (for a TUS upload session).
- Set `contentType` (e.g. `landscape`), `enhance: true` to trigger upscaling + audio upmixing,
  and `publish: true` to make it public immediately (or set `releaseDate`).
- The response returns `content { id }` (a `did:rad.live:content/feature/<id>` DID) and
  `job { id status }` (starts `QUEUED`).

## Step-by-step path
1. `createContent` → get the content DID.
2. `createContentAsset` → open a TUS upload session; upload the file.
3. `submitContentForProcessing` with `layout` and `enhance` → returns a transcode `job`.
4. Poll `get_processing_status` or block with `waitForProcessing(contentId, timeoutSeconds)`.
5. `publishContent(id, releaseDate)` once processing succeeds.

## Rules
- Resources are DIDs: `did:rad.live:content/feature/<id>`.
- On `TRANSCODE_FAILED` / `UPLOAD_FAILED`, re-submit; on `RATE_LIMIT_EXCEEDED`, back off and retry.
- Query `me { channel { features(first: 1) { edges { node { outputAssets { video { hls download preview } } } } } } }`
  to read HLS/MP4/preview outputs after processing.
