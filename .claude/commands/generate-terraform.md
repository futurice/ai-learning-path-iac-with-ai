# Generate Terraform Infrastructure Code

Generate or update Terraform code in the `infra/` directory based on the infrastructure design documents. Also produce `infra/README.md` with end-to-end provisioning instructions.

This command is **cloud-agnostic**. The target cloud (Azure, AWS, GCP, …) and all cloud-specific rules come from the design documents and the best-practice references read in Step 1 — not from this command. Apply whatever the design and the referenced guidance specify for the chosen provider.

## Step 1 — Read all inputs before writing anything

Read the following in order:

1. `docs/infra/low-level-design.md` — **primary source of truth** for the target cloud, all resource names, SKUs/instance types, configs, and provisioning order
2. `docs/infra/high-level-design.md` — architectural decisions, selected cloud, naming policy, service rationale
3. `docs/architecture.md` — original intent; use for context and to fill gaps not covered by the design docs
4. **Cloud- and project-specific guidance** — read everything under `@.claude/infra` (best practices, conventions, examples, and any cloud-specific Terraform standards). If the folder is empty or missing, ignore this and rely on the design docs plus the generic standards in Step 3.
5. `CLAUDE.md` — project conventions and critical rules (e.g. never commit secrets)

Also read any existing files under `infra/` so you can update them in place rather than overwriting work that is already correct.

**Establish the target cloud and its provider** from the design docs before writing anything. If the design docs do not name a cloud, stop and ask rather than assuming one.

## Step 2 — Identify what to generate or update

Compare the low-level design against the current state of `infra/`. For each resource or module defined in the LLD that is missing or out of sync, generate or update the corresponding Terraform. Do not touch files that are already consistent with the LLD.

If any value in the LLD is marked `TBD`, emit a Terraform comment `# TBD: <reason>` in the relevant location and do not invent a placeholder value.

## Step 3 — Terraform coding standards

Apply the cloud-specific standards from the `@.claude/infra` guidance and the design docs. **Where that guidance conflicts with the generic rules below, the cloud/project guidance wins.** The following are provider-agnostic baselines that hold for any cloud:

- **Provider and Terraform version pins:** always specify `required_providers` with an exact `~>` minor constraint for each provider, and a minimum `required_version` for Terraform itself.
- **Remote state backend:** configure a remote backend (the backend type and settings come from the LLD for the chosen cloud). Do not hardcode backend credentials or access keys — use the provider's recommended keyless/identity-based auth.
- **Modules:** one module per logical grouping as specified in the LLD Terraform layout. Each module directory must contain `main.tf`, `variables.tf`, and `outputs.tf`.
- **Naming:** all resource names must exactly match those in the LLD. Use `var.` references, not string literals, for names and values that differ per environment.
- **No hardcoded secrets:** no passwords, keys, tokens, or connection strings in any `.tf` or `.tfvars` file. Secrets come from the cloud's secrets manager (e.g. Key Vault, AWS Secrets Manager / SSM, GCP Secret Manager) or are injected at runtime.
- **Identity and access:** use the cloud's managed-identity / IAM-role mechanism for workload permissions rather than long-lived static credentials, following the access model in the LLD.
- **Tags / labels:** every taggable resource must include at minimum `environment = var.environment` and `project = var.project` (use the cloud's label mechanism where tags aren't supported).
- **Outputs:** expose the key endpoints and identifiers downstream consumers need (as defined by the LLD) from the root module.
- **Variable descriptions:** every `variable` block must have a `description`.
- **`count` / `for_each` must not depend on unknown values:** Terraform requires `count` and `for_each` to be deterministic at plan time. Never use a resource attribute (e.g. a resource ID output from another module) in a `count` or `for_each` condition such as `count = var.some_id != null ? 1 : 0`. Instead, add a separate `bool` variable (e.g. `enable_<feature>`) that the caller sets to `true` or `false` — a static value known at plan time — and use that for the conditional: `count = var.enable_<feature> ? 1 : 0`. Pass the resource ID in a separate variable for use inside the resource body.
- **Formatting:** all files must be valid `terraform fmt` output (2-space indentation, aligned `=` signs in blocks).

## Step 4 — File layout to produce

Follow the directory structure defined in the LLD's Terraform layout section exactly. At minimum produce:

```
infra/
├── terraform.tf          # backend + required_providers
├── main.tf               # root module; calls all child modules
├── variables.tf          # shared input variables (environment, region, project, …)
├── outputs.tf            # root-level outputs
├── terraform.tfvars      # non-sensitive variable defaults
├── environments/
│   ├── dev.tfvars        # dev overrides (compute size, replica counts, retention, etc.)
│   └── prod.tfvars       # prod overrides
└── modules/
    └── <one directory per module in the LLD>/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

## Step 5 — Generate `infra/README.md`

The README must be a complete, step-by-step guide for provisioning from zero, written for the **target cloud** identified in Step 1. Cover:

1. **Prerequisites** — tools required (Terraform version, the cloud's CLI and version, required permissions/roles on the account/subscription/project)
2. **One-time manual steps** — anything that cannot be done by Terraform and must be done by hand before the first `terraform apply` (e.g. creating the remote state backend bucket/storage + lock mechanism, bootstrapping identity/permissions, seeding secrets). List each step as a numbered action with the exact CLI command for the target cloud.
3. **First-time provisioning order** — explain that shared resources (registry, state backend, etc.) must be provisioned before environment-specific resources, matching the provisioning order in the LLD.
4. **Per-environment apply instructions** — exact commands to initialise and apply for each environment:
   ```bash
   terraform init -backend-config="key=<env>.tfstate"
   terraform apply -var-file="environments/<env>.tfvars"
   ```
5. **Updating a running environment** — how to re-apply after a config change.
6. **Destroying an environment** — `terraform destroy` with any caveats (e.g. deletion protection on stateful or production resources).
7. **Secrets management** — how to populate the cloud's secret store manually after provisioning; remind that secrets must never be committed to source control.

## Step 6 — Write all files

Write every generated or updated file. Then print a brief summary listing:
- The target cloud and provider(s) used
- Which files were created
- Which files were updated and what changed
- Any TBD items left in the generated code that require a human decision before `terraform apply` can succeed
