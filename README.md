# sysinfo

Collect host, OS, mount, disk, and memory info on Arch (or any Linux with `util-linux`). Prints the report and writes a timestamped file.

Does not call `hostname` (often missing on a minimal Arch install). Uses `uname -n` and `/etc/hostname`.

Pinned URL (v1.0.1):

```
https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo
```

## Run on the Arch box (curl only)

If local DNS cannot resolve GitHub/jsDelivr hostnames, use DNS-over-HTTPS against 1.1.1.1:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo | bash
```

That writes `~/sysinfo-<host>-<timestamp>.txt` and prints the same report.

Custom path:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query \
  https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo \
  | bash -s -- --out /tmp/report.txt
```

Skip DNS entirely and pin Cloudflare’s IP:

```bash
curl -fsSL --resolve cdn.jsdelivr.net:443:104.17.208.5 \
  https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo | bash
```

Working local DNS:

```bash
curl -fsSL https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo | bash
```

## Run on a remote, store the output here

The remote also keeps its own copy under `$HOME`.

```bash
ssh you@remote-host \
  'curl -fsSL --doh-url https://1.1.1.1/dns-query https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.1/sysinfo | bash' \
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
