# Running career-ops in Claude Cowork (and Claude Code)

A practical, copy-paste guide to setting up **[career-ops](https://github.com/santifer/career-ops)** — an open-source AI job-search command center — inside **Claude Cowork**, so you can run it without living in a terminal. Plus the customization patterns that turn the generic template into a setup that fits a non-standard, multi-track senior search.

> **Heads up:** This is an independent community implementation guide. It is **not** affiliated with Anthropic or with the career-ops author. career-ops is created and maintained by [santifer](https://github.com/santifer/career-ops); all credit for the system itself goes to that project. This repo only documents *how to run it inside Cowork* and the customization patterns that made it fit a real search.

---

## TL;DR

Job search at the senior level is mostly admin, not strategy. career-ops turns job-search ops into one workflow: score a role, tailor a CV, scan portals, track the pipeline. It was built for the **Claude Code CLI**. You can also run it inside **Claude Cowork** — Anthropic's desktop "co-work with files" mode — so people who don't live in a terminal can use it too.

This guide is the implementation walkthrough plus the **customization patterns** that turn a generic, FAANG-leaning template into something that handles multiple career tracks at once (for example: an impact / mission lane, a public-sector / UN-system lane, and a high-growth startup lane). **Total setup time: about an hour.**

---

## Contents

1. [What career-ops actually is](#what-career-ops-actually-is)
2. [Cowork vs Claude Code — which surface to use](#cowork-vs-claude-code--which-surface-to-use)
3. [Setup: 6 steps, ~1 hour](#setup-6-steps-1-hour)
4. [Where the value is: the customization patterns](#where-the-value-is-the-customization-patterns)
5. [What an evaluation looks like](#what-an-evaluation-looks-like)
6. [Lessons learned](#lessons-learned)
7. [Copy-paste templates](#copy-paste-templates)

---

## What career-ops actually is

career-ops is an open-source job-search command center built on top of any Claude- or Gemini-compatible coding CLI. It does six things well:

1. **Evaluates** every job across weighted dimensions (CV match, "North Star" alignment, comp, cultural signals, red flags, and a global score) plus a posting-legitimacy check that flags ghost listings.
2. **Tailors your CV** per posting, exporting an ATS-clean PDF from a Playwright-driven HTML template (no decorations, no fancy fonts ATS parsers choke on).
3. **Scans portals** — Greenhouse, Ashby, Lever, and a list of pre-configured companies — to surface new openings without you opening a single tab.
4. **Batch-evaluates** — hand it a list of URLs, come back to a ranked shortlist with reports.
5. **Tracks** the whole pipeline in markdown tables that survive any laptop migration and never lock you into a SaaS.
6. **Coaches negotiation and interviews** with STAR+Reflection stories accumulated across evaluations.

The thing that makes career-ops genuinely useful isn't any one feature — it's the **opinionated structure** behind it: a `CLAUDE.md` that teaches the agent the workflow, a `modes/` directory of skill-specific playbooks, a `config/profile.yml` for your identity and targets, a `modes/_profile.md` for narrative and policy, and a `portals.yml` for the company list. Every customization lives in one of those files, and the agent reads them before doing anything.

That's why it works. Most "AI job search" tools are wrappers around a single prompt. **career-ops is a workflow, written down.**

---

## Cowork vs Claude Code — which surface to use

career-ops was built for the [Claude Code CLI](https://www.anthropic.com/claude-code). Power users love it. But "open Terminal and type slash commands" is a barrier for a lot of people who could otherwise benefit — people deep in a job search who don't write code daily.

[Claude Cowork](https://www.anthropic.com/news/cowork) is Anthropic's "select a folder, talk to Claude, watch it edit files alongside you" mode. Same underlying agent, friendlier surface. You don't run slash commands from the shell — you talk to Claude, and Claude reads `CLAUDE.md` and runs the right mode.

| | **Claude Code (CLI)** | **Claude Cowork (desktop)** |
|---|---|---|
| Surface | Terminal, slash commands | Desktop app, chat + file sidebar |
| Best for | Comfortable in a terminal | Anyone who isn't |
| Folder access | Native | Native (mounts your project folder) |
| Runs `node`/`npm` | Local shell | Sandboxed Linux shell |
| Playwright (PDF + scan) | Local | **Runs on your local machine**, not the sandbox |
| Human-in-the-loop | You | You — review every PDF and every application before submit |

**Why Cowork is a good fit for career-ops specifically:**

- **Native folder access.** Cowork mounts your project folder, so the agent can read `cv.md`, write reports to `reports/`, generate PDFs into `output/`, and update `data/applications.md` directly — you see the diffs in a sidebar instead of a `git diff`.
- **A sandboxed Linux shell** that runs `node` and `npm` for the parts of career-ops that aren't pure agent work — the portal scanner, the merge scripts, the validation utilities.
- **A second human in the loop.** You review every PDF before it goes out and every application before you click submit. career-ops' ethical-use rule ("never click submit without the user reviewing") is reinforced by Cowork's pace.

**The one Cowork caveat:** Playwright's Chromium binary lives in your computer's filesystem, not in Cowork's Linux sandbox. So **PDF generation and portal scanning run on your local machine via the shell**, not inside Cowork's sandbox. That's a one-line caveat, not a blocker.

---

## Setup: 6 steps, ~1 hour

### Step 1 — Prerequisites

```bash
# macOS: install Node.js (22+) and Git
brew install node git

# Verify
node --version   # v22.x or higher
git --version
```

On Windows, install Node from [nodejs.org](https://nodejs.org) and Git from [git-scm.com](https://git-scm.com/download/win) — the defaults work.

### Step 2 — Clone career-ops

```bash
cd ~/Documents/Claude/Projects
git clone https://github.com/santifer/career-ops.git
cd career-ops
```

Put it under `~/Documents/Claude/Projects/` (or wherever you keep folders Cowork can see) — one folder per project, easy to mount.

### Step 3 — Install dependencies

```bash
npm install
npx playwright install chromium     # ~165 MB Chromium binary for PDF rendering
npm run doctor                      # validates the whole setup
```

`doctor` runs a series of checks — Node version, deps, Playwright, fonts, required files. If anything's red, the error message tells you what's missing.

### Step 4 — Configure your profile

career-ops reads four files for personalization. The schemas below are what you reuse; fill them with your own content. **Sanitized, copy-paste versions live in [`examples/`](examples/).**

- **`cv.md`** — your full CV in plain markdown. Don't fancy it up; the template reformats it per role.
- **`config/profile.yml`** — structured identity + targets (name, contact, target roles, archetypes, comp band, location, visa). See [`examples/profile.example.yml`](examples/profile.example.yml).
- **`modes/_profile.md`** — narrative, policy, and scoring overrides. **This is where the system gets opinionated *about you*** — it's where the real work is. See [`examples/_profile.example.md`](examples/_profile.example.md).
- **`portals.yml`** — the company list and search queries. The default ships with a set of AI-platform companies; swap in the ones that fit your lane(s). See [`examples/portals-search-queries.example.yml`](examples/portals-search-queries.example.yml).

> **Important — user layer vs system layer.** career-ops separates *system-layer* files (`modes/_shared.md`, the `*.mjs` scripts, the auto-pipeline logic) from *user-layer* files (`cv.md`, `config/profile.yml`, `modes/_profile.md`, `portals.yml`). System files get auto-updated when you run `node update-system.mjs apply`; user files never do. **Put your customizations in the user-layer files** so a system update never overwrites them.

### Step 5 — Open the folder in Cowork

Open the Claude desktop app, switch to Cowork mode, and select your `career-ops` folder as the workspace. Cowork auto-loads `CLAUDE.md`, which teaches Claude the entire career-ops workflow.

*(On Claude Code: just run `claude` from inside the folder — same `CLAUDE.md`, same workflow.)*

### Step 6 — First prompt

Once the folder's connected, paste this prompt (also in [`examples/first-prompt.md`](examples/first-prompt.md)):

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

Claude reads `CLAUDE.md`, finds the onboarding workflow, asks you one structured question at a time, and writes your answers straight into the config files. That's the whole install.

---

## Where the value is: the customization patterns

career-ops out of the box is built for a Senior AI Engineer at a US-based unicorn. That's probably not you. The system is *designed* to be customized — its own README says so:

> "It will work out of the box, but it's designed to be made yours. If the archetypes don't match your career, the modes are in the wrong language, or the scoring doesn't fit your priorities — just ask. You (the AI agent) can edit the user's files."

Here are five patterns that turn the generic template into something that fits a real, non-standard search. They're written generically — adapt the specifics to your own lanes.

### 1. Multiple career tracks that don't share a job board

Say your search spans more than one track — for example a private-sector AI lane **and** a public-sector / UN-system lane. The default scanner targets Greenhouse, Ashby, Lever, and Workable. That covers ~95% of US tech hiring and ~0% of public-sector hiring. Public-sector and UN agencies post on aggregators and agency portals that don't expose a Greenhouse-style JSON API.

The fix: add a parallel `*_search_queries:` block to `portals.yml` that uses **WebSearch** against those domains instead of the API scanner.

```yaml
# Example: a WebSearch-driven block for boards with no public API
extra_search_queries:
  - name: Public sector — IT / Digital / Innovation
    query: 'site:example-jobs-board.org "Innovation" OR "Digital" OR "AI" OR "Data"'
    enabled: true
  # ...more per board
```

WebSearch is less reliable than an API hit — results can be stale — so treat it as a safety net while you also browse those boards manually once a week. **Belt and braces** is the right strategy when one of your tracks lives on completely different infrastructure.

### 2. A per-track seniority filter override

You might want a strict seniority filter for one track (Head/Director/Principal/VP) but a relaxed one for another. Write the override into `modes/_profile.md`:

> "Seniority filter relaxed **for this track only** — any role within [the target network] is acceptable, not just senior grades, because roles there are scarce and breaking in is itself valuable."

The system reads `_profile.md` after `_shared.md`, and per-track instructions in `_profile.md` win. So a mid-level role in your priority network doesn't get penalized on the seniority dimension, while the same level at a generic company does.

### 3. A comp floor that's a *signal*, not a contract

If you'd take a below-market mission-aligned role but don't want the system auto-rejecting those listings, set a deliberately low walk-away floor — and make clear (in `_profile.md`) that it's a **screening permission, not an expectation**:

> "Don't auto-reject mission-aligned roles for being below market — score them on mission, then I'll negotiate."

To stop the floor from quietly becoming the ceiling, pair it with **negotiation scripts** in `_profile.md` that do the actual asking when an offer materializes — e.g. *"Mission alignment is why I'm here, not what I'm paid in. Let's build a package that reflects market for the role."* The system uses these when drafting follow-ups and offer responses. The floor unblocks the screen; the scripts protect the number.

### 4. Graduated / asymmetric relocation scoring

Most people are either "I'll go anywhere" or "I'm staying put." If your real policy is more nuanced — *"I'll cross a continent for the right role; I won't cross a state for a worse one"* — encode it as **graduated scoring** in `modes/_profile.md`:

```
- Hybrid in home city                       → Remote dimension scored 5.0
- Fully remote international                 → 5.0
- On-site international with relocation      → 4.0
- Hybrid in a city you'd tolerate           → 3.5
- Forced domestic relo without support      → 1.5
- On-site in a city you don't want          → 1.0
```

The auto-pipeline reads this when scoring the Cultural / Remote dimension, so a strong role in an unwanted city *correctly* gets dragged below a slightly weaker role you can do from your desk. The system reflects the trade-off the way you'd actually make it.

### 5. A cross-cutting positioning frame + an adaptive framing table

The single most valuable line in `_profile.md` is a one-sentence positioning frame — the thing that makes you the *rare combination*, not a generalist. For example:

> "You are a [rare-combination descriptor] — most candidates have either X or Y; you have both, plus [differentiator]. Frame yourself as the rare combination, not as a generalist."

Below it, add an **adaptive framing table** — one row per archetype, with the proof points the agent should pull from `cv.md` for each:

| If the role is...        | Emphasize...                                          | Source            |
|--------------------------|-------------------------------------------------------|-------------------|
| Archetype A              | proof points relevant to A                            | cv.md sections    |
| Archetype B              | proof points relevant to B                            | cv.md sections    |
| Archetype C              | proof points relevant to C                            | cv.md sections    |

When career-ops generates a tailored CV summary, it reads this table, picks the row matching the archetype it detected in the JD, and rewrites the opening paragraph accordingly. **Same CV, several different first impressions.**

---

## What an evaluation looks like

After the configs are dialed in, paste a job URL into Cowork chat and say *"evaluate this."* The auto-pipeline runs:

1. **Verify** the listing is live (Playwright on your local machine, or WebFetch inside Cowork's sandbox marked `unconfirmed` so you double-check before applying).
2. **Score** the role across the weighted blocks (CV match, North Star alignment, comp, cultural signals, red flags, global) plus the posting-legitimacy check (real opening / proceed with caution / suspicious).
3. **Save** a report to `reports/{nnn}-{company-slug}-{date}.md`.
4. **Write** a one-line TSV to `batch/tracker-additions/` ready to merge into `data/applications.md`.
5. **Generate** a tailored ATS-clean PDF in `output/` if the score clears your apply threshold (the ethical-use rule says don't bother below it).
6. **Suggest** the next action: apply, ask for feedback, draft an outreach message, or skip.

For top-scoring roles, the system flags the proof points to lead with, the negotiation script that fits, and red flags worth asking about in the screen. For borderline cases, it asks what specific reason justifies applying. For clear misses, it recommends skipping. **That last one matters:** the default failure mode of senior-level job hunting is over-applying. A good agent pushes back.

---

## Lessons learned

1. **The system is only as opinionated as your `_profile.md`.** Out of the box it's a generic template. The customization file is where you encode *your* search. Empty `_profile.md` → the agent guesses. Specific `_profile.md` → the agent stays in your lane.

2. **Don't fork the source repo — customize the user-layer files.** Putting your archetypes in `modes/_shared.md` means losing them on every `update-system.mjs apply`. Put them in `modes/_profile.md` and they're permanent.

3. **Score floors are signals, not contracts.** A low floor is a screening permission. The negotiation scripts in `_profile.md` do the actual asking when an offer materializes.

4. **Treat the scanner as one of several discovery channels, not the only one.** It scans Greenhouse / Ashby / Lever well. It can't scan every board, and it can't read your LinkedIn DMs. Run the scanner weekly, paste off-API URLs manually, and forward warm intros into the auto-pipeline. The system compounds on multi-channel input.

5. **The ethical-use rule is the most important feature.** career-ops will never click submit. Every PDF, every cover note, every form fill is a draft you review. That's not a limitation — it's the design. AI-assisted job search at scale is irresponsible if the human ever stops reviewing. The system enforces that pace. **Quality over quantity:** a well-targeted application to 5 companies beats a generic blast to 50.

---

## Copy-paste templates

De-identified, ready-to-adapt examples live in [`examples/`](examples/):

| File | What it is |
|------|------------|
| [`profile.example.yml`](examples/profile.example.yml) | The `config/profile.yml` schema with placeholder values |
| [`_profile.example.md`](examples/_profile.example.md) | The narrative / policy / scoring-override patterns (multi-track, comp floor, relocation, positioning frame) |
| [`portals-search-queries.example.yml`](examples/portals-search-queries.example.yml) | A WebSearch-driven query block for boards with no public API |
| [`first-prompt.md`](examples/first-prompt.md) | The onboarding prompt to paste into Cowork or Claude Code |

---

## Credits & license

- **career-ops** — the system this guide is about — is by [santifer](https://github.com/santifer/career-ops). Go star it.
- **Claude Cowork** and **Claude Code** are by [Anthropic](https://www.anthropic.com).
- This guide is released under the [MIT License](LICENSE). Use it, fork it, adapt it for your own career path. If you set this up for a non-template search, contributions documenting *your* customization patterns are welcome.

*Maintained by [woolly-earth](https://github.com/woolly-earth). Not affiliated with Anthropic or the career-ops project.*
