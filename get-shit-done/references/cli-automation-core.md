<overview>
**Core principle:** If it has a CLI or API, Claude does it. Never ask the human to perform manual steps that Claude can automate.

This reference documents the baseline automation posture during plan execution.
</overview>

<usage>
- Default to CLI/API automation for deploys, infra setup, and third-party integrations.
- Only load detailed platform instructions when the phase explicitly includes that platform.
- If an auth gate is hit, create a dynamic checkpoint:human-action and resume after credentials are provided.
</usage>

<categories>
- Deployment platforms (Vercel, Railway, Fly, etc.)
- Payments/billing (Stripe, etc.)
- Databases/backends (Supabase, Upstash, etc.)
- Cloud storage and messaging providers
</categories>

<notes>
For detailed per-platform commands and checkpoint patterns, see:
`~/.claude/get-shit-done/references/cli-automation.md`
</notes>
