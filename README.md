# gha-lockfile

A minimal reproduction of Renovate's handling of the [GitHub Actions lockfile](https://github.com/github/actions-lockfile) (`.github/workflows/actions.lock`), as discussed in [github/gh-actions-lock#238](https://github.com/github/gh-actions-lock/issues/238#issuecomment-6376803290).

[`ci.yml`](.github/workflows/ci.yml) uses:

- `actions/checkout@v4`, a floating ref, which is locked to `d632683` (`v4.2.0`), and so has since moved upstream (`v4` now points at `11d5960`)
- `actions/setup-go@v5.0.0`, an exact ref, which Renovate will update

[`renovate.json`](renovate.json) only raises the `actions/setup-go` minor update, so there is a single branch to look at.

## Expected behaviour

Renovate updates `actions/setup-go@v5.0.0` to `v5.6.0`.

`actions.lock` should only change the `actions/setup-go` entry:

```diff
     '.github/workflows/ci.yml':
         - 'actions/checkout@v4'
-        - 'actions/setup-go@v5.0.0'
+        - 'actions/setup-go@v5.6.0'
 dependencies:
     'actions/checkout@v4':
         ref: 'v4'
         commit: 'sha1-d632683dd7b4114ad314bca15554477dd762a938'
         owner_id: 44036562
         repo_id: 197814629
-    'actions/setup-go@v5.0.0':
-        ref: 'v5.0.0'
-        commit: 'sha1-0c52d547c9bc32b1aa3301fd7a9cb496313a4491'
+    'actions/setup-go@v5.6.0':
+        ref: 'v5.6.0'
+        commit: 'sha1-40f1582b2485089dde7abd97c1529aa768e1baff'
```

## Previous behaviour

When Renovate ran `gh actions-lock --relock`, every ref was re-resolved, so the `actions/checkout@v4` entry was also moved to the current `v4` commit, even though it wasn't being updated:

```diff
     'actions/checkout@v4':
         ref: 'v4'
-        commit: 'sha1-d632683dd7b4114ad314bca15554477dd762a938'
+        commit: 'sha1-11d5960a326750d5838078e36cf38b85af677262'
```
