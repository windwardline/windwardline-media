# Windward Media — operating contract

Operating contract for AI work in this repo; the global `~/AGENTS.md` still applies. Work here follows the CONVERGE cycle and delivery discipline in `FLEET.md` (windwardline/windwardline) — find → refute → verify yourself → fix → re-rank → test → update → report; enumerate the gates rather than counting them, stage explicit paths, validate before mutating, preserve standing claims, derive populations rather than curating them, and never let a harness failure read as the subject refusing. `FLEET.md` governs where it and this summary differ. The production division masthead — renamed from Windward Creative; first works in development. Live at media.windwardline.com. Zero-dependency static HTML.

## Commands

Any static server for preview. CI-equivalent: `npx --yes html-validate@9 index.html` · JSON-parse `vercel.json`.

## Gates

CI (`ci.yml`, on pushes and PRs to main) is html-validate on `index.html` plus the `vercel.json` parse. `schedule.html` and `script.js` are not checked. Push to main deploys production. A parallel `security.yml` (PRs, pushes, weekly cron; a daily cron runs only the production headers probe) gates Semgrep and secret scan; a post-deploy job asserts the production security headers. An advisory Claude review runs on every same-repo PR via `claude-review.yml`, which deliberately calls the fleet reusable at `@main` — one merge updates every repo. It activates only when the `CLAUDE_CODE_OAUTH_TOKEN` secret is present — reviews bill the owner's Claude subscription, not Console credits; fork PRs never receive secrets, so they skip it by security design. `dependabot-auto-merge.yml` merges nothing itself: on a Dependabot PR it arms GitHub's native auto-merge so the `main-requires-green-ci` ruleset stays the only thing that decides, and it asserts that a gate exists first — a repo with `allow_auto_merge` off or no required check is a hold, because `gh pr merge --auto` would otherwise merge immediately. It holds for major bumps (labelling them `deferred-major` first), a release that changed maintainers, pre-1.0 packages, the `no-automerge` label, and metadata it cannot verify, withdrawing an already-armed merge when a rebase breaks compliance. It mints a fleet GitHub App token from `FLEET_AUTOMERGE_APP_ID` and `FLEET_AUTOMERGE_PRIVATE_KEY` — Dependabot secrets, not Actions secrets, since a Dependabot run reads the latter as empty — and degrades to `GITHUB_TOKEN` when they are absent; a `GITHUB_TOKEN` merge fires no push workflows, so on that path `security.yml`'s `Headers live` probe never runs after an auto-merge. Its job id carries no `name:` so the check renders exactly `dependabot-auto-merge`, and it must never become a required check — it is the lane that arms the gate, not one of the gates. The file is byte-identical in every fleet repo that takes it.

## Laws

- Koalendar slug `windward-media` is hardcoded in `schedule.html`; CSP `frame-src https://koalendar.com` in `vercel.json`; change both together or the iframe silently blanks.
- The Kilo flag ("I wish to communicate with you") is inline SVG in both HTML files with hardcoded fills (`#c9a25e` left, `#202e4d` right) — only the mount stroke is themed. A palette change touches the SVGs by hand.
- Four `:root` blocks (base, `@media` dark, `[data-theme="light"]`, `[data-theme="dark"]`) — an accent change needs all four. `--gold #c9a25e` is constant across themes; `--gold-ink #8a6b39` goes dark `#d9bd85`.
- `fonts/` carries its own license. EB Garamond ships under the SIL OFL 1.1, `fonts/OFL.txt` holds the upstream copyright line and the verbatim license text, and `LICENSE` excepts `fonts/` from the proprietary notice. Adding or replacing a family adds its copyright line to `fonts/OFL.txt` in the same change set.
- Never commit `.env.local` — `vercel link` drops an OIDC token there.
- `cleanUrls: true` maps `/schedule` → `schedule.html`. `.vercelignore` excludes `docs/`.
