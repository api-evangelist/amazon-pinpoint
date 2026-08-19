---
name: Send a transactional message with Amazon Pinpoint
description: Update or create an endpoint and send a one-off transactional message to an address, an endpoint, or every endpoint belonging to a user.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, arazzo/amazon-pinpoint-update-endpoint-send-message-workflow.yml, arazzo/amazon-pinpoint-message-user-across-endpoints-workflow.yml
operations:
  - UpdateEndpoint
  - UpdateEndpointsBatch
  - GetEndpoint
  - GetUserEndpoints
  - SendMessages
  - SendUsersMessages
---

# Send a transactional message

## Choose the right call

- **`SendMessages`** — you know the address (an email address, an E.164 phone number, a device token) or an endpoint id. Use it for a receipt, an alert, a password reset.
- **`SendUsersMessages`** — you know the person, not the channel. It fans out to every endpoint sharing that `UserId`.

Both are project-scoped: `POST /v1/apps/{application-id}/messages` and `/users-messages`.

## Steps

1. **Make sure the endpoint exists.** `UpdateEndpoint` is an upsert — it creates the endpoint if the id is new and updates it if not. Set `ChannelType` (`EMAIL`, `SMS`, `VOICE`, `CUSTOM`, `APNS`, `GCM`, …), `Address`, `OptOut`, and any `Attributes` / `UserAttributes` / `Metrics` you will segment on later. For bulk loads use `UpdateEndpointsBatch` — but note it is quota'd at **2 rps**, the tightest limit in the API.
2. **Read it back if it matters.** `GetEndpoint` confirms `EndpointStatus` and `OptOut`. Sending to an opted-out endpoint is a wasted call and a compliance problem.
3. **Send.** `SendMessages` with a `MessageRequest` containing either `Addresses` (keyed by address) or `Endpoints` (keyed by endpoint id), plus a `MessageConfiguration` per channel (`EmailMessage`, `SMSMessage`, `APNSMessage`, `GCMMessage`, `VoiceMessage`, `DefaultMessage`). Optionally reference a template with `TemplateConfiguration` instead of inline content.
4. **Read the per-recipient result.** The `MessageResponse` carries `RequestId` plus a `Result` / `EndpointResult` map. Each entry has `DeliveryStatus` (`SUCCESSFUL`, `THROTTLED`, `TEMPORARY_FAILURE`, `PERMANENT_FAILURE`, `UNKNOWN_FAILURE`, `OPT_OUT`, `DUPLICATE`), a `StatusCode`, a `StatusMessage` and a `MessageId`. **A 200 on the send does not mean every recipient succeeded** — you must read the map.
5. **For user fan-out**, `GetUserEndpoints` first if you want to know how many endpoints will be hit, then `SendUsersMessages` with a `SendUsersMessageRequest`.

## Rules

- Quotas: `SendMessages` **4,000 rps**, `SendUsersMessages` **6,000 rps**, `UpdateEndpoint` **10 rps**, `UpdateEndpointsBatch` **2 rps**, `GetEndpoint` **10 rps**. Independent per operation, per region, per account.
- **No idempotency.** A retried `SendMessages` sends the message again. If the connection drops after the request left, you cannot tell from the API whether it landed — the `RequestId` is only in the response you did not get. Design the caller to tolerate this or de-duplicate upstream.
- Payload ceiling is **7 MB** per invocation; an email including attachments is **10 MB**; an endpoint is **15 KB** with at most **250 attributes**, attribute names ≤ 50 chars and values ≤ 100 chars.
- Sandbox accounts can only send email to **verified** recipients, 200 per 24 hours at 1 per second, and SMS only to verified numbers under a $1.00 monthly cap.
- `PERMANENT_FAILURE` on email usually means an unverified sending identity or a hard bounce; check the event stream (`asyncapi/amazon-pinpoint-events.yml`) for `_email.hardbounce`.
