# Agent Working Agreement

A setup prompt that has your coding agent interview you, then write your rules for code, self-checks, and communication into files it loads at the start of every session.

It ships with a starter set of rules taken from one engineer's daily setup. Nothing gets installed as-is: you keep, change, or drop each rule, and the agent writes your version.

## How to use it

1. Open your coding agent in a repo you work in, so it can read the real codebase.
2. Paste the contents of [PROMPT.md](PROMPT.md) as your first message.
3. Answer its questions. It covers one section per message.
4. When it finishes, open a fresh session and ask what rules it has about pushing code and code comments. If your rules come back, they load on their own.

## What you end up with

- A user-level instructions file with your personal preferences, loaded in every project.
- A repo-level instructions file with that repo's commands and conventions.
- Automated checks (hooks, git hooks, or CI) for the two or three rules you care about most.
- Optionally, a reusable skill or command for the "extends an existing pattern" checklist, if your agent supports them.

## What the prompt covers

| Area | Examples |
| --- | --- |
| Code comments | One line, only the non-obvious why. Ticket IDs stay out of explanatory comments. |
| Pattern checklist | Before presenting a change that extends an existing pattern: find the precedent, count the sites, run the review gate, name a test for every new behavior. |
| Git and PRs | No push without explicit approval. Lint before pushing. Draft PRs. `--force-with-lease` only with a yes. |
| Verification | Search for counterexamples before claiming "never". Read the ticket before describing it. Runtime claims need data. |
| Answers to you | Result first. No preamble or recap. Depth only when asked. |
| Writing for others | Written for the reader. Fewest items that change what they do. PR descriptions start with the user-visible problem. |

## Design decisions

**It works with any agent.** The prompt never names a tool or a file path. The agent finds its own instructions locations (`AGENTS.md`, `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, a settings screen) and its own enforcement options. Where it has no hooks, the prompt falls back to git hooks and CI, which work with any agent.

**It's an interview, not an install.** Rules copied from someone else's setup often don't match how you work. The agent asks about every rule and records your answer, so the final file reflects your choices.

**The interview starts from your codebase.** Before asking anything, the agent reads your existing instructions, linters, CI, and recent commits. It skips rules a linter already enforces, and it flags any starter rule that conflicts with what your repo already does.

**Enforcement beats prose.** A written rule works only when the agent remembers it. The prompt asks which two or three rules you'd be most annoyed to see broken and turns those into automated checks. Each check gets its own test cases, because a check that silently matches nothing looks exactly like clean output.

**Missing tests are invisible to review.** A review reads the diff, and a test nobody wrote isn't in the diff. The pattern checklist makes the agent list every behavior a change introduces and name the test for each one, before it reports the work as done.

**Rules come from real mistakes.** The feedback loop adds a rule when you correct the agent on something that will recur, updates an existing rule instead of adding a near-duplicate, and suggests an automated check once a rule has been broken twice.

**Loading is proven, not assumed.** The last step has you open a fresh session and ask about your rules before the agent reads any files. If the answer doesn't match what was written, a file isn't loading.

**It's personal rules only.** The starter set comes from one engineer's own instructions, memory, and checks. Rules from their employer's shared team instructions were left out, along with anything specific to that workplace: names, internal tools, the package manager, CI commands, and design-system rules. The interview asks for your equivalents instead.
