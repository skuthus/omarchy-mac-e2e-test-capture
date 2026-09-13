# Omarchy Mac E2E test capture

Volunteer helper for a fresh Omarchy Mac install. You **curl once** on Asahi. The script records the checklist command output through setup, then writes a markdown report after first Omarchy login.

Share this file when you are done:

```
~/omarchy-mac-e2e/REPORT.md
```

Use the volunteer E2E checklist you were assigned. A successful `install.sh` is not an E2E pass.

## When to curl (once)

**Where:** the fresh Asahi Alarm install, logged in as **root**.  
**When:** after `nmtui` has a working network, **before** Omarchy setup.  
**Do not** curl again after every reboot. Boot snapshots and the first-login report are automatic.

```bash
curl -fsSL https://skuthus.github.io/e2e | bash
```

That command:

1. Records the fresh Asahi baseline (`uname`, mounts, disk, memory, …)
2. Installs background capture across reboots
3. Starts **Omarchy Mac setup** (same guided install as usual)

If DNS cannot resolve GitHub:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query https://skuthus.github.io/e2e | bash
```

Assigned `--no-encrypt` test:

```bash
curl -fsSL https://skuthus.github.io/e2e | bash -s -- --no-encrypt
```

Optional, **from macOS**, before the Asahi installer (section 2 hardware dump):

```bash
curl -fsSL https://skuthus.github.io/e2e | bash -s -- macos
```

Copy that macOS `REPORT.md` off to the side; the Asahi run produces the main report.

---

## Process: curl vs you

| Step | Who | What you do |
|---|---|---|
| 1. Assignment | **You** | Fill tester, test ID, Mac model, image (Minimal BTRFS), encrypt vs `--no-encrypt`, keymap, network. Do this before anyone else takes the same case. |
| 2. Safety | **You** | macOS backup, ≥50 GB Linux (100 GB preferred), power + internet. Do not disable `speakersafetyd`. Do not run ad-hoc partition/encryption/bootloader repairs before collecting evidence. |
| 3. macOS dump (optional) | **Curl** | `bash -s -- macos` on macOS: `system_profiler`, `sw_vers`, `diskutil list`. |
| 4. Install Asahi Alarm | **You** | From macOS, install the assigned image (primary: **Minimal BTRFS**). Record the image name and Linux size. |
| 5. First Asahi boot | **You** | Boot the **untouched** Asahi install. Log in as `root` / `root`. Run `nmtui`, connect Wi-Fi. Confirm display, keyboard/trackpad, DNS, and clock. **Do not** start Omarchy yet. |
| 6. Start capture + setup | **Curl once** | `curl -fsSL https://skuthus.github.io/e2e \| bash` as root. |
| 7. Setup questions | **You** | Encrypt (unless assigned `--no-encrypt`), hostname, username, password. Default the ARM-unavailable package prompt to **No**. Confirm the on-screen plan matches the assignment. |
| 8. Reboots | **Automatic + you** | Let it reboot. Encrypted path: type the **disk passphrase** when asked (same keymap as setup). Photograph or note the last line on screen before each reboot if you can. Do not power off during `cryptsetup reencrypt`. |
| 9. First Omarchy login | **You** | Sign in (or autologin after the disk unlock). Wait ~10 seconds. |
| 10. Report | **Automatic** | `~/omarchy-mac-e2e/REPORT.md` is written. |
| 11. Desktop smoke | **You** | Notch layout, menu/launcher/terminal/browser/files/settings, Wi-Fi, keys/backlight, audio at modest volume with speaker protection, lock, one suspend/resume, change a theme. |
| 12. Persistence | **You** | Log out and in again (first-run must not loop). Normal reboot. Shut down, wait 15 seconds, cold boot. Confirm `sudo` with the user password. |
| 13. Share | **You** | Attach `~/omarchy-mac-e2e/REPORT.md` (and photos/video) to the test ID / GitHub issue. Fill every **NEEDS TESTER** line. |

---

## What the curl records for you

These are the checklist command blocks. You do not re-run them.

- Fresh Asahi: `uname -a`, `/etc/os-release`, `findmnt` `/` and `/boot`, `lsblk -f`, `df -h / /boot`, `free -h`
- Setup: command, repo/ref, start time, `omarchy-mac-setup --status`, setup conf, warnings/skips, log tails
- After reboots: whether `/boot` moved, whether root is encrypted, setup `--status`
- First login: `omarchy version`, `OMARCHY_PATH`, pacman, failed units, `omarchy-migrate --pending`, `omarchy-done`, `swapon`, findmnt
- Cleanup: setup service/conf/sudoers gone, warning journals, UFW/SSH/root lock
- On setup failure: `--status`, `journalctl -b`, `--list-boots`, `findmnt`, `lsblk`, log tails — **before** any repair

Items the script **cannot** see stay as **NEEDS TESTER** in the report (screen photos, passphrase prompt, notch, apps, audio, suspend, second login, cold boot).

---

## If anything fails

Stop. Do **not** rerun setup, apply a suggested fix, or wipe Linux until evidence is saved.

1. Photograph the screen (last line, passphrase prompt, blank screen, GRUB).
2. If a shell is reachable, the failure snapshot should already be under `~/omarchy-mac-e2e/` or `/var/lib/omarchy-mac-e2e/`. If not, as root:

   ```bash
   curl -fsSL https://skuthus.github.io/e2e | bash -s -- fail
   ```

3. Share `REPORT.md` plus photos. Label guesses as hypotheses.

---

## After you sign in

```
~/omarchy-mac-e2e/REPORT.md   ← share this
~/omarchy-mac-e2e/            ← raw captures, keep with the report
```

Open `REPORT.md`, fill **NEEDS TESTER** and the overall PASS/FAIL/BLOCKED line, then attach it to the tracking issue.
