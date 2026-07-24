---
name: Create an agent and launch a call
description: Pick an avatar, create a conversational video agent, start a call, and read back the transcript using the Beyond Presence API.
api: openapi/beyond-presence-openapi-original.json
operations:
  - list_avatars_v1_avatars_get
  - create_agent_v1_agents_post
  - create_call_v1_calls_post
  - list_messages_v1_calls__id__messages_get
---

# Create an agent and launch a call

Use the Beyond Presence API (`https://api.bey.dev`) to stand up a conversational
video agent and run a call.

## Auth
Send `x-api-key: <YOUR_API_KEY>` on every request. Validate the key first with
`GET /v1/auth/verify` (`verify_v1_auth_verify_get`) if unsure.

## Steps
1. **Choose an avatar** — `GET /v1/avatars` (`list_avatars_v1_avatars_get`).
   Paginate with `limit` (max 50) and `cursor`; read `data`, `has_more`,
   `next_cursor`. Note the `id` of the avatar you want.
2. **Create the agent** — `POST /v1/agents` (`create_agent_v1_agents_post`) with
   the chosen avatar and its system prompt / configuration. Save the returned
   agent `id`.
3. **Start a call** — `POST /v1/calls` (`create_call_v1_calls_post`) for that
   agent to obtain the call and its join/iframe URL.
4. **Read the transcript** — after the call, `GET /v1/calls/{id}/messages`
   (`list_messages_v1_calls__id__messages_get`) for the transcribed messages.

## Rules
- Pagination is cursor-based (`limit`/`cursor` in, `data`/`has_more`/`next_cursor` out).
- No idempotency key is supported; do not blindly retry `POST` operations.
- Errors: `401` = bad/missing key (`{"detail": ...}`); `422` = validation error
  (`{"detail":[{"loc","msg","type"}]}`). See errors/beyond-presence-problem-types.yml.
- For live call events, subscribe to webhooks (`message`, `call_ended`) — see
  asyncapi/beyond-presence-webhooks.yml.
