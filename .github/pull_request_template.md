## Summary

What changed, and why someone shipping a mobile app would care.

Related issue: Fixes #

## Where this belongs

- **Directory:** `path/to/directory/`
- **Why here and not somewhere else:** (one line — e.g. "release mechanics, not ongoing store-operations work")
- Existing doc(s) checked for overlap before writing this: <!-- list them, or write "none found" -->

---

## Type of change

- [ ] New guide, or expanded a stub to real depth
- [ ] Correction to outdated or inaccurate guidance
- [ ] New framework, workflow, prompt, or agent definition
- [ ] Release pipeline / repository infrastructure
- [ ] Security, privacy, or governance update
- [ ] Bug fix — broken link, formatting, typo

---

## Checklist

- [ ] I checked for existing documentation on this topic before writing new content, and linked to it instead of repeating it.
- [ ] I verified technical steps where practical, and platform claims (a fee, a limit, an API level) against **current** official Apple/Google/Expo/React Native docs — not memory, not an older guide.
- [ ] Volatile claims cite a real source and end with `**Last verified:** <date>` — the date I actually checked, not copy-pasted from elsewhere.
- [ ] Relative links were clicked (or scripted) and actually resolve.
- [ ] No secrets, tokens, or credentials — placeholders only (`YOUR_APP_STORE_CONNECT_KEY`).
- [ ] `CHANGELOG.md` has an entry under `[Unreleased]` if this is a meaningful change (see CONTRIBUTING.md for what counts).
- [ ] This PR solves one clear problem — no unrelated changes bundled in.

<details><summary>If this touches <code>ai/</code> — expand</summary>

- [ ] The human-approval boundary is explicit for anything production, signing, secrets, or account-related — never implied.
- [ ] Agent/workflow guidance doesn't recommend unrestricted access to signing credentials, production secrets, or financial systems.
- [ ] Failure modes and false-positive/false-negative behavior are documented, not just the happy path.

</details>

---

## Reviewer notes

Anything you want a second pair of eyes on specifically — a judgment call, a source you couldn't fully confirm, a section you're not sure fits here.
