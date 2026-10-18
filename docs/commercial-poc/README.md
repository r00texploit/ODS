# Private AI Workspace — commercial proof-of-concept plan

**Status:** Discovery + architecture baseline. No production readiness claimed.

## Business hypothesis

A UAE small business will pay for a managed, private AI workspace that answers questions from its *approved shared company documents* with citations, drafts routine content, and reduces manual searching. The customer pays for deployment, configuration, security, support and measurable time savings — not simply an ODS installer.

First target segment: small consulting/professional-services businesses with repetitive document questions. Pricing hypothesis: AED 2,500 setup + AED 3,000/month **plus separately accounted infrastructure/model usage**. These are experiments, not validated market prices or margin assumptions.

**Discovery gate:** Interview 10 qualified prospects, run 2 credible demos, and seek 1 paid pilot commitment before developing general-purpose SaaS features.

## Minimal POC scope

- Dedicated single-customer ODS instance; test with synthetic sample data only.
- Employee chat with authenticated individual accounts.
- Approved shared documents indexed for question answering; citations and no-answer behavior tested.
- Bounded local inference or explicitly disclosed customer-approved provider routing.
- Separate operator-only administration; no customer access to ODS infrastructure dashboard.
- Model availability, basic latency/throughput metrics, backup/restore prototype, deployment receipt.
- Clear attribution and legal notices on all applicable components.

**Out of scope:** shared multi-tenant ODS, public self-signup, Stripe/subscriptions, open agent-shell access, Pixel Full Access, shared n8n builder, employee-private vector namespaces, per-tenant GPU scheduling, white-label removal of required upstream marks, unreviewed model licensing.

## Architecture

```text
Company users -> TLS ingress -> authenticated chat -> dedicated customer ODS runtime
                                                -> permitted LLM inference
                                                -> company-specific document store

Business admin/operator -> private VPN -> dedicated ODS admin plane
Future commercial control plane (separate repo): organizations / billing /
         deployment orchestration / fleet monitoring / backup audit records.
```

Within an individual company, `Open WebUI` supports user accounts. That does **not** establish row-level security for ODS's other services. For the POC, shared company documents are intentional; keep unrelated customer information on physically/logically separate instances. Prefer dedicated VMs as the cloud isolation boundary over merely separating Compose projects on one host.

## First technical vertical slice

1. Pin and audit upstream commit, check advisories, inspect deployed component licenses and asset terms. Avoid any customer-facing exposure.
2. Set up ODS on an isolated development machine/VM with generated strong secrets. Verify ports are bound to loopback as intended and customer networks cannot reach privileged APIs.
3. Configure a commercial-use-permitted inference model, recording exact version/weights/license and whether inference is on-device or remote.
4. Enable employee sign-in. Replace any bootstrap/default administrator credentials and keep signup closed.
5. Load only synthetic sample company files. Confirm answers reference the intended source and decline unsupported requests rather than fabricate citations.
6. Demonstrate 3 scripted tasks: policy lookup, source-grounded answer, and document-derived draft. Measure error rate, latency, onboarding effort and support time.
7. Validate backup + clean restore. Save deployment SHA, image/model digests, tested OS/GPU, logs, and redacted evidence.
8. Demonstrate to prospects. Get paid pilot scope in writing before using genuine client data.

## Mandatory release gates

### Security
- [ ] No known applicable critical/high advisory unremediated in chosen revision/dependencies.
- [ ] No customer reachability for `dashboard-api`, host-agent, Docker socket, n8n/admin tools, inference master credentials or unlocked raw model APIs.
- [ ] Verified host/container port bindings and provider-side network rules; do not assume UFW alone isolates Docker published ports.
- [ ] Per-user chat login tested; no factory `admin/admin` remains.
- [ ] Agent shell execution/Full Access disabled or independently reviewed and sandboxed.
- [ ] Negative tests: unauthorized session, horizontal data access, credential leak, prompt injection/tool escalation.
- [ ] Unique secrets per customer instance; rotate/revoke with retained audit evidence.

### Data and operations
- [ ] Signed data processing and support-access terms, documented retention and model-provider disclosures.
- [ ] Backups encrypted; at least one restoration on a clean machine succeeds.
- [ ] Restart, crash recovery, upgrade/rollback and disk exhaustion tests recorded.
- [ ] Realistic concurrent sessions load-tested against GPU/context/latency limits, not inferred from number of users.
- [ ] No real client data used in developer CI or demonstrations without explicit permission.

### Licensing
- [ ] Original ODS and Pixel license notices retained; ODS/Pixel boundary respected.
- [ ] Open WebUI deployment and branding compliant with the installed version's terms.
- [ ] n8n left internal/disabled unless the intended customer-facing use is licensed.
- [ ] Model weights, container images, generated assets and third-party libraries reviewed for commercial purpose and distribution.

## Pilot acceptance metrics (proposed thresholds, to validate with buyer)

- At least 15 agreed benchmark questions; 90% answered accurately with source citations, abstain when evidence is absent.
- No high-severity findings in scoped security tests, and documented data-flow map accepted by the pilot company.
- Under an agreed load profile, p95 response time meets the signed target; no arbitrary performance guarantee beforehand.
- Restore one customer instance from backup, prove data integrity, and document recovery time.
- Pilot user demonstrates time savings on at least one recurring workflow; customer confirms the value proposition.

## Repository implementation boundaries

Use ODS's documented extension and registry seams:
- `ods/extensions/services/<service-id>/manifest.yaml` + `compose.yaml`
- `ods/extensions/templates/`
- `ods/config/model-library.json` (license-vetted catalog only)
- `ods/extensions/services/dashboard/src/` for permitted operator UI branding
- `ods/config/network-exposure-policy.json` for review of network exposure
- `ods/docs/BUILD-ON-ODS-SERVER.md` and `ods/docs/FORKABILITY.md` as the fork contract

Avoid editing generated `ods/data/` files as source; do not rewrite `ods/docker-compose.base.yml`, host-agent auth, or installer phases before documenting need, threat model and relevant cross-platform regression tests.

## Milestones

**M1 — Verified baseline:** pinned source, license inventory, security gate, reproducible local install and synthetic-data demo.

**M2 — Paid design partner:** discovery, agreed deliverables and a signed scoped pilot contract.

**M3 — Operational pilot:** dedicated private deployment, hardened access, verifiable document answers, backups and user onboarding.

**M4 — Repeatability:** second isolated company instance from automation; measured setup time, support load and gross-margin economics.

Do not advertise as a public SaaS, certified-secure, fully private, or entirely open source solely on the strength of upstream ODS's repository or this plan.
