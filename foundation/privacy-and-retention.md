# Privacy And Retention

## Purpose

Peaked will process resumes, job descriptions, generated application materials, and AI metadata. Privacy and retention rules must be explicit before backend implementation.

## Data Categories

Private user data:

- Account email and profile details.
- Uploaded resumes.
- Parsed resume text.
- Career evidence.
- User job goals and dealbreakers.
- Saved jobs and application notes.
- Generated resumes, cover letters, and interview prep.

Operational metadata:

- AI task status.
- Provider and model.
- Token and cost estimates.
- Cache hit/miss.
- Error codes.
- Timing and latency.
- Feature usage counts.

Payment data:

- Provider customer IDs.
- Checkout/payment intent IDs.
- Subscription status.
- Invoice/payment status.
- Webhook event IDs.

Do not store raw card, wallet, or banking credentials.

## Logging Rules

Allowed in normal logs:

- IDs.
- Task type.
- Status.
- Error code.
- Provider/model.
- Latency.
- Token/cost estimates.
- Cache hit/miss.

Avoid in normal logs:

- Full resume text.
- Full generated documents.
- Raw prompts.
- Private job notes.
- Access tokens.
- API keys.
- Webhook secrets.
- Full payment payloads.

## Resume Retention

Initial recommendation:

- Uploaded files remain until the user deletes them or deletes the account.
- Parsed resume text remains while the related resume version exists.
- Deleted resumes should become inaccessible immediately.
- Physical deletion from storage can be immediate or queued, but the user-facing state must be deleted.

Free-tier storage protection:

- Limit active resumes for Free users.
- Hash uploads to detect duplicates.
- Reject oversized files.
- Provide delete controls early.

## Generated Artifact Retention

Generated resumes, cover letters, and interview prep should:

- Belong to the user.
- Be private by default.
- Be deletable.
- Store prompt/model/schema version metadata.
- Avoid storing unnecessary intermediate prompt text.

## AI Provider Handling

Before enabling an AI provider:

- Review provider data usage policy.
- Keep API keys server-side.
- Send only necessary input.
- Treat resumes and job descriptions as data, not instructions.
- Schema-validate outputs.
- Store usage metadata for cost and debugging.

## Account Deletion

Account deletion should eventually:

- Soft-delete the profile first.
- Revoke sessions.
- Delete or queue deletion of private files.
- Delete or anonymize generated artifacts.
- Preserve legally required payment records if needed.
- Preserve aggregate analytics without direct personal identifiers.

## Admin Access

Admins should only see sensitive user data when operationally necessary.

Admin actions should be logged for:

- Manual entitlement grants.
- Payment status overrides.
- User support access.
- Data deletion actions.
- Source or AI kill-switch changes.

## Open Questions

- Exact retention period for deleted files in backups.
- Whether generated artifacts should have automatic expiry for Free users.
- Whether raw parsed resume text should be retained indefinitely or compacted into reviewed career evidence.
- Whether users need an export-my-data workflow in MVP or shortly after.

