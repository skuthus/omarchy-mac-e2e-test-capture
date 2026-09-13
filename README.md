# omarchy-mac-e2e-test-capture

On a fresh Asahi Alarm install, as **root**, after `nmtui`:

```bash
curl -fsSL https://skuthus.github.io/e2e | bash
```

Setup and install run as usual. After you sign into Omarchy, share:

```
~/omarchy-mac-e2e/REPORT.md
```

That markdown is the checklist report plus every captured command. The rest of `~/omarchy-mac-e2e/` is the raw evidence.

If DNS cannot resolve GitHub:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query https://skuthus.github.io/e2e | bash
```
