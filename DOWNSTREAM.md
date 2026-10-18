# Downstream edition notes — Private AI Commercial POC

This fork explores a **managed private AI workspace for small businesses** built on Osmantic ODS. It is an engineering proof of concept, **not** a production release or a statement that commercial distribution, specific third-party models, or rebranding have been cleared.

## Upstream baseline

- Upstream: https://github.com/Osmantic/ODS
- Fork: https://github.com/r00texploit/ODS
- Branch: `feat/private-ai-commercial-poc`
- Pinned upstream baseline (2026-10-18): `63c985d5b62c7954238bf66da4435aaea15d5dbf`
- Target distribution: **one isolated ODS installation per business**, initially on customer-owned hardware or a dedicated VM. No shared cross-customer ODS instance.
- Product definition / gates: [docs/commercial-poc/README.md](docs/commercial-poc/README.md)

## Changes from upstream

- Initial branch: documentation and planning only. No runtime files, feature flags, container permissions, network bindings, installers, or secrets altered.
- Future customization should prefer `ods/extensions/services/`, versioned manifests, model catalogs, UI theme/branding where legally permitted, and downstream docs.
- Keep commercial customer registration, tenant records, billing and fleet orchestration in a **separate control-plane project**, not in the ODS system-administration API.

## License boundaries

Review the **exact deployed revisions** and intended form of delivery, hosting, and redistribution:
- Original ODS components: Apache-2.0 subject to per-file notices.
- Pixel: ODS-specific source-available license; never extract it into an unrelated standalone product.
- Open WebUI: version-specific branding restrictions and threshold/permission requirements.
- n8n: Sustainable Use License, with important hosting / embedding / customer-workflow limitations.
- Third-party models, service images, fonts and assets: their separate terms control.
- Reference: [ods/LICENSING.md](ods/LICENSING.md), [ods/docs/FORKABILITY.md](ods/docs/FORKABILITY.md), [ods/docs/THIRD-PARTY-LICENSING.md](ods/docs/THIRD-PARTY-LICENSING.md).

## Critical security boundaries

- No untrusted customer access to ODS dashboard, dashboard-api, Docker, host-agent, global n8n, raw shared gateway keys, or unrestricted agent execution.
- Never assume Open WebUI user accounts imply tenant isolation in Qdrant, n8n, agents or infrastructure APIs.
- No real customer files in the POC until security gates, consent/contract, data handling and privacy controls have been reviewed.
- Restrict host listeners, containers and management plane; use VPN/private networking for operators.
- Monitor upstream security advisories and pin **verified fixed commits** before deployment.

## Maintenance

Before each merge/update, record: date, upstream SHA, additions/removals, reviewed licenses and image/model digests, GPU/runtime assumptions, and validation results.

Recommended checks from `ods/`:
```bash
python3 scripts/audit-extensions.py --project-dir . --strict
python3 scripts/validate-generated-configs.py
python3 scripts/validate-golden-paths.py
git diff --check
```
Run relevant app, security, deployment and end-to-end suites **and** isolated installation/restore acceptance. Passing source-only tests is not proof of production readiness.
