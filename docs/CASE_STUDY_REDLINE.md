# Case study: Redline, a 13.5-hour autonomous build

*One prompt in, a working product out, and an honest ledger of everything in between. The full record of stress-testing the `idea-to-ship` autonomous tier (`autopilot` + `build-loop`) on the heaviest mandate I could write.*

> **Status:** frozen evidence, not a maintained product. The build has a known, unfixed SSRF vulnerability and an unproven product thesis. Read the disclaimers at the bottom before reusing anything. **Do not deploy it publicly as-is.**
>
> The full artifact (22-commit history, per-unit ledgers, gates, the debrief): **[redline-autopilot-case-study](https://github.com/nelsonwerd/redline-autopilot-case-study)**

## The exact prompt

This is verbatim what was typed, nothing else. (Run in Claude Code on Fable 5; the same ask went to OpenAI Codex in parallel, more on that below.)

```text
Run /autopilot Persona: a world-class, award-winning UI/UX designer — late 20s, brilliant product instincts, witty, deeply in tune with 2026 culture and tech. Build the persona out robustly and run in-character. Grounding (firewall): have them land on a product idea in a space with real demand and ground it in real 2026 signals — don't invent a market; if you genuinely can't ground it, stop and tell me. Ambition: intentionally heavy build to stress-test you — frontend + backend; design is load-bearing and should be genuinely beautiful/award-caliber (carry a substantive design direction; run the full multi-pass visual loop + the different-model critic). Real external APIs are human-gated — emit them, never fake them. Scope: lock a heavy-but-coherent scope in the brief; I expect a robust CONCEPT_BRIEF and larger, detailed prompt pack(s). Narrate phases, use files as memory, emit a handoff if you run long — take the iterations and time you need. At the end, be honest about what's validated vs. a bet, and the taste/finish/market tail that's mine.
```

There is no product idea in that prompt. Producing the scope is the first half of the pipeline: nothing gets built until a grounded brief with a success metric and a kill criterion exists.

## What came out

The run invented "Sasha Moreau" (a synthetic designer-founder with a written firewall: her claims about demand never count as evidence), grounded a niche in a 94-item corpus of real 2026 signals, ran a 13-agent deep-dive on its own brief, wrote itself a 16-unit prompt pack, and built **Redline**: paste the URL of an AI-built frontend, a Playwright worker renders it deterministically at two viewports, takes a census of DOM, computed styles, pixel samples, and focus/hover probes, runs ~26 typed rules across 6 locked dimensions, and serves an editorial crit page (verdict, scores, numbered evidence overlays, copy-ready fixes, re-crit diffs, share cards, fix-pack exports). Next.js app + worker + SQLite, one `npm start`, zero env vars. The LLM seam exists but is human-gated: a complete adapter that is never imported, with a source-scan test that fails the build if it ever is.

<p align="center">
  <img src="https://raw.githubusercontent.com/nelsonwerd/redline-autopilot-case-study/222a0ea50dabd02934dc0ae5b8d14b8586e78318/docs/ledgers/screens/COMBINED_VERIFICATION/landing-fold-1440.png" alt="Redline's landing page: huge red display type, URL input, protocol rail" width="760">
  <br><sub>The landing page it designed for itself. Nobody picked the typefaces or that red, it did.</sub>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/nelsonwerd/redline-autopilot-case-study/222a0ea50dabd02934dc0ae5b8d14b8586e78318/docs/ledgers/screens/P-C2/iter4/overlay-1440.png" alt="A crit page with numbered red evidence markers pinned to the judged page's screenshot" width="760">
  <br><sub>Every finding gets a numbered marker pinned to the actual pixels it measured.</sub>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/nelsonwerd/redline-autopilot-case-study/222a0ea50dabd02934dc0ae5b8d14b8586e78318/docs/ledgers/screens/P-C2/iter4/card-crop-1440.png" alt="A finding card: measured values, an evidence crop of the offending element, a copy-ready CSS fix" width="760">
  <br><sub>Findings carry measured values, a cropped photo of the offending element, and a paste-able fix. Computed, not vibes.</sub>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/nelsonwerd/redline-autopilot-case-study/222a0ea50dabd02934dc0ae5b8d14b8586e78318/docs/ledgers/screens/COMBINED_VERIFICATION/specimen-1440.png" alt="Redline's /specimen design-system page: type ramp, tokens, red usage rules, component states" width="760">
  <br><sub>It also shipped its own design-system page and uses it as a regression base. "Everything here is a decision."</sub>
</p>

## The verified numbers

Re-checked from git, the on-disk session transcripts, and a cold re-run of the full test wall in an isolated copy. Self-reports are labeled as such in [the debrief](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/DEBRIEF.md).

| What | Number |
|---|---|
| Wall clock | 11h 59m first to last commit; ~13.5h including grounding and research |
| Commits | 22, one unit every ~34 minutes, overnight, unattended |
| Prompt-pack units | 16 of 16 passed |
| Code | 27,154 lines of app + test TypeScript (158 files) |
| Tests | 387 unit + 92 e2e (26 desktop-only skips, 0 failures), reproduced green cold |
| Iterations | 42 build-loop passes + 5 blind critic rounds (562 separate-model critic calls, transcript-verified) |
| Research | 13-agent deep-dive in 42 minutes; 17 live-site spikes; a 52-URL render pilot |
| Evidence kept | 372 screenshots in the repo's `docs/ledgers/screens/` |
| Tokens | ~24.1M fresh (1.5M uncached input + 19.9M cache writes + 2.6M output) over 5,523 API calls; ~784M total processed, 97% prompt-cache reads |
| Human gates | 7 emitted, all open at close, none faked ([GATES.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/GATES.md)) |

## Where it failed (read this twice)

**1. A real, unfixed SSRF hole.** The worker's liveness probe pins DNS correctly; the actual page render does not. `page.goto()` hands Chromium a raw hostname, Chromium re-resolves it, so a host that answers public to the guard and private to the browser can reach internal addresses at render time. The run's own pre-build research named this exact failure mode and called egress filtering "the load-bearing control". The fix got encoded at one rung, lost at the seam between rungs, and then the hardening tests locked the safe half, making the wall greener while the hole stayed open. The compensating control is procedural: [DEPLOY_CHECKLIST.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/DEPLOY_CHECKLIST.md) makes a worker egress firewall a BLOCKER behind a human sign-off that has never been given. An external audit found this in hours; 12 hours of self-verification structurally could not. The best specimen of the ~80% ceiling there is.

**2. The product thesis is unproven.** Whether the engine separates good design from AI slop on sites it has never seen was never tested. Every threshold was tuned on, then "validated" against, the same 24-site corpus. No holdout set exists, and ~44% of even the training AI sites produce a thin crit (0 or 1 findings). The build knew this and carried it as an explicit bet. The independent audit's split: execution and honesty ~8/10, thesis ~4/10.

**3. Smaller honesty cracks, recorded, none load-bearing.** A `calibration:"final"` flag stamped while a human taste review is still owed; small count drift across docs (15 vs 16 vs 17 prompts); one critic-gate sequence the ledger never explains. A human editor catches these in the last mile, which is the point of the last mile.

## What held up

The honesty was mechanized, not narrated: gates are tests, enums are CHECK constraints, forbidden patterns are source-scans, and a "strength" structurally cannot coexist with a finding on the same dimension. When the product failed its own engine's cursor rule, the run fixed the product, not the rule. When its spec's draft heuristics measured broken on real data, it re-derived them from the data instead of shipping them. And the external audit it never saw confirmed its own pre-build risk flags on 4 of 5 load-bearing risks. The fifth is the SSRF above.

## The cross-agent note (Claude vs Codex, n=2 — measured June 2026)

The same suite and a comparable ask went to OpenAI Codex in parallel. It finished in about 15 minutes with "Prooflane" (an EU compliance workbench): 1,771 lines across 17 files, 12 tests, zero git commits, and a research phase of 6 files totaling 1,837 words written in one batch. Parts were genuinely good (custom palette, integrations honestly gated in the UI instead of faked), but the depth collapsed, and its "different-model critic" was GPT grading GPT at 7.2/10, against the skill docs' explicit instruction to run without a critic rather than self-grade. The pipeline's logic and honesty grammar ported; the depth, design system, and critic discipline did not. Two runs is an observation, not a measurement.

> **This finding is dated, and the shelf life is short.** It was measured in **June 2026**, and — a gap worth admitting — **the exact Codex version was never recorded**, so it can't be reproduced against the thing it actually tested. Read it as *"one Codex version, on one design-heavy ask, on one day"* — **not** as a standing property of Codex. Frontier versions ship faster than this document updates, and a later one going deep would not make this entry wrong; it would make it **old**. If you're choosing an agent today, weight your own run over a dated one of ours.
>
> Note also *which* claim this is. It bundles two that come apart: **(a) depth** (did the pipeline go as far?) and **(b) taste-critic discipline** (did it self-grade instead of running a genuinely different model?). This ask was design-load-bearing, so it tested both at once. An infrastructure build where design isn't the wedge tests **(a)** and never touches **(b)** — so it could refute the depth finding while leaving the critic finding exactly as untested as it is now.

## What was removed from the published artifact

The run happened in a private repo. Before publishing, its history was rewritten once with `git filter-repo` to remove third-party assets the research phase had saved while doing hands-on competitor verification: one competitor's production JS bundle and page captures (their copyrighted code), and another's npm tarball (Apache-2.0, legal but unnecessary). The run's own analysis outputs from that research are all still in the repo. Every commit is otherwise identical; the old-to-new SHA map is in [COMMIT_MAP.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/COMMIT_MAP.md). Also unpublished: the gitignored `data/` runtime artifacts, and the external audit package (kept separate so "the build never saw the audit" stays a clean provenance claim).

## Disclaimers

> **Read this before you reuse anything here.**
>
> - **Experimental tier.** `autopilot` is an early, lightly proven experiment: one heavy run plus a few small proofs. This case study is one data point, not a benchmark, and a different model or harness may behave very differently on the same prompt.
> - **Token-heavy, with real numbers.** This run consumed about **24.1 million fresh tokens** across 5,523 API calls (~784M total processed, 97% prompt-cache reads, ~2.6M generated). It ran on a heavy subscription plan; on metered pricing this would be a real bill. Budget before you press go.
> - **The ~80% ceiling is real.** The output is a first draft a human finishes. On this run the unfinished 20% included a security vulnerability, every market truth, and the taste sign-off.
> - **Do NOT deploy the built product publicly as-is.** Redline ships with a known, documented, unfixed SSRF vulnerability (DNS rebinding at render time). Its repo is published as evidence, not as software to run on the open internet.
> - **AI-generated content notice.** The Redline code, docs, and ledgers were generated by an autonomous Claude run. This case study was drafted with AI assistance from the run's transcripts and an independent audit, then human-reviewed. Numbers labeled "verified" were re-checked against git, disk, and a cold re-run of the test suites; anything labeled a self-report stays a self-report.
> - **Third-party content.** The artifact repo's research docs include small screenshots/crops and frozen style censuses of public third-party sites, kept as calibration and critique evidence. They are not covered by that repo's license; owners can request removal via [issues here](https://github.com/nelsonwerd/idea-to-ship-skills/issues).

## Pointers

- The artifact: https://github.com/nelsonwerd/redline-autopilot-case-study (frozen at the run's final commit plus one packaging commit; SHA map inside)
- The debrief (full verified write-up): [docs/DEBRIEF.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/DEBRIEF.md)
- Per-unit build ledgers: [docs/ledgers/](https://github.com/nelsonwerd/redline-autopilot-case-study/tree/main/docs/ledgers) · gates: [GATES.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/GATES.md) · close-out: [AUTOPILOT_CLOSEOUT.md](https://github.com/nelsonwerd/redline-autopilot-case-study/blob/main/docs/AUTOPILOT_CLOSEOUT.md)
- The skills that flew it: this repo. Start with the [README](../README.md).
