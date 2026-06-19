# First prompt

After you've cloned career-ops, installed dependencies, and opened the folder in
Claude Cowork (or run `claude` from inside it), paste this prompt to kick off the
guided onboarding. Claude reads `CLAUDE.md`, finds the onboarding workflow, asks
you one structured question at a time, and writes your answers into the configs.

```
I just finished installing career-ops. CLAUDE.md, modes/, the configs, and
cv.md are all in place. Please:

1. Read CLAUDE.md and the relevant files in modes/ so you know the workflow.
2. Read my cv.md and tell me in 2 sentences what my strongest positioning is.
3. Walk me through filling in config/profile.yml — ask me one question at a time
   (target roles, seniority, salary band, remote/hybrid, locations, deal-breakers)
   and edit the file as we go.
4. Suggest 3–5 companies to add to portals.yml based on my CV and target roles.
5. Then show me how to evaluate my first job posting — I'll paste a URL.
```

## Follow-up prompts you'll use a lot

- **Evaluate a role:** paste a job URL and say `evaluate this`.
- **Batch:** paste several URLs and say `batch-evaluate these and give me a ranked shortlist`.
- **Scan portals:** `scan my portals for new roles` (runs the zero-token API scanner).
- **Tailor a CV:** `generate a tailored PDF for the <company> role`.
- **Tracker:** `show me my pipeline status`.
- **Customize:** `change the archetypes to <your tracks>` / `relax the seniority filter for <track>` — the agent edits your user-layer files directly.
