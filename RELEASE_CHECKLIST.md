# Release Checklist

Use this checklist before publishing a new release.

## 1. Validate Functionality

- Run shell syntax check:
  - `bash -n nblock`
- Test key commands on a test host:
  - `add <ip>`
  - `list`
  - `remove <ip>`
  - `auto-setup`
- Verify `--reload` path works:
  - `nginx -t`
  - `systemctl reload nginx` (or `nginx -s reload`)

## 2. Validate Safety

- Confirm auto-setup does not overwrite unrelated Nginx configs.
- Confirm duplicate `deny` lines are not added.
- Confirm invalid IPv4 values fail fast.
- Confirm remove is idempotent (no error if IP is absent).

## 3. Documentation

- Update `README.md` if behavior/flags changed.
- Update `CHANGELOG.md` with user-visible changes.
- Ensure examples reflect current commands and defaults.

## 4. Versioning

- Choose next version using SemVer:
  - Patch: bug fixes
  - Minor: backward-compatible features
  - Major: breaking changes
- Add release section in `CHANGELOG.md` with date.

## 5. GitHub Publish

- Commit:
  - `git add .`
  - `git commit -m "release: vX.Y.Z"`
- Tag:
  - `git tag vX.Y.Z`
- Push:
  - `git push origin <branch>`
  - `git push origin vX.Y.Z`
- Create GitHub Release notes from changelog.

## 6. Post-Release Smoke Check

- Install from fresh clone.
- Run one full happy-path flow:
  - `auto-setup`
  - `add --reload`
  - `list`
  - `remove --reload`
