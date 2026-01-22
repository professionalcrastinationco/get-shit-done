<overview>
Plans execute autonomously. Checkpoints formalize the interaction points where human verification or decisions are needed.

**Core principle:** Claude automates everything with CLI/API. Checkpoints are for verification and decisions, not manual work.
</overview>

<checkpoint_types>

<type name="human-verify">
## checkpoint:human-verify (Most Common)

**When:** Claude completed automated work, human confirms it works correctly.

**Use for:**
- Visual UI checks (layout, styling, responsiveness)
- Interactive flows (click through wizard, test user flows)
- Functional verification (feature works as expected)
- Audio/video playback quality
- Animation smoothness
- Accessibility testing

**Structure:**
```xml
<task type="checkpoint:human-verify" gate="blocking">
  <what-built>[What Claude automated and deployed/built]</what-built>
  <how-to-verify>
    [Exact steps to test - URLs, commands, expected behavior]
  </how-to-verify>
  <resume-signal>[How to continue - "approved", "yes", or describe issues]</resume-signal>
</task>
```

**Note:** If the task changes user-visible UI, include an automated Playwright check in the related `type="auto"` task verification before requesting human verification.
</type>

<type name="decision">
## checkpoint:decision

**When:** Human must make choice that affects implementation direction.

**Use for:**
- Technology selection (which auth provider, which database)
- Architecture decisions (monorepo vs separate repos)
- Design choices (color scheme, layout approach)
- Feature prioritization (which variant to build)
- Data model decisions (schema structure)

**Structure:**
```xml
<task type="checkpoint:decision" gate="blocking">
  <decision>[What's being decided]</decision>
  <context>[Why this decision matters]</context>
  <options>
    <option id="option-a">
      <name>[Option name]</name>
      <pros>[Benefits]</pros>
      <cons>[Tradeoffs]</cons>
    </option>
    <option id="option-b">
      <name>[Option name]</name>
      <pros>[Benefits]</pros>
      <cons>[Tradeoffs]</cons>
    </option>
  </options>
  <resume-signal>[How to indicate choice]</resume-signal>
</task>
```
</type>

<type name="human-action">
## checkpoint:human-action (Rare)

**When:** Action has NO CLI/API and requires human-only interaction, OR Claude hit an authentication gate during automation.

**Use ONLY for:**
- Authentication gates (Claude tried CLI/API but needs credentials)
- Email verification links
- SMS 2FA codes
- Manual account approvals
- Credit card 3D Secure flows
- OAuth app approvals

**Structure:**
```xml
<task type="checkpoint:human-action" gate="blocking">
  <action>[What human must do - Claude already did everything automatable]</action>
  <instructions>
    [What Claude already automated]
    [The ONE thing requiring human action]
  </instructions>
  <verification>[What Claude can check afterward]</verification>
  <resume-signal>[How to continue]</resume-signal>
</task>
```

**Key principle:** Claude automates EVERYTHING possible first, only asks human for the unavoidable manual step.
</type>

</checkpoint_types>

<execution_protocol>
When Claude encounters `type="checkpoint:*"`:

1. Stop immediately - do not proceed to next task
2. Display checkpoint clearly and include resume signal
3. Wait for user response - do not assume completion
4. Verify if possible (files, env vars, tests)
5. Resume execution only after confirmation
</execution_protocol>

<notes>
For full examples and extended patterns, see: `~/.claude/get-shit-done/references/checkpoints.md`
</notes>
