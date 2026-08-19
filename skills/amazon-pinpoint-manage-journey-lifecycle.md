---
name: Create and drive an Amazon Pinpoint journey through its lifecycle
description: Author a multi-step journey, move it between DRAFT, ACTIVE, PAUSED, CANCELLED and CLOSED, and read its execution metrics.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, arazzo/amazon-pinpoint-launch-journey-workflow.yml, arazzo/amazon-pinpoint-pause-active-journey-workflow.yml
operations:
  - CreateJourney
  - GetJourney
  - ListJourneys
  - UpdateJourney
  - UpdateJourneyState
  - GetJourneyExecutionMetrics
  - GetJourneyExecutionActivityMetrics
  - GetJourneyDateRangeKpi
  - DeleteJourney
---

# Drive a journey through its lifecycle

A journey is Pinpoint's multi-step automation: a start condition over a segment, then a graph of activities (send email/SMS/push, wait, multivariate split, random split, holdout, condition, custom Lambda).

## Steps

1. **Create it in DRAFT.** `CreateJourney` with a `WriteJourneyRequest` that sets `Name`, `StartCondition` (usually `SegmentStartCondition.SegmentId`), `StartActivity`, the `Activities` map, `Schedule` (`StartTime`, `EndTime`, `Timezone`), `Limits` (`DailyCap`, `EndpointReentryCap`, `MessagesPerSecond`, `EndpointReentryInterval`) and `QuietTime`. Capture `JourneyResponse.Id`.
2. **Review before you activate.** `GetJourney` and check the `State` and that every activity id referenced in `NextActivity` exists in the `Activities` map. A dangling reference is a 400 at activation, not at create.
3. **Activate.** `UpdateJourneyState` with `JourneyStateRequest.State = ACTIVE`. This is the moment the journey starts touching customers.
4. **Pause or resume.** `UpdateJourneyState` with `PAUSED`, then `ACTIVE` again to resume. Pausing holds endpoints in place rather than dropping them.
5. **Stop for good.** `CANCELLED` abandons in-flight endpoints; `CLOSED` stops new entries but lets endpoints already inside finish. Choose deliberately — they are not the same.
6. **Measure.** `GetJourneyExecutionMetrics` for journey-level counters, `GetJourneyExecutionActivityMetrics` for one activity (`journey-activity-id`), `GetJourneyDateRangeKpi` for a named KPI over a `start-time`/`end-time` window.
7. **Clean up.** `DeleteJourney` removes it. `ListJourneys` pages with `page-size` and `token`.

## Rules

- **`UpdateJourney` is the one operation in this API that returns 409 `ConflictException`.** It fires when the journey's current state does not allow the edit — typically editing an `ACTIVE` journey. The recovery is: `UpdateJourneyState` to `PAUSED`, apply `UpdateJourney`, then back to `ACTIVE`. Do not retry the same `UpdateJourney` blindly; it will conflict again.
- The `487` response code on `UpdateJourney` in the OpenAPI is an aws2openapi placeholder. The wire status is **409**.
- State transitions are not idempotent-safe in any special way, but they are effectively convergent: setting `PAUSED` on an already-paused journey is harmless. Creating a journey is not — check `ListJourneys` before retrying a timed-out `CreateJourney`.
- Journey operations fall under the **300 rps** default quota.
- Journey **run** metrics (`get-journey-runs`, `get-journey-run-execution-metrics`, `get-journey-run-execution-activity-metrics`) exist in the AWS CLI and the live service but are **absent from the OpenAPI in this repo** — the harvested contract predates them. Reach them via the CLI or an SDK, not from the spec.
- Everything here stops working on **2026-10-30**. Journeys have no equivalent in AWS End User Messaging; the migration target is Amazon Connect outbound campaigns.
