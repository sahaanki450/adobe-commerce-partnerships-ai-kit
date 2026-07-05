# Receiving the API Spec

**Who does this:** Adobe provides the API spec — no action required from the partner  
**Frequency:** Once per feature  
**Prerequisites:** Backend Service card is complete and reviewed; Feature Experience card has been received

---

## What the API Spec Is

The API spec is a precise, structured description of every Adobe VIPMP Partners API endpoint the feature will call. It is written by **Adobe** and provided to you as part of the feature package alongside the experience card.

You do not write the API spec, and you don't need to validate it — Adobe provides it complete and ready to use. Read it to understand exactly which endpoints the feature calls, since that's the contract the skills will build against.

The AI generates the backend LLD by reading the API spec alongside your service cards.

---

## Understanding the API Spec's Structure

Every API spec covers every endpoint the feature uses, not just the primary one. Each operation includes:

- HTTP method and full path (e.g., `GET /v3/offer-switch-paths`)
- Query parameters, with name, type, required/optional, and description
- Request headers — typically `Authorization: Bearer <token>` and `X-Api-Key`
- Request body (for POST/PUT/PATCH), with every field, its type, and whether it is required
- Response body (200 OK), with every field the feature will use — actual JSON structure, not a prose description
- Error responses, with status code, error code, meaning, and expected UI behavior
- If an endpoint has multiple modes controlled by a field (e.g., `type: PREVIEW_SWITCH` vs `type: SWITCH`), each mode's own request/response shape

---

## Worked Example — Anytime Upgrade

Below is the API spec for the Anytime Upgrade feature, as provided by Adobe. This is the format you will receive.

### Authentication

All requests require:
- `Authorization: Bearer <access_token>` — obtained from the partner auth flow
- `X-Api-Key: <api_key>` — your partner API key

---

### Operation 1 — Get Upgrade Paths

**Purpose:** Retrieves all available upgrade paths for a customer's subscriptions. Called when the subscription management page loads (Step 1 of the experience card).

**HTTP Method and Path:**
```
GET /v3/offer-switch-paths
```

**Query Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `customerId` | string | Yes | The customer's Adobe ID |
| `subscriptionId` | string | Yes | The subscription to retrieve upgrade paths for |

**Request Headers:**

| Header | Value |
|---|---|
| `Authorization` | `Bearer <access_token>` |
| `X-Api-Key` | `<api_key>` |

**Response (200 OK):**
```json
{
  "offerSwitchPaths": [
    {
      "subscriptionId": "string — ID of the source subscription",
      "currentOfferId": "string — offer ID of the current subscription tier",
      "switchPaths": [
        {
          "targetOfferId": "string — offer ID of the upgrade target",
          "targetOfferName": "string — display name of the upgrade target",
          "minQuantity": "number — minimum quantity for this offer",
          "maxQuantity": "number — maximum quantity for this offer"
        }
      ]
    }
  ]
}
```

**Error Responses:**

| Status | Code | Meaning | UI behavior |
|---|---|---|---|
| 404 | `CUSTOMER_NOT_FOUND` | Customer ID does not exist | Show page-level error banner |
| 403 | `FORBIDDEN` | Partner lacks access to this customer | Redirect to customer list with error message |
| 500 | — | Server error | Show page-level error banner; preserve any data already loaded |

---

### Operation 2 — Preview or Place Upgrade Order

**Purpose:** Used for two sub-operations depending on the `type` field. Called to preview pricing (Steps 3–4 of the experience card) and to confirm the order (Step 5).

**HTTP Method and Path:**
```
POST /v3/orders
```

**Request Headers:**

| Header | Value |
|---|---|
| `Authorization` | `Bearer <access_token>` |
| `X-Api-Key` | `<api_key>` |
| `Content-Type` | `application/json` |

**Request Body — Preview:**
```json
{
  "type": "PREVIEW_SWITCH",
  "customerId": "string — customer's Adobe ID",
  "subscriptionId": "string — ID of the subscription being upgraded",
  "targetOfferId": "string — offer ID of the upgrade target",
  "quantity": "number — desired quantity after upgrade"
}
```

**Request Body — Place Order:**
```json
{
  "type": "SWITCH",
  "customerId": "string",
  "subscriptionId": "string",
  "targetOfferId": "string",
  "quantity": "number"
}
```

**Response (200 OK — Preview):**
```json
{
  "previewOrder": {
    "proratedTotal": "number — amount due now, in cents",
    "currency": "string — ISO 4217 currency code",
    "remainingTermDays": "number — days left in current billing term",
    "newMonthlyPrice": "number — monthly price after upgrade, in cents"
  }
}
```

**Response (200 OK — Place Order):**
```json
{
  "orderId": "string — ID of the placed order",
  "status": "string — COMPLETED | PENDING",
  "subscription": {
    "subscriptionId": "string",
    "offerId": "string — new offer ID after upgrade",
    "quantity": "number — new quantity"
  }
}
```

**Error Responses:**

| Status | Code | Meaning | UI behavior |
|---|---|---|---|
| 400 | `INVALID_QUANTITY` | Quantity outside min/max bounds | Show inline error in the upgrade panel |
| 400 | `INELIGIBLE_SWITCH` | This subscription/offer combination cannot be switched | Show inline error; do not close panel |
| 409 | `PENDING_ORDER_EXISTS` | A prior order for this subscription is pending | Inform user; disable confirm button |
| 500 | — | Server error | Show inline error; allow retry |

---

## What to Do If Something Is Unclear

Adobe provides the API spec complete and ready to use, so this should be rare. If you do hit something ambiguous, contact your Adobe partner contact with a specific question rather than making an assumption — a wrong assumption will propagate through the LLD into generated code. Example:

- "The experience card Step 3 says the preview shows a 'prorated total,' but the response shape does not include that field. Is it `proratedTotal` or something else?"

---

## After This Step

Proceed to **[Generating the Backend LLD](generate-backend-lld.md)**.