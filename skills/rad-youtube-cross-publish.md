---
name: Cross-publish between Rad TV and YouTube
description: Import YouTube videos into Rad TV or publish Rad content to a connected YouTube channel, individually or in batch.
api: graphql/rad-api.graphql
operations: [verifyConnection, createContentFromYouTube, importYouTubeAndPublish, importYouTubeLibrary, publishContentToYouTube, youtubePublishJob]
---

# Cross-publish between Rad TV and YouTube

Rad TV supports bidirectional YouTube sync over the GraphQL API (`POST https://api.rad.live/graphql`).
Requires a connected YouTube channel (YouTube OAuth) and `Authorization: Bearer <API_KEY>`.

## Import YouTube → Rad
- One video: `createContentFromYouTube(videoId, channel)` imports a YouTube video as Rad content;
  or `importYouTubeAndPublish(videoId, channel, publish)` imports and publishes in one call.
- Batch: `importYouTubeLibrary(maxResults, publishedAfter, autoPublish, channel)` pulls a library slice.
- List source videos first with the `youtubeVideos` query (`pageToken`, `limit`).

## Publish Rad → YouTube
- `publishContentToYouTube(id, privacyStatus)` publishes a Rad content DID to the connected channel.
- Track the async job with the `youtubePublishJob(id)` query until it reaches a terminal status.

## Rules
- `channel` and content `id` are DIDs (`did:rad.live:channel/<id>`, `did:rad.live:content/feature/<id>`).
- Verify auth + connected channel with `verifyConnection` before importing.
- Handle `SERVICE_UNAVAILABLE` (YouTube dependency) with retry; `UNAUTHORIZED` means the YouTube
  connection or subscription lacks permission.
