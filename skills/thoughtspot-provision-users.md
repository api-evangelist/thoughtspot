---
name: Provision ThoughtSpot users and groups
description: >-
  Authenticate as an admin, search for existing users and groups, create a
  group, create a user, and assign the user to the group.
api: openapi/thoughtspot-rest-v2-openapi-original.json
operations:
- getFullAccessToken
- searchUsers
- searchUserGroups
- createUserGroup
- createUser
- updateUser
---

# Provision ThoughtSpot users and groups

Manage ThoughtSpot principals through the v2.0 REST API (`/api/rest/2.0`).

## Auth
Send `Authorization: Bearer <access_token>` on every call.
1. `getFullAccessToken` (`POST /auth/token/full`) — obtain an admin bearer
   token (the caller must hold user-administration privileges).

## Steps
1. `searchUserGroups` (`POST /groups/search`) — check whether the target group
   already exists (search by name); reuse its `identifier` if found.
2. `createUserGroup` (`POST /groups/create`) — create the group only if the
   search returned nothing.
3. `searchUsers` (`POST /users/search`) — check whether the user already
   exists before creating a duplicate.
4. `createUser` (`POST /users/create`) — create the user, passing the group
   identifier(s) so membership is set at creation.
5. `updateUser` (`POST /users/{user_identifier}/update`) — for an existing
   user, add the group membership or update roles instead of recreating.

## Rules
- Search-before-create keeps the flow idempotent: users and groups are keyed on
  a unique name (conventions/thoughtspot-conventions.yml), so re-running should
  update, not duplicate.
- Paginate search results with `record_offset` + `record_size` in the request
  body.
- 403 means the token lacks user-administration privileges; 409 signals a
  name/identifier conflict (errors/thoughtspot-problem-types.yml).
