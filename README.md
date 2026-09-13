# sysinfo

Collect host, OS, mount, disk, and memory info on Arch (or any Linux with `util-linux`). Prints the report and writes a timestamped file.

Use the jsDelivr URL. Many networks cannot resolve `raw.githubusercontent.com`.

```
https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.0/sysinfo
```

## Run on the Arch box (curl only)

```bash
curl -fsSL https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.0/sysinfo | bash
```

Writes `~/sysinfo-<hostname>-<timestamp>.txt` and prints the same report.

Custom path:

```bash
curl -fsSL https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.0/sysinfo | bash -s -- --out /tmp/report.txt
```

If jsDelivr is also blocked but you can reach GitHub's CDN by IP:

```bash
curl -fsSL --resolve raw.githubusercontent.com:443:185.199.108.133 \
  https://raw.githubusercontent.com/skuthus/sysinfo/v1.0.0/sysinfo | bash
```

## Run on a remote, store the output here

The remote also keeps its own copy under `$HOME`.

```bash
ssh you@remote-host \
  'curl -fsSL https://cdn.jsdelivr.net/gh/skuthus/sysinfo@v1.0.0/sysinfo | bash' \
  | tee ~/sysinfo-remote-$(date +%Y%m%d-%H%M%S).txt
```

Helper (after cloning, or copy `sysinfo-remote` to `~/.local/bin`):

```bash
sysinfo-remote you@remote-host
sysinfo-remote you@remote-host /tmp/that-box.txt
```

## What it captures

- `hostname` and timestamp
- `uname -a`
- `/etc/os-release`
- `findmnt` for `/` and `/boot`
- `lsblk -f`
- `df -h / /boot`
- `free -h`

Missing `/boot` as a separate mount is not a failure; that line is recorded and the rest continues.
