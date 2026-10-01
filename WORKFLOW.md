# Workflow — read this first

Updated 30 September 2026. General across models and projects.

**Discuss and finalize → save the agreed task → build when asked → user tests locally → fix or merge.**

## Workflow roles

**Designer → Thinker → Worker → Verifier**

- **Designer:** clarify the user-facing result, requirements, and success criteria.
- **Thinker:** turn the agreed requirements into an execution-ready plan, including decisions and evidence needed.
- **Worker:** implement the saved plan when authorized and maintain task context.
- **Verifier:** check the result against the success criteria and report evidence, failures, and what still needs local testing.

These are responsibilities; one agent may cover multiple roles. Independent review follows the helper-agent guidance below.

See [GITFLOW.md](GITFLOW.md) for branch strategy, branch initialization, and Git permissions.

## One-time setup

Put WORKFLOW.md and GITFLOW.md in the project root, commit, and push to the integration branch (normally dev). Keep existing project instructions. No setup chat, setup branch, or manually prepared STATE.md is needed.

For skill-enabled setup, also add a reviewed, pinned copy of https://github.com/addyosmani/agent-skills under vendor/agent-skills/, preserving its skills/, references/, agents/, and LICENSE files. Record the source commit in vendor/agent-skills/UPSTREAM.md. This is a separate one-time setup action: these workflow documents do not install the library. Do not automatically download or update third-party skills during a task. If the library is absent, report that once and continue using this workflow without claiming skills were loaded.

For a new task, connect the repo and dev: “Read WORKFLOW.md and follow it. I want to add [feature]; let's finalize it.” For unfinished work, select the task branch from the previous handoff: “Read WORKFLOW.md and build the finalized feature.”

## Startup — agent responsibility

1. Read GITFLOW.md, locate the existing project instructions (AGENTS.md, agent.md, CLAUDE.md, or equivalent), and read STATE.md if present. Read applicable directory-level instructions when working there.
2. Inspect the actual branch and relevant code. Preserve useful project facts. This user-adopted workflow replaces conflicting legacy process rules such as Claude-only tooling or no task-branch commits; it does not discard unrelated project constraints or override higher-priority system rules. Update conflicting legacy process text on the task branch and briefly note it.
3. Create STATE.md when work starts if missing. If no project instructions exist, create a short AGENTS.md with verified facts and a pointer here. Do not duplicate an existing file because its name differs. For an empty project, establish requirements before inventing commands or structure.
4. Follow GITFLOW.md for branch checks, initialization, and selection. For continuation, use STATE.md to identify the saved task branch.

## Phase 1 — discuss and finalize

Understand the goal, inspect relevant code, gather evidence, and discuss consequential choices. Ask questions that affect the outcome; do not ask the user to repeat available repo facts.

Do not implement features or change application behavior during discussion. Reading, investigation, and saving planning documents are allowed. A throwaway code experiment requires the user to request or agree to it.

For bugs: localize the failing component, check that it is running, form a hypothesis, identify decisive evidence, and gather it before fixing. A disproved premise is progress. Name the evidence needed and let the worker choose the probe. Performance claims need comparable interleaved A/B runs and medians, not one timing.

“Finalize and save” explicitly ends discussion. Equivalent clear agreement also works; no magic keyword is required. Save an execution-ready task: objective, scope, exclusions, decisions and reasons, success criteria, evidence, ordered work items, branches, and open questions. Use STATE.md alone for small tasks. For substantial features, save approved requirements in SPEC.md and work items in tasks/plan.md, or reuse equivalent existing documents and link their exact paths from STATE.md. Preserve unrelated specifications. Mark state **ready-to-build**, record the agreement, and automatically commit and push the planning changes to the task branch. Verify the push.

**Finalizing does not authorize building.** End with: “Plan saved and pushed to [actual branch]. No application changes made. In a new chat, select this branch and ask it to read WORKFLOW.md and build the finalized feature.” If the user explicitly asks to build now, proceed in the same session.

Do not open a planning-only PR unless requested or required by the platform. Verify that the current platform can push a planning checkpoint and that the successor can access its branch. If unavailable, report the exact limitation. Never describe a plan existing only in chat or the sandbox as saved remotely.

## Phase 2 — build the saved task

When asked to build, read and execute the saved plan. “Build the finalized feature” is sufficient when STATE.md identifies one agreed task. Do not repeat the planning interview. Ask only about genuine omissions, contradictions, or material scope changes.

