# PR: Reproducible registry & cross-platform CI

**Branch:** `chore/reproducible-registry-ci`  
**Scope:** Platform/CI only. No new skills.

## Title

```text
chore(ci): reproducible registry and cross-platform validate:references
```

## Summary

Makes registry generation (README, catalog, bundles) deterministic and makes `validate:references` pass on Windows and Linux regardless of path separators in `bundles.json`.

## Changes

| File | Change |
|------|--------|
| **.github/workflows/ci.yml** | Set `SOURCE_DATE_EPOCH` in job env; drift-fix instructions tell contributors to set it when running chain/catalog locally. |
| **tools/scripts/update_readme.py** | Use `SOURCE_DATE_EPOCH` or fixed fallback for `updated_at`; add TOC substitution so "Browse … Skills" link matches heading; catch `OverflowError` for invalid epoch. |
| **tools/scripts/build-catalog.js** | Normalize skill IDs to POSIX (`toPosixPath`) so catalog/bundles use `/` on all platforms; use named constant for fallback timestamp. |
| **tools/scripts/validate_references.py** | Normalize collected skill IDs and slugs from JSON/docs to POSIX so comparison works with either `\` or `/`. |

## Why

- **Drift:** README and catalog no longer depend on run time; local and CI output match when `SOURCE_DATE_EPOCH` is set (or fallback is used).
- **validate:references on Windows:** `bundles.json` and filesystem paths can use different separators; normalizing both sides avoids false "missing skill" errors.
- **TOC:** README table-of-contents "Browse … Skills" anchor stays in sync with the "## Browse … Skills" heading.

## Validation

```bash
npm run validate
npm run validate:references
export SOURCE_DATE_EPOCH=1767225600
npm run chain
npm run catalog
git diff --quiet   # should be clean
```

## Checklist

- [ ] No new skills in this PR.
- [ ] CI passes (validate, validate:references, drift check).
- [ ] Same `SOURCE_DATE_EPOCH` value used in workflow and in docs (1767225600 = 2026-02-08 00:00:00 UTC).
