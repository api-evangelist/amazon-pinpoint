---
name: Send and verify a one-time password with Amazon Pinpoint
description: Issue an OTP over SMS and validate the code the user types back, using the two-call OTP surface that survives the Pinpoint retirement.
api: openapi/amazon-pinpoint-apps-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/amazon-pinpoint-apps-api-openapi.yml, https://docs.aws.amazon.com/pinpoint/latest/developerguide/send-validate-otp.html, arazzo/amazon-pinpoint-send-and-verify-otp-workflow.yml
operations:
  - SendOTPMessage
  - VerifyOTPMessage
---

# Send and verify a one-time password

This is one of the few Pinpoint surfaces that is **not** being retired. AWS states that SMS, voice, mobile push, OTP and PhoneNumberValidate are unaffected by the 2026-10-30 end of support and continue under AWS End User Messaging.

## Steps

1. **Send the code.** `SendOTPMessage` — `POST /v1/apps/{application-id}/otp`. The `SendOTPMessageRequestParameters` body requires:
   - `DestinationIdentity` — the recipient in E.164 form.
   - `ReferenceId` — **the value that ties the send to the verify.** Use a per-attempt identifier you can reproduce (for example a hash of session id + phone number). Generate it before the send and store it.
   - `BrandName`, `Channel` (`SMS`), `OriginationIdentity` (your short code, long code or sender id).
   - Optional: `CodeLength` (default 6), `ValidityPeriod` in minutes (default 5), `AllowedAttempts` (default 3), `Language`, `EntityId` / `TemplateId` for regulated markets.
   The response carries a `MessageResponse` with the per-endpoint `DeliveryStatus` — read it, do not assume delivery.
2. **Collect the code from the user** in your own UI. Pinpoint never returns the generated code.
3. **Verify.** `VerifyOTPMessage` — `POST /v1/apps/{application-id}/verify-otp`. Send `DestinationIdentity`, the **same** `ReferenceId`, and `Otp` (what the user typed). The `VerificationResponse` has one field: `Valid` (boolean).
4. **Branch on `Valid`.** `false` covers wrong code, expired code and attempts exhausted — the API does not distinguish them. Track your own attempt count if you need to tell the user which it was.

## Rules

- `ReferenceId` mismatch is the single most common failure. It must be byte-identical between the two calls.
- The code expires after `ValidityPeriod` minutes and dies after `AllowedAttempts` failures. Both are set at send time and cannot be changed afterwards.
- Neither call is idempotent. Re-sending `SendOTPMessage` issues a **new** code and invalidates nothing — the user may then hold two codes and the older one still verifies until it expires.
- Both fall under the **300 rps** default quota. Exhaustion is a `TooManyRequestsException` (429) with no `Retry-After`.
- In the SMS sandbox you can only send to verified destination numbers, up to 10, under a $1.00 monthly spend cap. Request production access before launch — one AWS Support case per region.
- Sending an OTP costs real money per SMS on the AWS End User Messaging price list, not the Pinpoint price list.
