# omarchy-mac-e2e-test-capture

Evidence collector for Omarchy Mac volunteer E2E installs, plus a small host snapshot helper.

## Omarchy Mac E2E evidence (`omarchy-mac-e2e-capture`)

Records the command output the [volunteer E2E checklist](https://github.com/omacom/omarchy-mac) asks for. Run it at each stage (and after every reboot). Writes a timestamped directory plus `COMBINED.txt`. Serial numbers and password/token-looking fields are redacted.

Pinned URL (v1.1.1):

```
https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.1.1/omarchy-mac-e2e-capture
```

On the Asahi box, after login / after each reboot (use tty2 if tty1 is the guided setup):

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.1.1/omarchy-mac-e2e-capture \
  | bash -s -- auto --tester YOU --test-id ISSUE
```

Phases (checklist sections):

| Phase | When |
|---|---|
| `macos` | From macOS, before the Asahi installer (`system_profiler`, `sw_vers`, `diskutil`) |
| `asahi` | Fresh Asahi, before Omarchy |
| `start` | Right as the guided installer starts, and after each setup reboot |
| `desktop` | After first graphical login |
| `persistence` | After second login / normal reboot / cold boot |
| `fail` | **Before** any repair, rerun, or wipe |
| `auto` | Everything that applies on this OS right now |
| `wrap` | Record this tty until you `exit` (last lines before a reboot) |

If the install fails:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.1.1/omarchy-mac-e2e-capture \
  | bash -s -- fail --tester YOU --test-id ISSUE
```

From macOS:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.1.1/omarchy-mac-e2e-capture \
  | bash -s -- macos --tester YOU --test-id ISSUE
```

Evidence lands in `~/omarchy-mac-e2e/<stamp>-<phase>/`. Attach `COMBINED.txt` and the directory to the test ID. Review for remaining personal data before publishing.

---

## Host snapshot (`sysinfo`)

Collect host, OS, mount, disk, and memory info on Arch (or any Linux with `util-linux`). Prints the report and writes a timestamped file.

Does not call `hostname` (often missing on a minimal Arch install). Uses `uname -n` and `/etc/hostname`.

Pinned URL (v1.0.1):

```
https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo
```

## Run on the Arch box (curl only)

If local DNS cannot resolve GitHub/jsDelivr hostnames, use DNS-over-HTTPS against 1.1.1.1:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo | bash
```

That writes `~/sysinfo-<host>-<timestamp>.txt` and prints the same report.

Custom path:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo \
  | bash -s -- --out /tmp/report.txt
```

Skip DNS entirely and pin Cloudflare’s IP:

```bash
curl -fsSL --resolve cdn.jsdelivr.net:443:104.17.208.5 \
  https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo | bash
```

Working local DNS:

```bash
curl -fsSL https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo | bash
```

## Run on a remote, store the output here

The remote also keeps its own copy under `$HOME`.

```bash
ssh you@remote-host \
  'curl -fsSL --doh-url https://1.1.1.1/dns-query https://cdn.jsdelivr.net/gh/skuthus/omarchy-mac-e2e-test-capture@v1.0.1/sysinfo | bash' \
  | tee ~/sysinfo-remote-$(date +%Y%m%d-%H%M%S).txt
```

Helper (after cloning, or copy `sysinfo-remote` to `~/.local/bin`):

```bash
sysinfo-remote you@remote-host
sysinfo-remote you@remote-host /tmp/that-box.txt
```

## What it captures

- kernel node name (`uname -n`) and `/etc/hostname`
- timestamp
- `uname -a`
- `/etc/os-release`
- `findmnt` for `/` and `/boot`
- `lsblk -f`
- `df -h / /boot`
- `free -h`

Missing `/boot` as a separate mount is not a failure; that line is recorded and the rest continues.
