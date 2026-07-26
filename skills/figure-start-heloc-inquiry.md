---
name: Start a HELOC inquiry and select an offer
description: Create a Figure HELOC inquiry, enrich it with borrower SSN, income and property, then present and select an offer.
api: openapi/figure-heloc-inquiries-openapi-original.yml
operations: [startInquiry, addInquirySsn, addInquiryIncome, addInquiryPropertyEstimate, verifyInquiryLiens, fetchMatchingProperties, selectProperty, selectInquiryOffer, getBorrowerCosts]
---

# Start a HELOC inquiry and select an offer

Use the Figure HELOC Inquiries API to run the home-equity offer flow for a borrower.

## Auth & conventions
- Send the `apikey` header (issued per environment at onboarding) and a `User-Agent` header on every request — requests missing either are rejected. See `authentication/figure-authentication.yml`.
- Base host: `https://api.figure.com` (production) or `https://api.test.figure.com` (test). See `conventions/figure-conventions.yml`.
- In test, drive outcomes with the magic values in `sandbox/figure-sandbox.yml` (e.g. last name ending `TestH`, SSN `111-11-1111` for the happy path).
- Payloads may be JWE/RSA-encrypted; if you cannot encrypt client-side, call `encryptPayload` (POST `/encryption/v1/encrypt`).

## Steps
1. **startInquiry** — POST `/products/heloc/v1/inquiry/start` with applicant, property, and partner data. Capture the returned `appUuid`.
2. **addInquirySsn** — PUT `/products/heloc/v1/inquiry/{appUuid}/add-ssn` to attach the borrower SSN.
3. **addInquiryIncome** — PUT `/products/heloc/v1/inquiry/{appUuid}/add-income` (or `assetDepletion` to add income plus liability payoff).
4. **addInquiryPropertyEstimate** — PUT `/products/heloc/v1/inquiry/{appUuid}/add-property-estimate` if a borrower estimate is needed.
5. If one address resolved to multiple properties, **fetchMatchingProperties** — GET `/products/heloc/v1/inquiry/{appUuid}/properties` then **selectProperty** — PUT `/products/heloc/v1/inquiry/{appUuid}/select-property`.
6. **verifyInquiryLiens** — PUT `/products/heloc/v1/inquiry/{appUuid}/verify-liens`.
7. **getBorrowerCosts** — GET `/products/heloc/v1/inquiry/{appUuid}/borrower-costs` to show costs, then **selectInquiryOffer** — PUT `/products/heloc/v1/inquiry/{appUuid}/select-offer`.

## Events
Subscribe to inquiry webhooks (e.g. `OFFERS_AVAILABLE`) rather than polling. See `asyncapi/figure-webhooks.yml`.

## Errors
Requests return JSON errors; handle 400 (bad request / missing header) and 401 (bad apikey). See `errors/figure-problem-types.yml`.
