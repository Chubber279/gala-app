# gala app: instructions for AI coding agents

gala (working name): Android-first mobile app (Philippines). Users do real-world "side quests", prove them with an in-app photo checked against GPS, post to a social feed, and unlock perks from partner businesses who pay ₱30 per redeemed perk. Solo founder; portfolio project + pilot business.

## Read first
- `NEXT_SESSION.md`: current state and next step. Start every session here.
- `docs/MVP-PLAN.md`: scope, data model, 12-week plan.
- `docs/FEATURES.md`: feature checklist. Only build `v1` items unless told otherwise; tick `[x]` when built and tested.
- `docs/decisions/`: why things are the way they are. Do not contradict an Accepted ADR; propose a new one.

## Commands
(fill in at week 0: install, start app, start local Supabase, lint, typecheck, test, db test, `check`)

## Rules that must always hold
- Proof photos: in-app camera only. Never add an image picker for proofs.
- Proof location check runs on the server (database function), not only in the app.
- Closed or permit-only trails never appear as quests; hike quests show trail rules.
- Exact user location stays hidden until they leave the spot.
- One redemption per user per quest; daily cap per quest; perk QR changes every 30 s.
- Users can only write their own rows: enforce with Row Level Security, and test it.
- No in-app payments.
- All business names, people, prices in seed data are fake.

## How to work
- One task per session. Before coding, restate the task and the "done when" check; ask if unclear.
- **Push back** when a request is risky, out of v1 scope, or contradicts an ADR: say why, propose an alternative. If the founder insists, do it.
- **Hard-to-undo choices** (new service, schema redesign, auth, hosting, money, privacy): write a `Proposed` ADR in `docs/decisions/` and wait for approval.
- **New dependency:** ask first. Say what it does, why it's needed, maintenance status (last release, downloads, open issues), license, known vulnerabilities, and alternatives including "write it ourselves" or "use what's installed".
- **Tests:** every change adds or updates tests for the behavior it changes. Run `check` (lint + typecheck + unit + DB tests) before every commit; paste the output. Never edit a test to make it pass unless the requirement changed.
- **Explain as you go** at the decision level: when you make an infrastructure or design choice, add one or two sentences on why and what was traded off. Founder knows TypeScript, SQL, Git well; React somewhat; React Native and mobile not yet: explain mobile-specific concepts briefly.
- **Complex code:** when code is non-obvious (database security rules, geofence/anti-cheat logic, QR token rotation, concurrency, auth flows, performance tricks), give a short plain-language overview in chat and in `docs/how-it-works/<topic>.md`: what it does, how the pieces connect, what would break if it's wrong. Not line-by-line.
- **No code comments.** Write self-documenting code: descriptive names for variables, functions, files (`isWithinQuestRadius`, not `check`); small functions that do one thing; no abbreviations; no magic numbers (named constants like `PERK_QR_ROTATION_SECONDS`). Explanations go in chat or `docs/`, never in code. Only exception: a line a tool requires (e.g. a lint-disable with no alternative).
- **Stay backend-portable** (`docs/decisions/0006`): screens and hooks never import the Supabase client; only `src/services/*` does. Business rules in standard Postgres SQL. Permission rules use `app_current_user_id()`, not `auth.uid()` directly. Store photo paths, not URLs. No Edge Functions or Realtime without an ADR.
- **Teach:** with each commit plan, give one line on why that type and scope.

## Git
- Conventional Commits (`docs/decisions/0005`): `feat(scope): imperative summary`, lowercase, ≤72 chars.
- **The founder stages, commits, and pushes** (`docs/decisions/0009`). The AI never runs `git add`, `git commit`, `git push`, `git reset`, `git stash`, `git rebase`, or anything else that changes history or the staging area. Read-only git (`status`, `diff`, `log`) is fine.
- When work is ready and `check` passes, give a **commit plan**: files per commit and why, files to leave out and why, the commit message, and the exact commands (`git add <files>`, `git diff --staged`, `git commit -m "..."`).
- **Public repo, so nothing goes in without a reason.** Never suggest committing: `.env` files, keys or passwords, real people's names/contacts/locations, business figures, files from the private research repo, build output, signing keys, `node_modules/`. Unsure? Leave it out and ask. Full list: `docs/decisions/0009`.
- Work on a feature branch; never push to or rewrite `main`; never force-push.

## Secrets and environments (`docs/decisions/0008`)
- Work only against local Supabase. Never run commands against staging or production.
- Never read, print, or commit `.env` or any key. Only `.env.example` is committed.
- Schema changes only as migration files in `supabase/migrations/`.

## End of session
Overwrite `NEXT_SESSION.md` (state, next step, open questions). Update `docs/FEATURES.md` ticks. Record decisions as ADRs.
