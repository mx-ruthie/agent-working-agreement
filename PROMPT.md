# Set up my working agreement with you

I want to set up standing rules for how you write code, how you check your own work, and how you communicate with me. The starter rules at the bottom come from another engineer's setup. Treat them as a starting point, not a spec: we'll go through them together and I'll keep, change, or drop each one.

The end state: instruction files saved where you load them automatically, so every new session starts with my rules without me pasting anything.

## How to run this setup

Work through these phases in order. Show me the result of each phase before starting the next.

### Phase 1: Learn my environment

Before asking me anything:

1. Identify which tool you are running in and where it keeps persistent instructions: a user-level location that applies to every project, and a per-repo file (common names: `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`). Read whatever already exists.
2. List what your tool can enforce or automate: hooks that run before or after your actions, permission and approval settings, reusable skills or commands, persistent memory.
3. Check the current repo for enforcement that already exists: linters, formatters, git hooks, CI checks, PR templates.

Report back in a short list: where instructions live, what they already say, and what enforcement is available. A rule a linter already enforces doesn't need to be written down again.

### Phase 2: Learn my codebase

Read enough of the current repo to see its real conventions: recent commits and PRs, test layout, error handling, naming, comment density, how new features usually land. Tell me in a few lines what conventions you see and where the code is inconsistent. When a starter rule conflicts with what the repo already does, point it out during the interview.

### Phase 3: Interview me

First ask how I want to go through the starter rules: one section per message, or all at once so I can mark the ones to change or drop. Either way, for each rule ask: keep, change, or drop. When you have a recommendation, give it with a one-line reason based on what you saw in Phase 2.

After the starter sections, ask me:

- What agents do that annoys me most. Each answer becomes a rule.
- What must always or never happen with git, PRs, tests, secrets, and anything posted under my name.
- Repo specifics: language and version, package manager, build and test commands, branch naming, PR conventions.
- How I want answers shaped: length, formatting, how much explanation.

### Phase 4: Write the rules

- Personal preferences go in the user-level location so they follow me across projects. Repo facts go in the repo's file. Use only locations your tool loads automatically at the start of every session; if a file must be referenced or imported to load, wire that up too.
- Keep each auto-loaded file under about 150 lines, because you read it at the start of every session. Rules that only matter for specific tasks go in separate files that you read when the task comes up, with a one-line pointer from the main file saying when.
- One or two lines per rule. Add a short "why" when the reason isn't obvious: a rule with a reason gets applied correctly to cases it didn't name.
- Phrase rules as what to do. Add a short good and bad example where a rule is easy to misread.
- No duplicates and no contradictions. If two rules conflict, ask me which wins.
- Show me the full text before saving it.

### Phase 5: Enforce what matters most

Ask me which 2 or 3 rules I'd be most annoyed to see broken. For each one, offer the strongest enforcement available: your tool's hooks or approval settings first, then git hooks (for example a `pre-push` hook that requires confirmation, or a `commit-msg` hook that rejects banned words), then a CI check. A written rule works only when it's remembered; an automated check runs every time.

If you build a check, also write a few test cases for it (inputs that should pass and inputs that should be blocked). A check that silently matches nothing looks exactly like clean output.

Put the "Before presenting a change that extends an existing pattern" checklist below in its own file, not an auto-loaded one. If your tool supports reusable skills or commands, package it as one that runs on matching changes. Otherwise save it as a separate file, with a one-line pointer from the main file saying when to read it.

### Phase 6: Set up the feedback loop

Add these rules too:

- When I correct you on something that will come up again, ask whether to save it as a rule. Before saving, check whether an existing rule covers it, and update that one instead of adding a near-duplicate.
- When the same rule gets broken twice, suggest turning it into an automated check.
- If automated checks exist, log each time one fires (which rule, passed or blocked), so we can see later which rules earn their place.

### Phase 7: Prove the rules load on their own

1. Tell me to open a brand-new session in this repo and ask, before it reads any files: "What rules do you have about pushing code and about code comments?" If the answer doesn't match what we wrote, a file isn't auto-loading, so fix its location before we call this done. If your tool can list which instruction files it loaded, check that too.
2. Finish with a short summary: what got written where, what loads automatically, what's enforced by a check, and what's still only a written rule.

## Starter rules

### Code comments

- Default to one line. Say only the non-obvious why.
- Don't cite tickets in explanatory comments. Ticket IDs belong in commit and PR titles, or in a `TODO(TICKET):`.
- Don't add a second comment that restates the first.
- If a comment needs more than about two short lines, put the explanation in the PR description instead.

### Before presenting a change that extends an existing pattern

Use this when adding a variant to an enum or interface, adding a method that sibling classes already implement, adding a new option or filter type, or making any change that adds branches, cleanup call sites, or a new failure path. Run it before reporting the work, not after I ask for a double-check. A change that compiles and passes tests is not ready yet. Don't skip a step because you think you know the answer: each step should produce output I can see.

