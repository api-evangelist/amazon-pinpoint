---
name: Provision an Amazon Pinpoint project and enable its channels
description: Create a Pinpoint application (project), enable the delivery channels it needs, and confirm the configuration before any message is sent.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, conventions/amazon-pinpoint-conventions.yml, arazzo/amazon-pinpoint-provision-project-with-settings-workflow.yml
operations:
  - CreateApp
  - GetApp
  - UpdateApplicationSettings
  - GetApplicationSettings
  - UpdateEmailChannel
  - UpdateSmsChannel
  - GetChannels
---

# Provision a Pinpoint project and enable its channels

## Before you start

- **This service is being retired.** AWS ends support for Amazon Pinpoint on **2026-10-30** and has been closed to new customers since 2025-05-20. If you are standing up something new, use AWS End User Messaging or Amazon Connect outbound campaigns instead. Only continue here for an account that already has Pinpoint.
- **Pick a region and stay in it.** There is no global endpoint. Call `https://pinpoint.{region}.amazonaws.com`; `pinpoint.amazonaws.com` does not resolve. Resources never cross regions.
- **Auth is AWS SigV4**, not a bearer token. Sign every request with IAM credentials that hold the relevant `mobiletargeting:*` actions. There are no scopes.
- **Nothing here is idempotent.** Pinpoint publishes no idempotency key and no `ClientToken`. If `CreateApp` times out, do **not** blind-retry — call `GetApps` and look for the name first, or you will create a duplicate project.

## Steps

1. **Create the project.** `CreateApp` with a `CreateApplicationRequest` carrying `Name` and optional `tags`. Capture `ApplicationResponse.Id` — every later call needs it as the `application-id` path parameter.
2. **Confirm it exists.** `GetApp` with that id. A 404 `NotFoundException` here means you are talking to the wrong region, not that the create failed.
3. **Set project-wide settings.** `UpdateApplicationSettings` for the campaign quiet time, default campaign limits (`MessagesPerSecond`, `Daily`, `Total`), and the campaign hook if you use a custom channel. Read them back with `GetApplicationSettings`.
4. **Enable each channel you will send on.** One call per channel:
   - `UpdateEmailChannel` — requires a verified `FromAddress` and the SES `Identity` ARN.
   - `UpdateSmsChannel` — sets `SenderId` and `ShortCode`.
   - The push channels follow the same shape (`UpdateApnsChannel`, `UpdateGcmChannel`, `UpdateAdmChannel`, `UpdateBaiduChannel`, plus the APNs sandbox variants).
5. **Verify the whole set.** `GetChannels` returns every configured channel for the application in one response. Check `Enabled` is true on each channel you expect before sending anything.

## Rules

- Quotas that bite here: **100 projects per region** and **200 active campaigns per account**. Both are marked not eligible for increase.
- `CreateApp` has no published per-second quota of its own, so it falls under the **300 rps** default. Channel updates are also 300 rps.
- Errors are AWS JSON exceptions, not RFC 9457. Read `x-amzn-ErrorType`; map it with `errors/amazon-pinpoint-problem-types.yml`. Note the OpenAPI shows 480–487 — those are conversion placeholders, the wire statuses are 400/500/413/403/404/405/429/409.
- On `TooManyRequestsException` (429) back off exponentially with jitter. No `Retry-After` header is returned; you must pick the delay.
- New accounts sit in a **sandbox** per channel — email is capped at 200 messages per 24 hours to verified recipients only, and SMS at $1.00/month to up to 10 verified numbers. See `sandbox/amazon-pinpoint-sandbox.yml` before you conclude a send failed for another reason.
