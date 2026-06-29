# Firmware binaries

Drop the compiled `.bin` files here. To publish a release:

```bash
# From the repo root:
gh release create v1.00 firmware/*.bin --title "v1.00" --notes "Release notes here"
```

The webflasher will pick up the latest release automatically.
