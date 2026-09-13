# omarchy-mac-e2e-test-capture

On a fresh Asahi Alarm install, as **root**, after `nmtui`:

```bash
curl -fsSL https://skuthus.github.io/e2e | bash
```

Setup and install run as usual. After you sign into Omarchy, copy this folder off the machine:

```
~/omarchy-mac-e2e
```

If DNS cannot resolve GitHub:

```bash
curl -fsSL --doh-url https://1.1.1.1/dns-query https://skuthus.github.io/e2e | bash
```
