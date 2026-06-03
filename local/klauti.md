## Open Questions / Not Yet Decided

- **Where to host?** No cloud provider chosen (AWS / GCP / Azure / Fly / Render
  / Vercel + managed Postgres all on the table).
- **How to deploy?** No CI/CD pipeline exists yet. No container registry,
  no infra-as-code.
- **Managed vs. self-hosted Postgres** in production — undecided.
- **Auth** is currently a local stub (single demo user). A real auth strategy
  (sessions vs. JWT, provider, etc.) is TBD.
- **Environments** (staging/prod), secrets management, observability, and
  backups are all not yet designed.

When working on hosting/deploy topics, treat these as decisions to be made — ask
or propose options rather than assuming an existing setup.

---

commands

Read these files before generating anything:
- `docs/infra-specs/1-initial-idea.md` — project intent and initial decisions (source of truth)
- `docs/infra-specs/2-high-level-design.md` — existing high-level design (update if it exists, create if it doesn't)
- `docs/infra-specs/3-low-level-design.md` — existing low-level design (update if it exists, create if it doesn't)

Also read `CLAUDE.md` for any project-level conventions (naming, tooling, critical rules).

5. `docs/infra-best-practices/` — read every file in this folder if it exists and is non-empty; treat its contents as hard coding-style requirements that override any defaults below. If the folder is absent or empty, skip it and apply the defaults in Step 3.

---

KOKEMUKSET:

- SIMPPELÖITÄ APPLIKAATIOTA IHAN HELVETISTI! Tyyliin 99%!!!!
- Laita high level designiin jo pari arvoa
- Mieti tosi simppeli workflow läpi
  - aja kerran
  - luo terraform
  - muuta hieman
  - aja uudestaan
  - kopioi best-practice tiedostot hidden-kansiosta
  - lisää skilliin, että se lukee ne
  - aja uudestaan
- Jätä kotitehtäväksi miettiä miten laatua parannetaan