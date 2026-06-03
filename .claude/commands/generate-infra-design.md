# Generate Infrastructure Design Documents

This command generates and modifies cloud architecture documentation. 

## Step 1 — Understand

Do following:
- Understand project architecture. Read it from `docs/architecture.md`
- Read best practices and examples from `.claude/infra`. If the folder is empty, ignore this

Always read `CLAUDE.md` for any project-level conventions (naming, tooling, critical rules).

## Step 2 — Generate or modify `docs/infra/high-level-design.md`

This document captures **strategic and architectural decisions**. It does not spell out every detail — that belongs in the low-level design. At the beginning this file might be empty or very rudimentary. Essential part of the process is that human engineer discusses with client and gets relevant information needed to do the missing decisions.

Cover:

- Purpose and scope of the cloud infrastructure
- Functional and non-functional requirements (availability, scalability, security, cost targets)
- Selected cloud (e.g. Azure, AWS, GCP)
- Cloud services selected and why (e.g. Container Apps vs AKS, Key Vault, ACR)
- Networking model (public vs private, ingress rules, VNet/subnet topology at a conceptual level)
- Identity and access model (managed identities, RBAC strategy)
- Environment strategy (dev / prod, isolation approach)
- IaC tooling decision and rationale
- Naming convention policy
- Cost vs security trade-off decisions
- Open questions or decisions deferred to low-level design

Keep entries concise. Use tables or bullet lists where they aid clarity. Mark any section that still needs a decision with `> **TBD:** <what needs to be decided>`.

Note: Process should be iterative. It is not common to be able to create perfect high level design document from scratch. Usually it is first quite rudimentary and simple and will grow from there.

If there are missing decisions, use one of this methods:
- Start by asking the most important questions, like which cloud to use
- Ask the user, give options. Include following options: that you think is the best, simplest to get started and ability to skip (will be marked as **TBD**)
- Not everything needs to be done in one run. You can always save the file and this command can be then rerun

## Step 3 — Generate or modify `docs/infra/low-level-design.md`

This document must be **complete enough to provision cloud infrastructure from zero without making any additional decisions**. It must be consistent with the high-level design (same naming policy, same service choices, same environment strategy). Cover:

- Full resource inventory per environment (resource names, SKUs, regions, tiers)
- Naming convention applied concretely (show actual resource name examples)
- Network topology: VNet CIDR, subnet names and CIDRs, NSG rules, private endpoints if any
- Container Apps environment config: workload profiles, ingress settings per app, CPU/memory allocations
- Container Registry config: SKU, geo-replication if any, image naming convention
- Key Vault config: SKU, access policies or RBAC roles, secret naming convention
- Managed identity assignments: which identity gets which role on which resource
- Terraform layout: module structure, state backend config (storage account, container, key), workspace or directory strategy for environments
- Secrets and environment variables: which values come from Key Vault, which are injected at deploy time, none hardcoded
- Startup and health-check configuration for each container
- Any resource dependencies and provisioning order

For every resource, provide a concrete name following the naming policy from the high-level design. If a value is genuinely unknown, write `TBD: <reason>` — do not invent placeholder values.

## Step 4 — Write the files

Write the generated content to:
- `docs/infra/high-level-design.md`
- `docs/infra/low-level-design.md`

After writing, print a short summary of what changed or was added in each file.
