# Privacy Policy — WA_bridge

**Effective date:** 2026-10-12

**Operator:** Kewmann Pte Ltd
**Registered address:** 12 Marina Boulevard, Marina Bay Financial Centre Tower 3, #17-01, Singapore 018982
**Country:** Singapore
**Privacy contact:** info@kewmann.com

---

## What this service does

WA_bridge is a backend integration service that forwards WhatsApp Business messages between end customers and our client's customer support platform (Chongqing Fangzheng Technology Co., Ltd. / 重庆方振科技有限公司, "方振"). When a customer messages 方振 on WhatsApp, WA_bridge receives the message from Meta's servers, authenticates and forwards it to 方振's support platform, and delivers the agent's reply back to the customer over WhatsApp.

---

## Data we collect

When a customer sends a text or voice-note message to 方振's WhatsApp business number, WA_bridge processes the following data:

| Data | Source | What we do with it |
|---|---|---|
| Phone number (E.164 format) | Meta's webhook payload | Forward to 方振; store as conversation key |
| WhatsApp message ID (`wamid`) | Meta's webhook payload | Use for deduplication; forward to 方振 |
| Message timestamp | Meta's webhook payload | Forward to 方振 to order messages chronologically |
| Message direction (inbound / outbound) | WA_bridge | For our own delivery auditing |
| Message type (text / audio) | Meta's webhook payload | Determines processing path |
| Text message body | Meta's webhook payload | Forward to 方振 (see "Text body retention" below) |
| Voice note (audio file) | Meta's Media API | Downloaded, forwarded to 方振, not stored on our side |
| Customer display name (if available) | Meta's webhook payload | Forward to 方振; store as conversation metadata |

We do not collect or process: payment data, location data, contacts, calendar, device identifiers, or any data beyond what Meta includes in the WhatsApp Business webhook payload.

---

## How we use this data

We process the data only to:

1. **Forward messages** between Meta's WhatsApp platform and 方振's customer support platform.
2. **Prevent duplicate processing** — the WhatsApp message ID is used to ensure the same inbound message is not processed twice when Meta retries delivery.
3. **Retry delivery** during transient platform outages — a short-lived queue holds text bodies when 方振 is temporarily unreachable.
4. **Audit delivery status** — message IDs and timestamps let us confirm whether outbound messages reached Meta successfully.

We do not use this data for marketing, advertising, profiling, automated decision-making, or any purpose beyond delivering the conversation between the customer and 方振.

---

## What we store and for how long

### Message metadata — kept until you request deletion

Conversation records (phone number, customer display name) and message metadata (WhatsApp message ID, timestamp, direction, message type, delivery status) are stored in our database. We keep these records until the user requests deletion (see "How to request deletion" below). We do not automatically delete this data after a fixed period.

### Text message body — short-lived retry buffer only

When 方振's platform is temporarily unreachable, WA_bridge queues the text message body in a retry buffer (the "outbox") until delivery succeeds or the entry expires. Outbox entries are deleted after `OUTBOX_MAX_AGE_DAYS` (currently 1 day). Expired entries are logged before deletion for operational auditing.

Once 方振 acknowledges receipt of a queued message, the text body is removed from the outbox immediately.

### Voice notes (audio) — never stored

Audio files are downloaded from Meta's servers via the WhatsApp Business Media API, encoded in memory, forwarded to 方振's support platform, and then discarded. We do not write audio bytes to disk and do not retain them after the forward call completes. The Meta `media_id` reference is kept in the outbox for the same 1-day window as text bodies, then deleted.

### Deletion summary

| Data | Stored? | Lifetime |
|---|---|---|
| Phone number | Yes (in `conversations` table) | Until user requests deletion |
| Customer display name | Yes (in `conversations` table) | Until user requests deletion |
| Message metadata (wamid, timestamp, direction, type, status) | Yes (in `messages` table) | Until user requests deletion |
| Text body — successful delivery | No | — |
| Text body — queued for retry | Yes (in `outbox` table) | Up to 1 day, or until delivered |
| Audio bytes | No | — |

---

## Who we share data with

We share the data with **one party**:

**Chongqing Fangzheng Technology Co., Ltd. (重庆方振科技有限公司)** ("方振"), our client and the operator of the customer support platform. 方振 receives the message content (phone, display name, text body or audio file, timestamp, message ID) so their support agents can reply to the customer. 方振's handling of the data is governed by 方振's own privacy policy; please also contact 方振 if you wish to delete your data from their system.

We do not share WhatsApp Business data with any other party. We do not sell, rent, or trade this data.

---

## Cross-border data transfer

WA_bridge is operated by Kewmann Pte Ltd in Singapore. We forward messages to 方振, which is operated from China. By messaging 方振 on WhatsApp, you acknowledge that your message content (including phone number, text body, and any voice note) will be transferred from Meta's infrastructure (location varies) to our server in Singapore, then forwarded to 方振 in China for agent reply.

We rely on WhatsApp's standard data flow (Meta → our webhook → 方振) for this transfer. We do not perform any additional cross-border transfer beyond what is described in this paragraph.

---

## Security

- All webhook traffic to and from WA_bridge uses HTTPS (TLS).
- Inbound webhook requests from Meta are verified using the `X-Hub-Signature-256` HMAC-SHA256 signature with the Meta app secret. Requests with missing or invalid signatures are rejected.
- Outbound webhook requests to 方振 are signed using HMAC-SHA256 with a shared secret; recipients verify the signature before accepting the payload.
- Database access is restricted to authenticated operators via the bridge's API and ops tooling.
- We do not log message bodies in plaintext; routine logs may contain message IDs, phone hashes, and delivery status, but not message content.

We do not currently claim encryption at rest for stored metadata. The database is access-controlled, but metadata fields (phone, name) are stored as plain text.

---

## Your rights

Depending on your jurisdiction, you may have rights to:

- **Access** the data we hold about you
- **Correct** inaccurate data
- **Delete** the data we hold about you
- **Withdraw consent** and stop further processing
- **Lodge a complaint** with a data protection authority

To exercise any of these rights, contact us at **info@kewmann.com**.

For data held by 方振's support platform, please also contact 方振's privacy team — we cannot delete data that we did not receive from Meta or that has been forwarded to 方振.

---

## How to request deletion

Send an email to **info@kewmann.com** with:

- The phone number you messaged us from (E.164 format, e.g. `+8613xxxxxxxxx`)

We will delete all WA_bridge records associated with that phone number — conversation records, message metadata, and any queued retry rows — within **30 days** of receiving your request, and reply to confirm.

Note: this deletes our records only. 方振's customer support platform holds a copy of the conversation from the moment your message was forwarded. To delete from their system, contact 方振 directly.

---

## Children

WA_bridge is a backend service for adult customer support. We do not knowingly collect data from individuals under the age of 16. If we receive such data inadvertently (the customer self-identifies as a minor or we determine a phone number belongs to a minor), we delete it on request without further processing.

---

## Changes to this policy

We may update this policy as the service evolves. The effective date above will change on each update. Material changes (data categories added, retention extended, new recipients) will be announced by posting the revised policy at this URL; continued use of the service after a change constitutes acceptance of the revised policy. Non-material changes (wording, contact details, typo fixes) will be made without separate notice.

---

## Contact

**Kewmann Pte Ltd**
12 Marina Boulevard, Marina Bay Financial Centre Tower 3, #17-01, Singapore 018982
Singapore
Email: info@kewmann.com
