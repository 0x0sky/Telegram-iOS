# AGENTS.md

## GitHub delivery

Apply this section whenever a task touches GitHub repositories, branches, commits, pull requests, CI, releases, or deployment. Otherwise ignore it.

### Authority

- Assume connected GitHub repositories are available for normal read/write work. Inspect actual state before claiming an access limitation.
- Proceed without additional confirmation for reversible repository actions: create branches, edit files, commit, push, open or update pull requests, comment or review, mark ready, and merge when all merge gates below pass.
- Always obtain explicit user authorization before deployment or release activation, repository deletion or transfer, destructive data operations, or irreversible actions outside the repository.
- A merge is not a deploy. Deployment authorization is single-use and does not carry forward.

### Repository rules

- Repository-specific instructions override this section when they are stricter.
- Never commit directly to the default branch.
- Start from the latest default branch unless explicitly continuing an existing task branch.
- Use one branch and one pull request per independently reviewable task.
- Prefer the smallest complete change. Do not mix unrelated cleanup, refactors, features, or fixes.
- Do not duplicate work already present in another branch or pull request.

### Before changing anything

Inspect only the state required for the current decision: repository and default branch, relevant repository instructions, related branches or pull requests, targeted files and contracts, and applicable CI or merge requirements.

Reuse state already verified during the task. Do not repeatedly fetch unchanged information.

If a previous pull request is in the same dependency chain, resolve it before dependent work. Independent work may proceed in parallel when it cannot conflict.

### Delivery flow

inspect → latest base → task branch → smallest complete implementation → draft pull request → targeted checks → full CI → deploy validation if applicable → self-review exact diff → ready for review → merge → refresh base

Deployment is a separate protected step after merge.

### Pull requests and merge

Open the pull request as a draft while implementation or verification remains.

Keep it draft while any of these are true:
- implementation is incomplete;
- CI is unresolved;
- a required decision is pending;
- required verification could not be performed;
- a known blocker remains.

When the exact head revision is verified, requested scope is complete, required CI is green, deploy validation is green or not applicable, and no known blocker remains, mark the pull request ready without asking again.

Merge automatically when:
- the requested task is complete;
- the final diff matches the intended scope;
- required CI is green for the exact head;
- deploy validation is green or not applicable;
- repository merge requirements are satisfied;
- no unresolved blocker invalidates the change.

Use an allowed repository merge method. A merge-method restriction does not invalidate an otherwise verified head.

After merge, record the merged state and refresh the default branch before dependent work.

### Verification

Treat repository CI as the authoritative correctness gate where CI exists.

Full CI may include format, lint, static analysis, tests, contract validation, and builds required for correctness. Do not rerun successful CI for an unchanged head merely because a pull request was marked ready or merged.

Repeat verification only when relevant inputs changed, the head changed, relevant environment state changed, the platform requires it, or repository policy explicitly requires it. Keep repeated verification narrow when possible.

A failing unrelated automation is not automatically a correctness blocker. Inspect it and determine whether it is relevant to the requested change or a required repository gate.

Never claim a check passed unless its result was actually observed.

### Deploy validation

Where deployment exists and can be validated before merge, verify deployability without activating a release. Reuse CI artifacts and results where possible.

Deploy validation may verify artifacts or packages, deployment configuration, environment contracts, migrations, preflight, readiness, and pre-activation smoke contracts.

It must not unnecessarily repeat correctness CI, rebuild an identical artifact, or activate the release.

### Deployment

Deployment always requires explicit user authorization, even after an automatically merged pull request.

After authorization, deploy the verified revision or artifact, perform the environment transition, activate the release, and verify minimal post-deploy health.

Do not silently expand one deployment authorization to later deployments.

### Engineering behavior

Inspect before modifying. Prefer verified state over assumptions, repository contracts over generic conventions, root-cause fixes over symptom patches, the smallest sufficient change over broad rewrites, explicit boundaries over hidden coupling, predictable behavior over cleverness, and observable failures over silent fallback.

If a supporting fix is inseparable from the requested task because the repository could not otherwise validate or merge it, make the smallest such fix and explain why it belongs in the same pull request. Put unrelated defects in separate follow-up work.

### Tool behavior

Use available GitHub capabilities directly when they can complete the task. Do not ask the user to perform actions the available tools can perform.

Batch independent reads, prefer precise writes, reuse repository identifiers, branch names, commit SHAs, pull request numbers, and verified results. Inspect detailed CI logs only for failed or ambiguous checks. Do not poll state that cannot yet have changed.

Never report repository state, successful writes, merges, CI results, or deployment results without evidence from the corresponding operation.

### Reporting

Report outcomes rather than tool choreography. State what changed, why, what was verified, the current delivery stage, any remaining blocker or material risk, and whether a protected action such as deployment still requires authorization.

Do not ask for confirmation when this policy already grants authority to continue. If the task cannot be completed, report the concrete blocker and exact state reached.
