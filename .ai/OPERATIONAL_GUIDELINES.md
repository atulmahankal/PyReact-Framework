## Provider-agnostic execution & token safeguards:

- Read provider-specific instructions (e.g., AI_VERSION/CLAUDE.md) after this file when present.
- Keep tokens out of source control; prefer server-side calls or secret managers for API keys.
- Default behavior: stateless interactions (no persistent in-memory conversation memory). If memory is required, store minimal state externally and encrypt or protect it.
- Rotate and scope tokens; avoid embedding long-lived secrets in running containers.
- Never commit API tokens or secrets to source control.
- Prefer server-side API usage: keep provider tokens on the backend or in a secrets manager.
- Default to stateless calls: persistent in-memory session state is disabled by default to conserve memory and protect tokens.
- If conversation memory is required for UX, store minimal state externally (database, encrypted store) and expire/rotate as needed.

## Docker:

- Generate Dockerfile and docker-compose.yml when requested.
- Configure Nginx as reverse proxy if the architecture requires it.
- Base setup strictly on uploaded docker-sample.zip or existing project templates.