---
name: Export and import ThoughtSpot content as TML
description: >-
  Authenticate, find content objects, export them as ThoughtSpot Modeling
  Language (TML) for version control, and import TML to promote changes.
api: openapi/thoughtspot-rest-v2-openapi-original.json
operations:
- getFullAccessToken
- searchMetadata
- exportMetadataTML
- importMetadataTML
---

# Export and import ThoughtSpot content as TML

Use TML (ThoughtSpot Modeling Language) to version-control and migrate content
(Models, Answers, Liveboards, Tables) via the v2.0 REST API (`/api/rest/2.0`).

## Auth
Send `Authorization: Bearer <access_token>` on every call.
1. `getFullAccessToken` (`POST /auth/token/full`) — obtain a bearer token with
   privileges on the content you intend to export/import.

## Steps
1. `searchMetadata` (`POST /metadata/search`) — locate the objects to export by
   type (LIVEBOARD, ANSWER, LOGICAL_TABLE, etc.) and collect their identifiers.
2. `exportMetadataTML` (`POST /metadata/tml/export`) — export the selected
   objects as TML (YAML/JSON) text; commit the returned TML to your Git repo.
3. `importMetadataTML` (`POST /metadata/tml/import`) — import edited TML into a
   target environment to create or update objects; use the import policy to
   validate before committing changes.

## Rules
- TML import is identifier-aware: importing TML that carries an existing object
  GUID updates that object rather than creating a duplicate, making promotion
  idempotent (conventions/thoughtspot-conventions.yml).
- For large exports use `exportMetadataTMLBatched`; for large imports use
  `importMetadataTMLAsync`.
- Validate with the import's dry-run/validate policy first; errors return the
  `{ "error": {...} }` envelope (errors/thoughtspot-problem-types.yml).
- ThoughtSpot also offers native Git version control (the `vcs` API group) built
  on this TML export/import mechanism.
