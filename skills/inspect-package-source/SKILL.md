---
name: inspect-package-source
description: Read a third-party package's REAL source instead of guessing its API. Use BEFORE writing or debugging non-trivial code against any dependency whose exact API you are not 100% certain of — especially pre-1.0, recently-updated, sparsely-documented, or unfamiliar packages. Fetch the actual installed source with `opensrc` (npm, PyPI, or any GitHub repo), then grep/read it. This is an anti-hallucination rail: if you would otherwise recall a signature, option name, return shape, or import path from memory, fetch the source and verify it first.
---

# Inspect Package Source

Coding agents hallucinate dependency APIs: wrong function names, invented
options, stale signatures, guessed import paths. The fix is not "remember
harder" — it is **read the real source that is actually installed**.

`opensrc` fetches the genuine source for a package into a local cache and
prints a path you can open and grep. One tool covers npm, PyPI, and arbitrary
GitHub repos.

## When this fires

Reach for this the moment you are about to:

- call a dependency's API and you are recalling the signature/options/return
  shape from memory rather than from something you just read
- integrate or extend a dependency where a wrong signature fails silently
- debug behavior that lives *inside* a dependency (the bug is in their code
  path, not yours)
- use a package you have never seen, or one that is pre-1.0 / freshly bumped /
  thin on docs

## When it does NOT fire (don't waste a fetch)

- language stdlib and ubiquitous, stable APIs you genuinely know
  (`Array.map`, `json.dumps`, `fs.readFile`)
- trivial one-liners where a wrong guess is cheap and obvious
- you already read the source this session

## Tool: opensrc

Check it is installed; install if missing:

```bash
which opensrc || npm i -g opensrc
```

Get the source path (fetches on cache miss). Pass `--cwd <repo>` so the version
resolves from that repo's lockfile — you want the version actually installed,
not latest:

```bash
# npm package, version pinned by the repo you're working in
opensrc path zod --cwd /path/to/repo

# PyPI package
opensrc path pypi:requests

# any GitHub repo (great for the package's own monorepo / pre-publish code)
opensrc path earendil-works/pi
```

Then read it. Don't dump whole files blind — grep for the symbol first:

```bash
SRC=$(opensrc path <pkg> --cwd /path/to/repo)
rg -n "export (function|const|class) <SymbolYouNeed>" "$SRC"
rg -n "<optionName>" "$SRC"
```

Other commands: `opensrc list` (what's cached), `opensrc fetch <pkg>` (prefetch
without printing path), `opensrc remove <pkg>`, `opensrc clean`.

## Per-ecosystem fallbacks (when opensrc isn't the fastest path)

- **Python**: deps are usually already readable in the active venv —
  `.venv/lib/python3.*/site-packages/<pkg>/`. Read there directly; use
  `opensrc path pypi:<pkg>` if not installed locally.
- **Rust**: crate source lives under
  `~/.cargo/registry/src/*/<crate>-<version>/`. Or `opensrc path <owner>/<repo>`
  for the crate's GitHub source.

## The rule

If you are about to write `import` or call an API from memory and a wrong guess
would cost more than the ~5 seconds to fetch — fetch and read first. Verified
beats remembered.
