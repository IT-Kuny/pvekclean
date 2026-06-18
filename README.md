![PVEKCLEAN Logo](assets/banner.png)

Easily remove old/unused PVE kernels on your Proxmox VE system

[![Version](https://img.shields.io/badge/Version-v2.1.0-brightgreen)](https://github.com/IT-Kuny/pvekclean)
[![License: MIT](https://img.shields.io/badge/License-MIT-brightgreen.svg)](https://opensource.org/licenses/MIT)
![Updated](https://img.shields.io/github/last-commit/IT-Kuny/pvekclean)
![Proxmox](https://img.shields.io/badge/-Proxmox-orange)
![Debian](https://img.shields.io/badge/-Debian-red)

> **Fork maintained by [IT-Kuny](https://github.com/IT-Kuny)**
> Upstream: [jordanhillis/pvekclean](https://github.com/jordanhillis/pvekclean) — this fork applies community PRs and bug fixes not yet merged upstream.

---

### What is PVE Kernel Cleaner?

PVE Kernel Cleaner is a program to complement Proxmox Virtual Environment (PVE), an open-source server virtualization platform. It purges old/unused kernels accumulating in `/boot`, which on Proxmox systems is typically limited to 1 GB. Without regular cleanup, `dpkg` and `apt` will fail when there is no space left to install new kernels.

This fork addresses multiple open issues from the upstream repository that have been pending since 2023.

## Example Usage

![PVEKCLEAN Example](assets/example-2.0.2.png)

## Features

* Removes old PVE kernels from your system
* Fully removes associated kernel headers (fixes dkms/module rebuild issues after cleanup)
* Detects boot method: **GRUB** or **UEFI/systemd-boot** — runs the correct refresh command automatically
* ZFS root support: correctly reports `/boot` usage when no separate `/boot` partition exists
* Scheduler: removes old kernels automatically on a daily/weekly/monthly basis
* Simple `pvekclean` command for system-wide access
* Boot disk health check based on available space
* Dry-run mode for non-destructive testing
* Keeps a configurable minimum number of recent kernels
* Support for latest Proxmox VE versions and kernel naming conventions

## What's Fixed in This Fork (v2.1.0)

| Issue | Description | Fix |
|-------|-------------|-----|
| [#10](https://github.com/jordanhillis/pvekclean/issues/10) / [#13](https://github.com/jordanhillis/pvekclean/issues/13) | Metapackage named `Latest` misidentified as kernel version | Filter `Latest` from dpkg output |
| [#12](https://github.com/jordanhillis/pvekclean/issues/12) / [#15](https://github.com/jordanhillis/pvekclean/issues/15) | Boot disk info blank on ZFS root (no separate `/boot`) | Fall back to `/` when `/boot` not a separate mount |
| [#13](https://github.com/jordanhillis/pvekclean/issues/13) | UEFI/systemd-boot installs: `update-grub` has no effect | Detect boot method, run `proxmox-boot-tool refresh` on UEFI |
| [#16](https://github.com/jordanhillis/pvekclean/issues/16) / [#18](https://github.com/jordanhillis/pvekclean/issues/18) | Kernel headers not fully removed, breaks dkms after cleanup | Purge all header variants (`pve-headers-*`, `proxmox-headers-*`) |
| [#19](https://github.com/jordanhillis/pvekclean/issues/19) | Removed kernels reappear as candidates on next run | Run `apt autoremove -y` after purge to flush stale dpkg state |

Incorporates upstream PRs [#17](https://github.com/jordanhillis/pvekclean/pull/17) and [#21](https://github.com/jordanhillis/pvekclean/pull/21).

## Latest Version

* **v2.1.0** (IT-Kuny fork)
* v2.0.2 (upstream)

## Prerequisites

The following packages must be installed:

* `cron`
* `curl`
* `git`

```bash
apt-get install cron curl git
```

## Installing

### Installation via Git (this fork)

```bash
git clone https://github.com/IT-Kuny/pvekclean.git
cd pvekclean
chmod +x pvekclean.sh
./pvekclean.sh
```

### Installation via Curl (this fork)

```bash
curl -o pvekclean.sh https://raw.githubusercontent.com/IT-Kuny/pvekclean/master/pvekclean.sh
chmod +x pvekclean.sh
./pvekclean.sh
```

## Updating

PVE Kernel Cleaner checks for updates automatically on each run. If a new version is available you will be prompted to update in-place.

> **Note:** The auto-update checks against the upstream `jordanhillis/pvekclean` repository. If you want to stay on this fork's version, you can set `check_for_updates=false` at the top of the script.

## Usage

```
pvekclean [OPTION1] [OPTION2]...

  -k, --keep [number]   Keep the specified number of most recent PVE kernels on the system
                        Can be used with -f or --force for non-interactive removal
  -f, --force           Force the removal of old PVE kernels without confirm prompts
  -rn, --remove-newer   Remove kernels that are newer than the currently running kernel
  -s, --scheduler       Have old PVE kernels removed on a scheduled basis
  -v, --version         Shows current version of pvekclean
  -r, --remove          Uninstall pvekclean from the system
  -i, --install         Install pvekclean to the system
  -d, --dry-run         Run the program in dry run mode for testing without making system changes
```

## Usage Examples

**Remove old kernels non-interactively:**
```bash
pvekclean -f
```

**Keep the 2 most recent kernels, remove the rest without prompts:**
```bash
pvekclean -f -k 2
```

**Dry run — see what would be removed without touching anything:**
```bash
pvekclean -d
```

**Schedule automatic cleanup (daily/weekly/monthly via cron):**
```bash
pvekclean -s
```

**Remove kernels newer than the currently running one:**
```bash
pvekclean -rn -k 2
```

## Developers

* **Jordan Hillis** — *Original Author* ([jordan@hillis.email](mailto:jordan@hillis.email))
* **IT-Kuny** — *Fork Maintainer* [it@it-kuny.ch](mailto:it@it-kuny.ch) community PRs + bug fixes

## License

This project is licensed under the MIT License — see [LICENSE.md](LICENSE.md) for details.

## Acknowledgments

* This program is not an official product of Proxmox Server Solutions GmbH
* Thanks to upstream contributors: beckerr-rzht (PR #17), BrendanKOz (PR #21)
