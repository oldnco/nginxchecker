# NGINX CVE Audit

A small, read-only local triage script for the November 2026 NGINX HTTP/2, HTTP/3, resolver, and mail-proxy advisories.

It inspects the local NGINX Open Source binary and expanded configuration. It sends no requests to other systems, changes no configuration, and contains no exploit code.

## CVEs checked

- CVE-2026-123007 — HTTP/2 flow-control memory corruption
- CVE-2026-123633 — HTTP/2 use-after-free with upstream connections
- CVE-2026-121810 — HTTP/3 use-after-free with upstream connections
- CVE-2026-123634 — resolver use-after-free; additional configuration conditions apply
- CVE-2026-113425 — mail-proxy use-after-free

## Run

```sh
chmod +x nginx-cve-audit.sh
sudo ./nginx-cve-audit.sh
```

`sudo` lets `nginx -T` read included configuration files. The script reports feature flags only; it does not print or save the configuration. Without sufficient permissions, config-dependent results are `UNKNOWN`.

## Interpret the output

`POTENTIALLY AFFECTED` means the upstream version range and a relevant configuration feature matched. This is not proof of exploitability. Linux distributions and vendors may backport fixes without changing the upstream version string, so check the full installed package release against its vendor security advisory.

`NO — required feature not found` means the script did not find the relevant directive in the configuration it could read. Matching is heuristic; it does not fully parse NGINX contexts or inspect every generated/container-managed configuration. It reads configuration from disk, which can differ from the configuration currently loaded by workers if changes were not reloaded.

CVE-2026-123634 has extra conditions involving request-time proxy destinations and mirror/SSI behavior. A `resolver` and proxy directive alone do not confirm exposure.

This is a host-side tool, not a remote-domain scanner. An external website cannot read the site's NGINX configuration. Remote HTTP/2 checks can provide clues but cannot confirm all CVE-specific conditions. NGINX Plus and products embedding NGINX (such as ingress controllers and OpenResty) need their own vendor/version assessment; this script does not classify their release branches.

## Fixed upstream versions

The NGINX project lists `1.30.7` (stable) and `1.31.9` (mainline) as fixed releases for the November 18, 2026 advisories. Confirm vendor package status separately.

## References

- [NGINX security advisories](https://nginx.org/en/security_advisories.html)
- [NGINX release news](https://nginx.org/en/news.html)
- [CERT-EU Security Advisory 2026-022](https://cert.europa.eu/publications/security-advisories/2026-022/)

## License

MIT. See [LICENSE](LICENSE).
