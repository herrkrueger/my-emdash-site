# Handoff — my-emdash-site

Written 2026-10-01 by the Claude Code session `my-emdash-site.macstudio` before
moving repo sessions to homebase. Removing this file once its items are done is fine.

## State (2026-09-29)

- EmDash 1.0.1 is live: https://my-emdash-site.arne-krueger.workers.dev/
  - Deployed by Workers Builds from `main` (baa60d0), Worker version
    `7166b828-1a3d-4877-959a-435e45e6b245` at 100 %.
  - Previous version `5b462d5d-edeb-4efc-a739-0a2260a31067`.
- Production D1 `my-emdash-site`: migrations 001–089 applied (54 applied on 2026-09-29),
  `emdash migrate --check` shows 0 pending, 0 unknown.
- Verified after deploy: site 200, `/_emdash/admin` 302 to login,
  `POST /_emdash/api/mcp` 401 with `WWW-Authenticate` pointing to
  `/.well-known/oauth-protected-resource`.
- `main` and `emdash-1.0` point to the same commit (plus this handoff on `emdash-1.0`).

## Rollback (not executed, only if needed)

    bunx wrangler rollback 5b462d5d-edeb-4efc-a739-0a2260a31067
    bunx wrangler d1 time-travel restore my-emdash-site \
      --bookmark=0000001c-00000000-000050f5-f18d2f639e377adcafbe246e46248c65

Note: `wrangler d1 export` does not work for this database (FTS5 virtual tables);
the Time Travel bookmark above (taken 2026-09-29 ~14:43 UTC, before the migrations)
is the only backup. Time Travel only reaches back a limited window.

## Open items

1. **claude.ai connector**: add the site's MCP server in claude.ai at
   `https://my-emdash-site.arne-krueger.workers.dev/_emdash/api/mcp` (Arne).
2. **Migration API token**: the 1Password item "emdash-migrate my-emdash-site API token"
   (Employee vault) currently has D1 Edit. Reduce it to D1 Read or delete it.
3. **Cloudflare Claude Code plugin on homebase**: `.claude/settings.json` (untracked, not
   committed on purpose) enables `cloudflare@cloudflare` at project scope. On a new machine:

       claude plugin marketplace add cloudflare/skills
       claude plugin install cloudflare@cloudflare --scope project

4. **Running `emdash migrate`**: it needs `CLOUDFLARE_API_TOKEN` (wrangler's OAuth login is
   not used) and explicit selectors:

       bunx emdash migrate --from-config --check --d1=my-emdash-site \
         --wrangler-config=wrangler.jsonc --account-id=<id from `wrangler whoami`>

   In zsh, do not pass these flags via an unquoted `$VAR` (no word splitting).
5. **Merge decision**: whether to merge this handoff commit into `main` (a push to `main`
   triggers a production build) or drop it after reading.

## Noticed, not touched

- `wrangler.jsonc`: the comment on `database_id` ("paste the real ID here") is stale;
  the `r2_buckets` entry has `preview_bucket_name` on the same line as `bucket_name`.