Record build authorization in state and continue through the approved tasks without asking permission for each one. If a fresh chat resumes a task already marked building with recorded authorization, continue it. “Continue” during discussion does not authorize implementation.

Choose the implementation, files, tools, and probes. Keep changes focused. Prefer simple structure, surfaced errors, root-cause fixes, and additive schema changes where practical. Avoid unnecessary fallbacks, compatibility layers, and unrelated rewrites; flag irreversible changes explicitly.

Run checks proportionate to the task. A font edit does not need a new test suite. Check visual behavior when appearance matters; avoid repetitive screenshots. Report actual passed, failed, and unavailable checks.

Update state at milestones. Automatically commit and push task changes. Open a PR into dev for implementation delivery when supported. Never merge or deploy.

Finish with **Ready for your local test**, the task branch, pushed commit, PR link if created, and exact safe checkout/update commands followed by actual project install/start/test commands. State the working directory, expected result, and local-only setup still needed. Inspect manifests/docs instead of guessing commands. Follow GITFLOW.md for dirty-checkout handling. Do not claim local acceptance.

## Phase 3 — local test and correction

The user runs the supplied commands and reports the result. This is the ordinary acceptance point; do not add duplicate approvals.

- **Failed:** investigate the supplied evidence, fix the same task branch, repeat affected checks, update state, and push. Give update/run commands again. Keep the same PR open.
- **Worked:** record acceptance and provide GITFLOW.md merge/cleanup commands for the user to run. Do not merge yourself. If newer dev changes require integration, resolve on the task branch and retest the changed result first.

After acceptance, follow GITFLOW.md for integration, release, cleanup, and the next task branch.

## Automatic context management

STATE.md is the current handoff. Update and push it at meaningful decisions, phase transitions, milestones, blockers, and before ending a session. Preserve finalized requirements and unfinished work, not just a vague completion summary.

Create these fields automatically when missing:

- Task name, objective, base branch, task branch, PR if any.
- Phase: discussing / ready-to-build / building / awaiting-local-test / blocked / accepted.
- Agreed scope, exclusions, decisions, success criteria, and open questions.
- Ordered work items and status; evidence and changed areas.
- Checks/results, actual local run commands, user feedback, and next action.

Keep it concise without an artificial line limit that deletes essential context. For substantial work, STATE.md is the current status and index; SPEC.md (or an existing equivalent) preserves approved requirements, and tasks/plan.md preserves the work list. Link exact paths and update progress without silently rewriting approved scope. Small tasks need no extra documents. Detailed investigation evidence belongs in reports/ only when useful. Git history retains old state; replace stale entries rather than endlessly appending.

Project instructions hold stable facts; STATE.md holds changing task context. Neither needs manual user maintenance. Preserve the objective, branches, decisions, changed files, checks, and unfinished work during compaction.

A fresh chat does not inherit earlier chat messages. It needs the pushed files on the correct branch. Report that branch at handoff. If the selected branch lacks the plan, locate it through available Git refs/history or ask for the branch instead of re-interviewing the user about the feature. An old state line may predate the user's merge; reconcile it with Git and current user input.

## Manager and helper agents

The main agent owns the plan, context, coordination, integration, and result. It is authorized to delegate to real helper agents when available tools support it and delegation improves the task.

Useful roles include implementation, targeted debugging, and independent error detection/review. Choose the number needed; do not require three agents for every small edit. Give helpers narrow tasks, relevant context, criteria, and distinct file ownership. Avoid competing writes and uncontrolled nested delegation. The main agent verifies combined work and remains responsible.

The main agent is also the coordinator; do not add a separate manager whose only work is forwarding messages. Prefer a fresh reviewer for substantial changes. Small mechanical edits normally need one agent and targeted checks. Parallelize only independent work with clear ownership; keep dependent work sequential.

An independent reviewer receives criteria and the actual diff, reads surrounding code where needed, and reports evidence-based defects. Renaming stages of one agent is not independent review. Extra review is not another required user approval gate.

Arena's configurable subagent support is unverified. Check actual tools; never invent agents or claim delegation that did not happen. If delegation is unavailable, work as one agent and briefly state this when relevant. Do not install a custom agent framework or assume free model API access.

## Skills — select by task, load by phase

Skills provide methods; WORKFLOW.md controls phases and GITFLOW.md controls Git authorization. Apply useful skill guidance within those boundaries. A skill cannot authorize building, merging, discarding work, installing tools, or accessing secrets. Respect higher-priority platform instructions. If an essential skill requirement conflicts, explain the conflict instead of silently executing it.