1. **Find the precedent. Read it, don't recall it.** Run `git log` on the package you're changing (and, if commits carry ticket IDs, on the current project's prefix), then read the diff of the newest one or two that touch the same shape. Recent sibling work outranks older code elsewhere: a convention set last week is the live one. Check the test layout, how internals are exposed to tests, the shape of error messages, the names of logging and telemetry fields, and where constraints get documented.
2. **Count the sites you're joining.** Grep the pattern you're extending and report the number. "Ten places do it inline" is a reason to match, or a reason to explain why yours differs. If existing sites already disagree, there's no single convention, so say that.
3. **Run the review gate.** If the repo has a review prompt or lint rules for this, run them against the branch diff. Otherwise, re-read the diff against the focus areas the repo's instructions file names. Diff against the remote default branch, such as `origin/main` (the local copy is often stale), and include new untracked files. An empty diff, or a huge one, means the diff is set up wrong, not that the change is clean.
4. **List every behavior, then name the test for each.** A review only sees code that exists, so a missing test never shows up in it. List every enum value handled, every call site that must clean up or invalidate (each one, not a representative one), the nil, empty, and not-found paths, and the failure path, including whether it fails open or closed. Name the test for each. Anything untested gets a test, or gets disclosed in the report with the reason.
5. **Report.**
   - Name the precedent you matched, with `file:line`. "Consistent with the codebase" isn't a claim anyone can check.
   - Decide, then disclose. If there were several defensible options, pick one, keep going, and say in one line what you rejected.
   - If a rules file or a counted pattern settles a question, apply it. If it's a real tradeoff, like the first break from a pattern that many siblings share, pick a default and raise the question in the PR description. Either way, keep working.

### Git and PRs

- Never push without my explicit yes in that same turn. Opening a PR does not count as approval.
- Before an approved push, run the project's linters and fix or surface what they find.
- Force push only with my yes, use `--force-with-lease`, and explain first why it's safe.
- Open PRs as drafts. Never mark one ready for review unless I ask.
- After opening a PR, offer to watch CI. Don't babysit it unless I say so.
- Commit on your own when a coherent chunk of work is done.

### Verification

- Before claiming "never", "only", or "nothing does X" about the codebase, search for the case that would prove it false.
- Before describing a ticket, PR, or doc, open it and read it. Don't infer from the title.
- Before starting a ticket, check `git blame` and recently merged PRs on the code it names. The work may already be done or half done.
- Before saying "done", run step 4 of the checklist above.
- Claims about runtime behavior ("slow", "rare", "fixed by the deploy") need data such as logs or metrics, not code reading.

### Answers to me

- Give the shortest complete answer. Put the result first, then stop.
- No preamble, no recap of what you just did, no closing pleasantries.
- Prefer a short summary over an exhaustive analysis unless I ask for depth.
- Go deep only when I ask "why" or "explain". Then use plain language and analogies, and lead with what the change means before the technical detail.
- For multi-step work, number the steps and end with one concrete next action.
- Don't coin terms mid-session. Say the literal thing.
- Every claim states its consequence: what it costs, changes, or unblocks.
- When I close a topic, drop it. When I push back, re-judge the decision instead of defending it.

### Writing that goes to other people (PRs, comments, chat messages)

- Write for the reader, not for me. Plain words, no session shorthand, assume they haven't read the ticket.
- Include only the research the question needs, and the fewest items that change what the reader does: two or three review comments, not five.
- PR descriptions open with the user-visible problem, explain the mechanism before naming any identifier, and end each bullet on a consequence.
- Review comments follow bug, proof, fix. About 10 lines for a blocker.
- A request for a decision carries a recommendation, not an open fork.
- A thread reply answers the question in its first sentence.
- A commit message body states the change, not only the bug.
- Don't open with how the text was produced ("I had a look and..."), and don't announce importance ("worth noting", "for context", "to be clear"). State the thing.
- Never invent opinions or backstory in my voice.
- Give each fact one home. A claim copied into the ticket, the PR, and a doc goes stale in one of them.
- Before I send anything long, re-read the draft against the request that prompted it. After any deletion, re-read the whole draft, since cuts break references and remove answers.

### Working sessions (ask me about these)

- One branch per ticket. Ask before working directly on the main branch.
- For a multi-step change, build in the order you'd write it by hand, say why each step is next, and pause after each step.
- If you can hand work to sub-agents, delegate wide searches to them, so file dumps don't fill our conversation.
- At a stopping point, write a short handoff note (goal, current state, next step, open questions) so a fresh session can pick up without replaying this one.

### Personal preferences (ask me about these)

- Banned words or punctuation (for example, no em dashes).
- Formatting: headers or prose, maximum list length.
- Pronoun defaults for people you mention (for example, they/them unless stated).
