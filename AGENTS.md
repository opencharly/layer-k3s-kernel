# AGENTS.md — layer-k3s-kernel

Standalone candy repo for the `k3s-kernel` layer — the full-distro-kernel
install plus a venue reboot so k3s boots on a kernel that supports overlayfs and
netfilter. The candy lives in `charly.yml` at the repo root: the `reboot: true`
flag, the per-distro `distro:` package arms, and the `check:` assertion. This
candy carries no `skill:` entity, so no owning `/charly-*` skill is projected
for it.

Canonical files:

- `charly.yml` — the `k3s-kernel:` candy entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:k3s` — the closest owning skill: the k3s binary
  installer this full-kernel layer unblocks. Load before editing or
  troubleshooting the candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, per-distro `distro:` arms, package
  sections, `reboot:`). Load before editing any entity field or plan step.

There is no dedicated `/charly-*:k3s-kernel` owning skill yet — this repo's
candy carries no `skill:` entity. The gap is tracked in
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when one is authored, add it here.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` step is the functional evidence; it must stay
  valid on every distro arm it runs on. Scope a distro-specific check in the
  command itself — the check runner does not honour runner-level
  `exclude-distro` fields.
- The reboot is a VM-deploy-only effect; pod/OCI/Kubernetes skip the RebootStep
  and local targets skip with a warning.

## Modify this repo

- Edit the `k3s-kernel:` candy entity in `charly.yml`. When a `skill:` entity is
  added, mirror behaviour changes into it too — the skill is the projected usage
  source.
- Keep the `reboot: true` flag, the per-distro `distro:` package arms, and the
  `package:` `package_map` in the `check:` assertion in sync.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
