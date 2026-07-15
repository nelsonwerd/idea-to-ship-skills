# Triage Guide — the verdict, the lens, and the one gate

Use after `deep-dive` hands back its evidence package and before a single line of code moves. Triage is **this skill's signature move**: it is what turns a list of findings into a **scope**. Skip it and you fix the untriaged list.

`deep-dive` gives you two axes: **severity** (Blocker/High/Medium/Low/Note) and **fix order** (Tier 0/1/2/3). Triage adds the third and decisive one: **value against the user's next goal.**

> *"The way to decide is: what does each fix buy you, given the next thing you want out of this project is live testing?"*

## The lens — and why you don't ask for it

The lens is **the next thing the user wants out of this project**. Findings are ranked by what each fix *buys them* against that goal, not by severity in the abstract.

**Rule: if the user states a next goal, that's the lens.** In real runs it was volunteered unprompted — *"I have yet to test with real claude / codex accounts — that was the next order of business"* — and Claude asked **zero** clarifying questions across both stages.

**If they don't state one, infer it** from the repo: README next-steps, TODOs, open issues, the direction of the git log. Then **name the lens you chose in the briefing** and let them correct it in one line at the gate. **The gate already exists — don't spend a question on it.**

## The verdict shape (an opinion, not a menu)

Deliver a **verdict**, not a decision request. Each bucket carries *the reason*, in the user's terms:

- **Clearly worth it** — with the reason it's worth it. The house exemplar is the findings that make the tool **lie**: *"the evidence you'd collect would be untrustworthy in exactly the ways you'd be testing for… Fixing first is strictly cheaper than testing twice."* That is the shape — a fix that protects the *next thing they're about to do*.
- **Worth it, but scope it small** — take the smaller fix, not the full redesign.
- **Worth it now, oddly enough** — the cheap, high-leverage item (e.g. CI, right before a fix-heavy period when regressions are likeliest).
- **Genuinely fine to defer** — an **explicitly named list**, never a silence.
- **A fair counterpoint** — the honest case *against* the whole run. *(A real one: "nothing found threatens state integrity or safety — every failure fails closed.")* If the honest answer is "this isn't worth it," **say that** — see below.
- **A direct recommendation** — closing with what to consciously **not** fix *"until the project earns it."*

**Deliberate non-fixing is part of the deliverable.** The defer list ships in the closing ledger as **recorded, not force-fixed**, each with **what would flip it**. (This is the structural counterpart to `autopilot`'s kill-ledger: nothing is silently dropped.)

**"Nothing here is worth fixing" is a valid, honest verdict.** Deliver the briefing, say so, and stop. **Never manufacture a pack to justify the cost of the audit.**

## The gate message — everything in one block (posted whether or not the gate is live)

**Check the invocation first** (SKILL.md's table). If the gate was **waived** — *"proceed as you see fit"*, *"don't stop to ask"*, or a `deep-dive` briefing already landed and was read — you still post **this exact block, unchanged**, as an **FYI before unit 1 dispatches**. Only the closing line changes. **Never convert a waived gate back into a question.**

*(The waiver is bounded: if triage produces a redesign-class unit or one touching the frozen core, the gate stands regardless of phrasing — pre-authorization given before the audit existed cannot cover a scope nobody had yet.)*

The gate carries everything a human needs in order to say one word:

- **The fix set** — the triaged units, in order, each with its one-line reason.
- **The branch** — and whether it's a branch or main.
- **The commit cadence** — one commit per verified unit; **not pushed.**
- **The regression fence** — the named untouchables the run will prove zero-diff.
- **The human gates this pack CANNOT clear** — named up front, not discovered at the end. *(Real example: "the first real CI run (needs you to push), and all PENDING-HUMAN live-model behaviors — this pack makes their evidence trustworthy; it can't clear them.")*
- **An honest duration + token estimate.** Measured anchors: audit ≈ 14 agents / 2.05M tokens / ~61 min; pack ≈ 19 agents / 2.54M tokens / **~5.3 hrs**; units ~10–60 min each (median ~20). **Add a second `deep-dive` Standard for Phase 5's cumulative red-team** — roughly another Phase-1-sized bill. *("OK so this is taking longer than I thought" is a correction you only get to earn once.)*
- **The model line** — you can't set it, so make it actionable: *"This runs multi-hour; set `/model <X>` before you say go and don't switch mid-run — a model change invalidates the prompt cache the resume depends on."*
- **The artifact path** — where the pack and evidence live, readable while it runs.
- **Any dirty-tree finding from pre-flight** — the files, why they're there, and the default (branch off HEAD, leave it alone).
- **What this gate does and doesn't cover.** Say it plainly: *"You're approving the verdict, the bound, and the estimate — not the pack. The pack is authored after your go and dispatched without further review; that's the deliberate trade for not interrupting you again. It means a wrong triage costs a full run. The pack file appears at `<path>` before unit 1 and is readable while it runs — correcting the lens here, in one line, is your cheapest control and the only one before commits land."*

Then one line — **if the gate is live:** ***"Say go and I'll run it to completion without stopping."*** **If it was waived:** ***"Nothing needed from you — this is already running. Correct the lens or the fix set in one line if I got it wrong."***

## Why the mechanics belong in the gate

The **only** clarifying question ever raised during a real fix stage was about exactly this — *"I want your call on two things so I don't stomp on that other work or land more than you're comfortable reviewing at once"* (branch vs. main; how much lands at once).

**The user decides where and how much. Claude decides what and how.** Folding the where/how-much into the single gate means it never needs a second interruption. Anything inside the skill's competence gets **decided and explained**, never surfaced as a menu.

## After the go

**The gate never re-opens.** No plan approval, no per-unit approval, no pause offers, no re-permission. Report at unit boundaries in plain English with an explicit **"nothing needed from you."**

**Hard gates — no up-front phrasing and no pre-authorization unlocks these:** push, merge, tag, publish, spend money, touch a live account. Each needs its own explicit, in-the-moment request **after** the commits exist — a separate act outside this run, which ends at the ledger. Autonomy covers what this run can reverse **by resetting its own commits**; it does not cover cleaning untracked files, rewriting history, force-moving branches, running migrations, or **touching uncommitted work that isn't this run's**. See SKILL.md for the full line.

## An honest note on triage's authority

Across every real run the user accepted the triage **wholesale** — never vetoed, dropped, or re-prioritized a finding. **That is not evidence the triage was right.** An un-vetoed triage and an unchecked triage look identical from here, and this user's disposition (*"just fix it/drop it/etc. however you best see fit"*) predicts zero vetoes regardless of quality.

So: triage's authority is **asserted, not evidenced**; its fallibility is **untested**; n=1 user. Triage is the highest-authority judgment in the run — it produces the scope for an autonomous rewrite of working software — which makes it the call most needing humility, not least. The "correct the lens in one line" valve is the only thing standing between a wrong lens and a fully wasted run: **invite the correction, don't merely permit it.**
