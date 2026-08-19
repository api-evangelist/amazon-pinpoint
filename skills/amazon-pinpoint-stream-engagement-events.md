---
name: Record and stream Amazon Pinpoint engagement events
description: Write engagement events into Pinpoint and route the resulting event stream to Kinesis or Firehose so the data survives the service retirement.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, asyncapi/amazon-pinpoint-events.yml, arazzo/amazon-pinpoint-configure-event-stream-workflow.yml
operations:
  - PutEvents
  - PutEventStream
  - GetEventStream
  - DeleteEventStream
  - CreateExportJob
  - GetExportJob
---

# Record and stream engagement events

Pinpoint's event surface has two halves: `PutEvents` writes events in, and the event stream pushes everything out to Kinesis. There are no inbound webhooks to subscribe to — the only push-to-you surface is the custom-channel webhook, which is a campaign delivery mechanism, not an event feed.

## Steps

1. **Point the stream at a destination first.** `PutEventStream` — `POST /v1/apps/{application-id}/eventstream` with a `WriteEventStream` carrying `DestinationStreamArn` (a Kinesis Data Stream or Data Firehose delivery stream in the **same region**) and `RoleArn` (an IAM role Pinpoint can assume to write to it). Do this before you generate volume, or the events are not retained anywhere you can read.
2. **Confirm.** `GetEventStream` returns the configured `DestinationStreamArn`, `RoleArn`, `LastModifiedDate` and `LastUpdatedBy`. An application has **at most one** event stream.
3. **Write events.** `PutEvents` — `POST /v1/apps/{application-id}/events` with an `EventsRequest` mapping endpoint id → `EventsBatch` → `Events` map. Each event carries `EventType`, `Timestamp`, optional `Attributes`, `Metrics`, `Session` and `AppPackageName`/`AppVersionCode`. The response is an `EventsResponse` with a per-event `EventItemResponse` (`StatusCode`, `Message`) — **read it, a 200 does not mean every event was accepted.**
4. **Consume downstream.** Records arrive as JSON with the envelope `event_type`, `event_timestamp`, `arrival_timestamp`, `event_version`, `application`, `client`, `device`, `session`, `attributes`, `metrics`. Event families and their types are catalogued in `asyncapi/amazon-pinpoint-events.yml` — `_session.start` / `_session.stop`, `_campaign.send`, `_email.send` / `_email.delivered` / `_email.hardbounce` / `_email.complaint` / `_email.open` / `_email.click` / `_email.unsubscribe`, `_SMS.SUCCESS` / `_SMS.BUFFERED` / `_SMS.FAILURE` / `_SMS.OPTOUT`.
5. **Export the endpoints too.** `CreateExportJob` writes endpoint definitions to S3; poll `GetExportJob` until `JobStatus` is `COMPLETED`. This is the mechanism AWS's own migration guide uses to get data out of Pinpoint.
6. **Tear down** with `DeleteEventStream` when you cut over.

## Rules

- `PutEvents` is quota'd at **15 rps** — one of the tightest in the API, and easy to trip when replaying history. Batch aggressively: one call carries many endpoints and many events per endpoint.
- **Transactional push notifications and voice messages are never streamed.** AWS states this explicitly. If you need delivery telemetry for those, it is not here.
- Event stream records are not deduplicated and `PutEvents` is not idempotent. Replaying a batch double-counts.
- The destination stream must be in the same region as the Pinpoint application, and the `RoleArn` must trust `pinpoint.amazonaws.com` — a bad trust policy surfaces as a 400 on `PutEventStream`, not as a silent drop.
- **Migration note.** The event stream is the retirement escape hatch. AWS recommends Amazon Kinesis directly for event collection after 2026-10-30; getting `PutEventStream` configured now means the data is already landing somewhere you control when Pinpoint goes away.
