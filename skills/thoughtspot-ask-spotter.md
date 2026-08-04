---
name: Ask ThoughtSpot Spotter a data question
description: >-
  Authenticate, discover relevant questions for a natural-language prompt, open
  a Spotter AI conversation over a data model, and retrieve a governed answer.
api: openapi/thoughtspot-rest-v2-openapi-original.json
operations:
- getFullAccessToken
- getRelevantQuestions
- createConversation
- sendMessage
- fetchAnswerData
---

# Ask ThoughtSpot Spotter a data question

Use the ThoughtSpot Public REST API v2.0 (`/api/rest/2.0`) to ask a
natural-language business question and get a governed answer from Spotter.

## Auth
All calls send `Authorization: Bearer <access_token>`.
1. `getFullAccessToken` (`POST /auth/token/full`) — exchange username +
   password (or a trusted-auth secret) for a bearer token. Reuse the token
   until it expires; do not request a new token per call.

## Steps
1. `getRelevantQuestions` (`POST /ai/relevant-questions/`) — pass the user's
   prompt plus the target data model/worksheet identifier to get a list of
   answerable, well-formed questions grounded in the semantic model.
2. `createConversation` (`POST /ai/conversation/create`) — open a Spotter
   conversation scoped to the chosen data model; keep the returned
   `conversation_identifier`.
3. `sendMessage` (`POST /ai/conversation/{conversation_identifier}/converse`) —
   send the selected question; Spotter returns an answer with the underlying
   query and result reference.
4. `fetchAnswerData` (`POST /metadata/answer/data`) — retrieve the tabular
   result data for the answer for downstream use.

## Rules
- Idempotency is identifier-based (conventions/thoughtspot-conventions.yml):
  reuse the same `conversation_identifier` to continue a thread instead of
  creating duplicate conversations.
- Errors return `application/json` with an `{ "error": {...} }` envelope
  (errors/thoughtspot-problem-types.yml); 401 = re-auth, 403 = the user lacks
  privileges on the data model.
- Answers respect ThoughtSpot's object-level security — you only get data the
  authenticated user can see.
