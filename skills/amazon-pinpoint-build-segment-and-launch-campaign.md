---
name: Build an Amazon Pinpoint segment and launch a campaign against it
description: Define an audience segment, create a campaign that targets it from a message template, and watch the campaign through to a terminal state.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, openapi/amazon-pinpoint-templates-api-openapi.yml, arazzo/amazon-pinpoint-build-segment-campaign-workflow.yml
operations:
  - CreateSegment
  - GetSegment
  - CreateEmailTemplate
  - UpdateTemplateActiveVersion
  - CreateCampaign
  - GetCampaign
  - GetCampaignActivities
  - UpdateCampaign
---

# Build a segment and launch a campaign

## Prerequisites

An application id from `CreateApp`, at least one enabled channel, and endpoints already loaded (`UpdateEndpointsBatch` or `CreateImportJob`). Everything happens inside one region.

## Steps

1. **Create the audience.** `CreateSegment` with a `WriteSegmentRequest`.
   - A **dimensional** segment carries a `SegmentGroups` / `Dimensions` query over endpoint attributes, demographics and behaviour — it re-evaluates at send time.
   - An **import** segment points at endpoints loaded by `CreateImportJob` — it is frozen at import time.
   - Capture `SegmentResponse.Id` and `SegmentResponse.Version`.
2. **Confirm the size before you spend money.** `GetSegment` returns `ImportDefinition.Size` for import segments. Imported segments are capped at **100,000,000 endpoints per campaign**. Every endpoint a campaign touches in a month is billed as Monthly Targeted Audience at $0.0012 above the free 5,000.
3. **Author the message.** `CreateEmailTemplate` (or `CreateSmsTemplate` / `CreatePushTemplate` / `CreateVoiceTemplate` / `CreateInAppTemplate`) with a `TemplateName`. Templates are **account-scoped, not project-scoped** — the name must be unique across the account, not the application.
4. **Promote the template version.** Templates are versioned. `UpdateTemplateActiveVersion` sets the version a campaign will render, otherwise the campaign binds to whatever is active now.
5. **Create the campaign.** `CreateCampaign` with a `WriteCampaignRequest` that sets `SegmentId`, `SegmentVersion`, `Schedule`, `MessageConfiguration` or `TemplateConfiguration`, and optionally `AdditionalTreatments` for an A/B split. Capture `CampaignResponse.Id`.
6. **Watch it.** Poll `GetCampaign` and read `State.CampaignStatus`. The values are `SCHEDULED`, `EXECUTING`, `PENDING_NEXT_RUN`, `COMPLETED`, `PAUSED`, `DELETED`, `INVALID`. `GetCampaignActivities` gives per-run counters (`SuccessfulEndpointCount`, `TotalEndpointCount`, `TimezonesCompletedCount`).
7. **Pause or amend.** `UpdateCampaign` with `IsPaused: true` stops an active campaign. A campaign is only editable in states the service allows — a rejected edit comes back as a 400, not a 409.

## Rules

- Pin `SegmentVersion` explicitly on the campaign. If you leave it off and the segment is edited later, the campaign audience moves under you.
- Rate quotas that apply here: `CreateSegment` **25 rps**, `CreateCampaign` **25 rps**, `UpdateCampaign` **25 rps**, `CreateEmailTemplate` **10 rps**. Independent per operation.
- **200 active campaigns per account** (`SCHEDULED`, `EXECUTING` or `PENDING_NEXT_RUN`) and **25 event-based campaigns per project**. Event-based campaigns must use dynamic segments, never imported ones.
- No idempotency key exists. Before retrying `CreateCampaign` after a timeout, call `GetCampaigns` and look for the name.
- Every write here is a real-money, real-customer action. Treat `CreateCampaign` on a large segment as the highest-consequence call in this API.
