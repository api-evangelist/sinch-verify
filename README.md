# Sinch Verification API (sinch-verify)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

The **Sinch Verification API** (Sinch Verify) is a dedicated phone number verification product for confirming that a user actually controls a given mobile number - the core building block of OTP, two-factor authentication (2FA), and anti-fraud sign-up flows. It supports four verification methods:

- **SMS** - a one-time passcode (OTP) delivered by text
- **Flash call** - a missed call whose caller ID *is* the code (cheaper and faster than SMS)
- **Phone call / callout** - a spoken or key-press code over a voice call
- **Data (seamless)** - silent verification over the mobile data connection

The lifecycle is straightforward: **start** a verification, **report** the code or caller ID the user received, then read the pass/fail **result**. Status can also be queried ad-hoc by id, by phone number, or by a custom reference string. The API is REST over HTTPS at `https://verification.api.sinch.com/verification/v1`.

> **Product-specific entry.** This is the product-level treatment of the Verification API. The parent company, **Sinch**, is a global CPaaS provider (SMS, Voice, Email, Conversation, Numbers, Verification) and is cataloged separately under the `sinch` provider entry. This `sinch-verify` entry isolates the Verification (Verify) product so the "number verification" / OTP / 2FA use case is directly discoverable.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/sinch-verify/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/sinch-verify/refs/heads/main/apis.yml)

## Access Model (honest read)

- **Not free/open.** The Verification API is a commercial, server-to-server API. You need a Sinch account and an application key/secret created in the Sinch Build dashboard. A **free trial** is available on sign-up.
- **Server-to-server auth only.** Two schemes: **Application-signed request** (HMAC-SHA256 over the canonicalized request using your application key and secret - recommended for production) and **HTTP Basic auth** (application key/secret - recommended only for prototyping). Secrets must never be embedded in a client app; the mobile/web Verification SDKs call the same API on your behalf.
- **Usage-based pricing.** Pay-as-you-go, billed per verification attempt, priced by method and by destination country/operator. Flash call is a fixed, materially lower per-attempt price (Sinch cites roughly 50-70% cheaper than SMS). Exact rates live behind the Build dashboard price list and are **not reconciled** in this catalog.
- **Modeled artifacts flagged.** The OpenAPI paths and methods here are grounded in the public Sinch developer documentation; request/response **schemas are modeled** (`x-modeled: true`) and should be reconciled against the live API reference and Sinch's published OpenAPI.

## Tags

- Number Verification
- Phone Verification
- OTP
- 2FA
- CPaaS
- SMS Verification
- Flash Call
- Two-Factor Authentication
- Identity Verification
- Sinch

## Timestamps

- **Created:** 2026-07-11
- **Modified:** 2026-07-11

## APIs

### Sinch Start Verification API

Starts a phone number verification (`POST /verifications`). Choose the method - `sms`, `flashcall`, `callout`, or `seamless` - and provide the phone number as the identity. Sinch responds with the template or CLI filter the client should watch for. Same endpoint the mobile and web Verification SDKs call; callable directly from any backend.

- **Human URL:** [Start a verification](https://developers.sinch.com/docs/verification/api-reference/verification/verifications-start)
- **Base URL:** `https://verification.api.sinch.com/verification/v1`
- **Endpoint:** `POST /verifications`

### Sinch Report Verification API

Reports the code or caller ID a user received so Sinch can complete the verification and return a pass/fail result. Report by verification id (`PUT /verifications/id/{id}`) or by the phone number the verification was sent to (`PUT /verifications/number/{endpoint}`).

- **Human URL:** [Verification API reference](https://developers.sinch.com/docs/verification/api-reference/verification)
- **Base URL:** `https://verification.api.sinch.com/verification/v1`
- **Endpoints:** `PUT /verifications/id/{id}`, `PUT /verifications/number/{endpoint}`

### Sinch Verification Status API

Queries the status of pending and completed verifications ad-hoc - by verification id (`GET /verifications/id/{id}`), by phone number and method (`GET /verifications/{method}/number/{endpoint}`), or by a custom reference string (`GET /verifications/reference/{reference}`).

- **Human URL:** [Verifications status](https://developers.sinch.com/docs/verification/api-reference/verification/verification-status)
- **Base URL:** `https://verification.api.sinch.com/verification/v1`
- **Endpoints:** `GET /verifications/id/{id}`, `GET /verifications/{method}/number/{endpoint}`, `GET /verifications/reference/{reference}`

## WebSocket Review

**Does Sinch Verification API expose a documented public WebSocket API? No.** The Verification API is request/response REST over HTTPS. Its only asynchronous mechanism is HTTP webhook callbacks (verification request, result, and SMS-delivered events) POSTed to a customer-configured URL - server-to-endpoint HTTP, not a WebSocket. See [`review.yml`](review.yml).

## Common Properties

- [Authentication](authentication/sinch-verify-authentication.yml)
- [Domain Security](security/sinch-verify-domain-security.yml)
- [LinkedIn](https://www.linkedin.com/company/sinch)
- [Website](https://sinch.com/verification-api/)
- [Documentation](https://developers.sinch.com/docs/verification/introduction)
- [Developer Portal](https://developers.sinch.com/)
- [Sign Up](https://dashboard.sinch.com/signup)
- [Plans](plans/sinch-verify-plans-pricing.yml)
- [Rate Limits](rate-limits/sinch-verify-rate-limits.yml)
- [Fin Ops](finops/sinch-verify-finops.yml)
- [Pricing](https://sinch.com/pricing/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
