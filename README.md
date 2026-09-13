# sysinfo

Collect host, OS, mount, disk, and memory info on Arch (or any Linux with `util-linux`). Prints the report and writes a timestamped file.

Pinned URL (v1.0.0):

```
https://raw.githubusercontent.com/skuthus/sysinfo/v1.0.0/sysinfo
```

## Run locally

```bash
curl -fsSL https://raw.githubusercontent.com/skuthus/sysinfo/v1.0.0/sysinfo | bash
```

Writes `~/sysinfo-<hostname>-<timestamp>.txt` and prints the same report.

Custom path:

```bash
curl -fsSL https://raw.githubusercontent.com/skuthus/sysinfo/v1.0.0/sysinfo | bash -s -- --out /tmp/report.txt
```

## Run on a remote, store the output here

The remote also keeps its own copy under `$HOME`.

```bash
ssh you@remote-host \
  'curl -fsSL https://raw.githubusercontent.com/skuthus/sysinfo/v1.0.0/sysinfo | bash' \
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
