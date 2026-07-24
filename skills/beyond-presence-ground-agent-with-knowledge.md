---
name: Ground an agent with a knowledge file
description: Upload a knowledge file via chunked upload and attach it to a Beyond Presence agent so the agent can reference it during calls.
api: openapi/beyond-presence-openapi-original.json
operations:
  - create_knowledge_file_v1_knowledge_files_post
  - upload_knowledge_file_chunk_v1_knowledge_files__id__upload_put
  - submit_knowledge_file_v1_knowledge_files__id__submit_post
  - update_agent_v1_agents__id__patch
---

# Ground an agent with a knowledge file

Give a Beyond Presence agent reference material through the chunked
knowledge-file upload flow.

## Auth
Send `x-api-key: <YOUR_API_KEY>` on every request.

## Steps
1. **Initialize the upload** — `POST /v1/knowledge-files`
   (`create_knowledge_file_v1_knowledge_files_post`) to create the file record
   and start a chunked upload. Save the returned knowledge-file `id`.
2. **Upload each chunk** — `PUT /v1/knowledge-files/{id}/upload`
   (`upload_knowledge_file_chunk_v1_knowledge_files__id__upload_put`) once per
   chunk until the whole file is sent.
3. **Finalize** — `POST /v1/knowledge-files/{id}/submit`
   (`submit_knowledge_file_v1_knowledge_files__id__submit_post`) to close and
   process the upload.
4. **Attach to the agent** — `PATCH /v1/agents/{id}`
   (`update_agent_v1_agents__id__patch`) referencing the knowledge file so the
   agent can use it during calls.

## Rules
- Upload chunks in order; finalize only after all chunks succeed.
- No idempotency key — track which chunks succeeded rather than re-submitting blindly.
- Errors: `401` bad key, `422` validation. See errors/beyond-presence-problem-types.yml.
