# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repo hosts public Mihomo/Clash custom rule files. It contains **no code, no build system, no tests** — only YAML configuration files. The real `clash.yaml` and any sensitive data (nodes, subscriptions, keys) are never committed.

## Repository Structure

- `rules/` — Custom domain rule files (`direct.yaml`, `reject.yaml`, `proxy.yaml`) consumed as remote `rule-providers` by Clash/Mihomo clients
- `templates/clash.template.yaml` — Sanitized template showing how to wire rule-providers and routing rules into a full Clash config

## Rule File Format

All rule files use `behavior: domain` with wildcard YAML syntax:

```yaml
payload:
  - '+.example.com'
```

An empty rule file must use `payload: []`, not an empty `payload:` key.

## Editing Rules

- Add domain entries under the `payload:` list in the appropriate file (`direct.yaml` for direct-connect, `reject.yaml` for ad-blocking, `proxy.yaml` for forced proxy)
- Use `'+.domain.com'` syntax to match a domain and all its subdomains
- Keep entries sorted or grouped logically; each file has a comment header explaining its purpose
- Changes are consumed by clients via raw GitHub URLs, so they take effect after the configured `interval` (default 86400s)

## Rule Priority

In `templates/clash.template.yaml`, rule order matters (top wins): private/LAN > custom-reject > public reject > custom-proxy > Apple/iCloud > custom-direct > public direct > proxy/gfw > GEOIP CN > MATCH fallback.
