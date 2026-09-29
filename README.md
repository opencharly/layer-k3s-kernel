# k3s-kernel

Full-kernel layer for k3s on minimal cloud images.

The `k3s-kernel` candy ensures the full distro kernel is installed and reboots
the venue so it boots it. Minimal Arch cloud images ship a kernel without
overlayfs or iptables/netfilter, on which k3s's containerd and kube-proxy both
fail (`failed to mount overlay: no such device`, `iptables is not available on
this host`). A deploy's system upgrade installs the full `linux` package, but
the running kernel stays the minimal one until a reboot.

This candy installs the full kernel explicitly and requests a reboot
(`reboot: true`, emitted last) so the k3s-server checks run against a kernel
that can actually run k3s. Only the VM deploy reboots (a deterministic `boot_id`
gate); pod/OCI/Kubernetes skip the RebootStep and local targets skip with a
warning.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `k3s-kernel` |
| Packages | `linux` (arch), `linux-image-amd64` (debian), `kernel` (fedora), `linux-image-generic` (ubuntu) |
| Reboot | `reboot: true` — VM deploy only |
| Install files | `charly.yml` (package + reboot request) |
| Service / port | none |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list, on a venue
whose base kernel is minimal. The k3s role candies (`k3s-server` / `k3s-agent`,
owned by the `vm-k3s-server` / `vm-k3s-agent` repos) depend on it:

```yaml
my-k3s-vm:
  candy:
    base: cachyos
    candy:
      - '@github.com/opencharly/layer-k3s-kernel:v2026.239.1629'
      - '@github.com/opencharly/layer-k3s:v2026.239.1630'
```

After the deploy, the venue reboots once and the k3s-server checks run against
the full kernel.

## Layout

- `charly.yml` — the `k3s-kernel:` candy entity: the `reboot: true` flag, the
  per-distro `distro:` package arms, and the `check:` assertion. No `skill:`
  entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: none yet — this candy carries no `skill:` entity. Closest:
  `/charly-infrastructure:k3s` (the binary installer this kernel unblocks) and
  `/charly-image:layer` (candy authoring). The gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- Consumer: `/charly-infrastructure:k3s-server` — the control-plane role candy
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
