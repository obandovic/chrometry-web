# `main` is protected

`main` has a repository ruleset, `protect-main` (id `25208319`), added 2026-10-15: **force pushes and deleting
`main` are blocked**, for everyone including the owner (no bypass). Ordinary pushes and merges work as before;
pull requests and approvals are not required.

- **Undo a bad commit:** `git revert <sha>` and push. Never reset and force-push.
- **Push rejected as non-fast-forward:** `main` moved since your last pull. `git pull --rebase origin main`, then push.
- **History truly must be rewritten** (a secret committed, a huge file): rotate the secret first, since rewriting does
  not un-leak it. Then switch the ruleset off, force-push with a lease, and switch it back on at once (owner only):

  ```sh
  gh api -X PUT repos/obandovic/chrometry-web/rulesets/25208319 -f enforcement=disabled
  git push --force-with-lease origin main
  gh api -X PUT repos/obandovic/chrometry-web/rulesets/25208319 -f enforcement=active
  ```
- **Check the rules in force:** `gh api repos/obandovic/chrometry-web/rules/branches/main --jq '[.[].type]'` should print
  `["non_fast_forward","deletion"]`.

This note lives in `.github/` so GitHub Pages does not publish it on the site.
