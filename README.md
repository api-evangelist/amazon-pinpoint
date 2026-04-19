# Amazon Pinpoint (amazon-pinpoint)
Amazon Pinpoint is a flexible and scalable outbound and inbound marketing communications service that enables you to engage with customers across multiple messaging channels including email, SMS, push notifications, and voice messages. Note - AWS will end support for Amazon Pinpoint on October 30, 2026. SMS, voice, mobile push, OTP, and phone number validation APIs will continue through AWS End User Messaging.

**URL:** [https://aws.amazon.com/pinpoint/](https://aws.amazon.com/pinpoint/)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags:

 - AWS, Campaigns, Communications, Email, Marketing, Messaging, Push Notifications, SMS, Voice, Customer Engagement, Segmentation, Journeys, Analytics

## Timestamps

- **Created:** 2024-01-15
- **Modified:** 2026-04-19

## APIs

### Amazon Pinpoint API
The Amazon Pinpoint API enables you to create and manage marketing campaigns, send transactional messages, define audience segments, manage message templates, configure messaging channels (email, SMS, push, voice), and analyze engagement metrics for multi-channel communications.

**Human URL:** [https://aws.amazon.com/pinpoint/](https://aws.amazon.com/pinpoint/)

#### Tags:

 - Communications, Marketing, Messaging, Campaigns, Segmentation, Journeys, Analytics, Email, SMS, Push Notifications, Voice

#### Properties

- [Documentation](https://docs.aws.amazon.com/pinpoint/latest/developerguide/welcome.html)
- [APIReference](https://docs.aws.amazon.com/pinpoint/latest/apireference/welcome.html)
- [OpenAPI](openapi/amazon-pinpoint-openapi.yml)
- [OpenAPI](openapi/amazon-pinpoint-openapi-original.yaml)
- [Pricing](https://aws.amazon.com/pinpoint/pricing/)
- [GettingStarted](https://aws.amazon.com/pinpoint/getting-started/)
- [FAQ](https://aws.amazon.com/pinpoint/faqs/)
- [Features](https://aws.amazon.com/pinpoint/features/)
- [Quotas](https://docs.aws.amazon.com/pinpoint/latest/developerguide/quotas.html)
- [Authentication](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)
- [RateLimits](https://docs.aws.amazon.com/pinpoint/latest/developerguide/quotas.html)

## Common Properties

- [Portal](https://console.aws.amazon.com/pinpoint/)
- [Blog](https://aws.amazon.com/blogs/messaging-and-targeting/)
- [Support](https://aws.amazon.com/premiumsupport/)
- [CLI](https://docs.aws.amazon.com/cli/latest/reference/pinpoint/)
- [SDK](https://aws.amazon.com/tools/)
- [StatusPage](https://status.aws.amazon.com/)
- [TermsOfService](https://aws.amazon.com/service-terms/)
- [PrivacyPolicy](https://aws.amazon.com/privacy/)
- [Documentation](https://docs.aws.amazon.com/pinpoint/)
- [Pricing](https://aws.amazon.com/pinpoint/pricing/)
- [GettingStarted](https://aws.amazon.com/pinpoint/getting-started/)
- [FAQ](https://aws.amazon.com/pinpoint/faqs/)
- [SignUp](https://portal.aws.amazon.com/billing/signup)
- [GitHubOrganization](https://github.com/aws)
- [StackOverflow](https://stackoverflow.com/questions/tagged/amazon-pinpoint)
- [CodeExamples](https://docs.aws.amazon.com/code-library/latest/ug/pinpoint_code_examples.html)
- [Compliance](https://aws.amazon.com/compliance/)
- [SpectralRules](rules/amazon-pinpoint-spectral-rules.yml)
- [NaftikoCapability](capabilities/customer-engagement.yaml)
- [Vocabulary](vocabulary/amazon-pinpoint-vocabulary.yaml)

## Features

| Name | Description |
|------|-------------|
| Multi-Channel Messaging | Send messages via email, SMS, push notifications, and voice through a unified API. |
| Audience Segmentation | Create dynamic segments based on app data or import static segments from external sources. |
| Messaging Campaigns | Schedule targeted campaigns with A/B testing and detailed analytics reporting. |
| Customer Journeys | Build multi-step automated engagement workflows triggered by customer events. |
| Transactional Messaging | Send real-time transactional messages such as account confirmations, order updates, and password resets. |
| Message Templates | Create reusable email, SMS, push, voice, and in-app message templates with personalization. |
| Analytics and Metrics | Track engagement trends, open rates, delivery rates, and campaign performance across all channels. |
| Endpoint Management | Manage customer endpoint profiles including device tokens, email addresses, and phone numbers. |

## Use Cases

| Name | Description |
|------|-------------|
| Marketing Campaigns | Run scheduled, targeted promotional campaigns across email, SMS, and push channels. |
| Customer Onboarding | Automate welcome sequences and onboarding journeys for new users. |
| Transactional Notifications | Deliver order confirmations, shipping updates, and account alerts in real time. |
| Re-engagement Campaigns | Win back inactive users with targeted re-engagement messages and offers. |
| A/B Testing | Experiment with different message content, timing, and channels to optimize engagement. |
| Event-Based Messaging | Trigger personalized messages based on in-app events and user behaviors. |

## Integrations

| Name | Description |
|------|-------------|
| Amazon Kinesis | Stream Pinpoint analytics data to Kinesis for real-time processing and external storage. |
| Amazon S3 | Import and export endpoint data and segment definitions using S3 bucket storage. |
| AWS Lambda | Trigger Lambda functions from Pinpoint journey actions and campaign events. |
| Amazon CloudWatch | Monitor Pinpoint service metrics and set alarms using CloudWatch. |
| AWS End User Messaging | The successor service for SMS, voice, push, OTP, and phone number validation APIs continuing after Pinpoint deprecation. |
| Amazon SES | Amazon Simple Email Service provides the email delivery infrastructure for Pinpoint email campaigns. |

## Artifacts

Machine-readable API specifications organized by format.

### OpenAPI

- [Amazon Pinpoint OpenAPI (simple)](openapi/amazon-pinpoint-openapi.yml)
- [Amazon Pinpoint OpenAPI (full - 119 operations)](openapi/amazon-pinpoint-openapi-original.yaml)

### JSON Schema

455 schema files covering all Amazon Pinpoint API resource types including campaigns, segments, journeys, channels, templates, endpoints, and messages.

- [Application Response](json-schema/amazon-pinpoint-application-response-schema.json)
- [Campaign Response](json-schema/amazon-pinpoint-campaign-response-schema.json)
- [Segment Response](json-schema/amazon-pinpoint-segment-response-schema.json)
- [Journey Response](json-schema/amazon-pinpoint-journey-response-schema.json)
- [Message Request](json-schema/amazon-pinpoint-message-request-schema.json)

### JSON Structure

455 JSON Structure files converted from JSON Schema with strict typing.

### JSON-LD

- [Campaigns Context](json-ld/amazon-pinpoint-campaigns-context.jsonld)
- [Segments Context](json-ld/amazon-pinpoint-segments-context.jsonld)
- [Journeys Context](json-ld/amazon-pinpoint-journeys-context.jsonld)
- [Channels Context](json-ld/amazon-pinpoint-channels-context.jsonld)
- [Templates Context](json-ld/amazon-pinpoint-templates-context.jsonld)
- [Messages Context](json-ld/amazon-pinpoint-messages-context.jsonld)
- [Analytics Context](json-ld/amazon-pinpoint-analytics-context.jsonld)
- [Endpoints Context](json-ld/amazon-pinpoint-endpoints-context.jsonld)
- [Apps Context](json-ld/amazon-pinpoint-apps-context.jsonld)
- [General Context](json-ld/amazon-pinpoint-general-context.jsonld)

### Examples

83 example JSON files generated from JSON Schema definitions.

## Capabilities

Naftiko capabilities organized as shared per-API definitions composed into customer-facing workflows.

### Shared Per-API Definitions

- [Amazon Pinpoint API](capabilities/shared/amazon-pinpoint.yaml) — 9 operations for multi-channel customer engagement

### Workflow Capabilities

| Workflow | APIs Combined | Tools | Persona |
|----------|--------------|-------|---------|
| [Customer Engagement](capabilities/customer-engagement.yaml) | Amazon Pinpoint API | 9 | Marketing Manager, Growth Engineer |

## Vocabulary

- [Amazon Pinpoint Vocabulary](vocabulary/amazon-pinpoint-vocabulary.yaml) — Unified taxonomy mapping resources, actions, workflows, and personas across operational (OpenAPI) and capability (Naftiko) dimensions

## Rules

- [Amazon Pinpoint Spectral Rules](rules/amazon-pinpoint-spectral-rules.yml) — 38 rules across 13 categories enforcing Amazon Pinpoint API conventions

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
