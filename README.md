# Omarchy Mac E2E test capture

One curl on a fresh Asahi box. Setup and install run as usual. After Omarchy comes up, the script probes the desktop, reboots once, and writes a finished report.

Share this file:

```
~/omarchy-mac-e2e/REPORT.md
```

Do not fill verdicts. They are already PASS / FAIL / SKIP.

## Curl once

**Where:** fresh Asahi Alarm, logged in as **root**.  
**When:** after `nmtui` has network, **before** any Omarchy setup.  
**Do not** curl again after reboots.

```bash
curl -fsSL https://skuthus.github.io/e2e | bash
```

If DNS cannot resolve GitHub:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query https://skuthus.github.io/e2e | bash
```

`--no-encrypt` assignment:

```bash
curl -fsSL https://skuthus.github.io/e2e | bash -s -- --no-encrypt
```

Optional, from **macOS** before the Asahi installer (hardware dump):

```bash
curl -fsSL https://skuthus.github.io/e2e | bash -s -- macos
```

---

## What you do vs what is automatic

| Step | Who | What happens |
|---|---|---|
| Backup, ≥50 GB Linux, power, internet | **You** | Do this in macOS. Do not disable `speakersafetyd`. |
| Install Asahi Alarm Minimal BTRFS | **You** | From macOS. Then boot the **untouched** Asahi image. |
| `nmtui` as `root` / `root` | **You** | Get Wi-Fi up. Do not start Omarchy yet. |
| `curl …/e2e \| bash` | **You, once** | Records the Asahi baseline, hooks capture across reboots, starts guided setup. |
| Setup questions | **You** | Encrypt (unless `--no-encrypt`), hostname, username, password. Leave ARM-unavailable packages at **No**. |
| Setup reboots | **Automatic** | Let it reboot. Encrypted path: type the **disk passphrase** when asked. Do not power off during encryption. |
| First Omarchy session | **Mostly automatic** | Encrypted machines autologin after unlock. If you see a greeter, sign in. |
| Desktop probes | **Automatic** | Screenshot, Hyprland monitors/devices, apps, brightness, quiet test tone, theme swap, lock/suspend units, checklist command dumps. |
| Persistence reboot | **Automatic** | The machine reboots once. After it comes back, capture runs again. |
| Final snapshot | **Automatic** | Root-only leftovers (`ufw`, `passwd -S root`, `journalctl --list-boots`, installed mounts). The report keeps the original Asahi baseline separate from post-install state. |
| Report | **Automatic** | `~/omarchy-mac-e2e/REPORT.md` is complete. |
| Share | **You** | Attach that markdown to the test ID / GitHub issue. |

You never re-run the checklist commands. You never fill **NEEDS TESTER**.

---

## What the report already contains

- Fresh Asahi: `uname`, `os-release`, `findmnt` `/` and `/boot`, `lsblk`, `df`, `free`
- Setup: command, repo/ref, `omarchy-mac-setup --status`, conf, warnings/skips, log tails
- Reboots: whether `/boot` moved, whether root is encrypted
- Desktop: `omarchy version`, packages, failed units, migrations, `omarchy-done`, screenshot, input, apps, audio, theme
- Cleanup: setup service/conf/sudoers gone, journals, UFW, SSH, root lock
- On failure: `--status`, `journalctl -b`, boots, `findmnt`, `lsblk`, log tails

**Cold boot** is SKIP (software reboot, not a 15s power-cut).  
**Disk passphrase** still has to be typed; the script cannot do that.

---

## If it fails

Stop. Do not rerun setup, “fix” storage, or wipe Linux until the report is saved.

If a shell is reachable and there is no report yet:

```bash
curl -fsSL https://skuthus.github.io/e2e | sudo bash -s -- fail
```

On a machine that already finished install (backfill the post-Omarchy snapshot):

```bash
sudo bash -c 'curl -fsSL https://skuthus.github.io/e2e | bash -s -- final'
```

That must be **root**. `curl | bash` as your desktop user cannot create `/var/lib/omarchy-mac-e2e/final`.

Then share `REPORT.md` (under `~/omarchy-mac-e2e/` or `/var/lib/omarchy-mac-e2e/`).