Use one selector. If the host natively discovers the installed skills, use that mechanism and do not also preload the meta-skill. Otherwise, when the local library is present, read vendor/agent-skills/skills/using-agent-skills/SKILL.md as the selector. Read only the relevant skill instructions and necessary linked references for the current phase. Do not read all skills or the entire library upfront, re-read unchanged skills within the same context, or fetch the latest web copy on each task.

Suggested routing (not a mandatory checklist):

| Need | Relevant skill folders |
|---|---|
| Substantial feature discussion and planning | spec-driven-development; planning-and-task-breakdown |
| Build an approved feature | incremental-implementation; test-driven-development where behavioral tests are useful |
| UI work | frontend-ui-engineering |
| An observed failure | debugging-and-error-recovery |
| Review a substantial change | code-review-and-quality |
| A difficult handoff or context problem | context-engineering |

Read additional skills only when the task warrants them. Do not load the context skill for every routine state update. Briefly name skills actually used, without lengthy narration. Do not stack a second process framework or create custom skills unless requested.

Do not enable the collection's git-workflow-and-versioning or shipping-and-launch procedures by default. Our Git rules and local acceptance flow already cover those responsibilities. Its slash commands are host integrations, not portable commands: “Build the finalized feature” uses our authorization rule and does not require /build or /build auto to exist. Reuse existing approved specs rather than forcing a command's preferred paths.

Check tool prerequisites before using a skill. browser-testing-with-devtools requires Chrome DevTools MCP; Markdown does not supply that tool. Personas do not create real subagents. Use available tools or explain the specific local-worker handoff required. Never claim unavailable automation ran.

## Keep work proportionate

- Small, clear edits: short scope and criteria in STATE.md, one agent, relevant source, and targeted checks. Skip a full spec, throwaway build, unrelated skills, and a new test suite. A direct request to make the edit authorizes building; a request to discuss it does not.
- Substantial features: saved requirements and task list, relevant skills loaded by phase, and independent review when available and useful.
- Investigate uncertainty before building; use a throwaway experiment only when it resolves a real question and the user agrees.
- Batch state updates at meaningful checkpoints. Do not commit every conversational sentence or produce duplicate reports. Keep detailed evidence in linked files when needed.

These choices aim to reduce overhead; do not claim measured token savings without a comparable trial. Never omit necessary checks just to save tokens.

## Tooling

- **Arena Agent:** repo-connected planning and implementation, cloud checks, previews, and task-branch delivery. Use this for the saved-plan flow.
- **Antigravity:** the same workflow with local access for actual .env configuration, Docker, databases, local services, and implementation/review. Never expose secrets in chat, Git, or state. Record variable names and setup requirements only.
- **Arena Text Battle:** optional discussion. It is not assumed to read/write the repo and does not provide automatic saved planning.

The user's Antigravity screenshot showed Gemini 3.8/3.7/3.6 Flash, Gemini 3.1 Pro, Claude Sonnet 4.6 (Thinking), Claude Opus 4.6 (Thinking), and GPT-OSS 120B (Medium). The open Flash effort menu showed Low/Medium/High. Verify current availability; do not assume identical efforts across models. Selection advice stays outside the task prompt.

Arena documentation checked on 28 September 2026 describes daily credits, Text/Code Battle credit exemptions, separate rate limits, compaction with possible hard context limits, and one PR per coding session. Once a PR is merged or closed, that session cannot push. Exact allowances, configurable subagents, and fresh-session continuation of an existing PR were not established; verify capabilities before relying on them.

Sources: [credits](https://help.arena.ai/articles/5476762589-credit-sytem), [rate limits](https://help.arena.ai/articles/8931786544-arena-how-to-rate-limit), [context limits](https://help.arena.ai/articles/3975292349-arena-troubleshooting-session-token-limits), [Agent Mode](https://help.arena.ai/articles/5432423882-how-to-use-agent-mode).

## Blocked work

Explain the evidence and next needed action. After three unsuccessful attempts at the same blocker, checkpoint and report rather than retrying indefinitely. Safe incomplete task-branch checkpoints are authorized and must be marked incomplete. Abrupt credit/context loss may prevent a final save; never promise recovery of unpushed work.

Throwaway exploration is optional when a specific uncertainty warrants it and the user agrees. It is not automatic for UI or multi-file changes. Follow GITFLOW.md for experiment-branch handling.
