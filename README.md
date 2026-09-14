# Omarchy Mac E2E test capture

The file you share is always:

```
~/omarchy-mac-e2e/REPORT.md
```

That is the real report only **after the final snapshot** has run. Verdicts are already PASS / FAIL / SKIP — do not edit them.

## New install (curl once)

On fresh Asahi Alarm, as **root**, after `nmtui`, before Omarchy setup:

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

Then:

1. Answer setup questions (hostname, user, password, encrypt unless `--no-encrypt`).
2. Type the **disk passphrase** when asked. Let it reboot.
3. Sign into Omarchy if you see a greeter (encrypted machines usually autologin).
4. Wait: desktop probes, one extra reboot, then a **final root snapshot**.
5. Share `~/omarchy-mac-e2e/REPORT.md`.

Do not curl again after reboots. The final snapshot is automatic on a new install from this script.

Optional, from **macOS** before the Asahi installer:

```bash
curl -fsSL https://skuthus.github.io/e2e | bash -s -- macos
```

---

## Already installed (backfill — this is the shareable report)

If Omarchy is already up and the report looks incomplete (empty hostname/user, mixed baseline mounts, `ufw` / `passwd -S root` failed as a normal user), run **this** as the logged-in user:

```bash
sudo bash -c 'curl -fsSL https://skuthus.github.io/e2e | bash -s -- final'
```

`sudo` must wrap the whole pipeline so **bash** is root. `curl | bash` as your desktop user cannot create `/var/lib/omarchy-mac-e2e/final`.

When that command finishes, **`~/omarchy-mac-e2e/REPORT.md` is the report to attach** to the test ID / GitHub issue. The rest of `~/omarchy-mac-e2e/` is raw evidence.

---

## What you do vs what is automatic

| Step | Who | What happens |
|---|---|---|
| Backup, ≥50 GB Linux, power, internet | **You** | In macOS. Do not disable `speakersafetyd`. |
| Install Asahi Alarm Minimal BTRFS | **You** | Boot the **untouched** Asahi image. |
| `nmtui` as `root` | **You** | Network up. Do not start Omarchy yet. |
| `curl …/e2e \| bash` | **You, once** | Baseline + hooks + guided setup. |
| Setup questions / disk passphrase | **You** | ARM-unavailable packages stay **No**. |
| Setup reboots, desktop probes, persistence reboot | **Automatic** | |
| Final snapshot | **Automatic** on new installs; **sudo … final** if you already installed | Root checks (`ufw`, `passwd -S root`, `journalctl --list-boots`) and a rebuilt report that keeps the Asahi baseline separate from the installed system. |
| Share | **You** | Attach `~/omarchy-mac-e2e/REPORT.md`. |

---

## What the final REPORT.md contains

- Fresh Asahi baseline (`uname`, `os-release`, pre-install `findmnt` / `lsblk` / `df` / `free`)
- Setup command, status, saved conf, warnings/skips, log tails
- Post-install mounts (separate `/boot`, LUKS root)
- Desktop: `omarchy version`, packages, failed units, migrations, screenshot, input, apps, audio, theme
- Cleanup: setup service/conf/sudoers gone, journals, UFW, SSH, root lock
- `journalctl --list-boots`

**Cold boot** is SKIP (software reboot, not a 15s power-cut).  
**Disk passphrase** still has to be typed.

---

## If the install fails

Stop. Do not repair or wipe until evidence is saved.

```bash
sudo bash -c 'curl -fsSL https://skuthus.github.io/e2e | bash -s -- fail'
```

Then share `~/omarchy-mac-e2e/REPORT.md` if it exists, otherwise whatever is under `/var/lib/omarchy-mac-e2e/`.
