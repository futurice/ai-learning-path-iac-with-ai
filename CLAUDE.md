# Project summary

Personal Expense Dashboard

A dashboard where users can log daily expenses, categorize them, and view spending trends via interactive charts.

**Note: This is an imaginary application. None of the code exists yet. Play along. This mock application will be used to test AI tooling, so don't worry about the missing code.**

## Tech Stack

Explained in the `docs/architecture.md`

## Important folders

- .claude/references: cloud specific best practices and our preferred ways. Can be empty, in that case generic best practices are used
- docs/infra: high level design and low level design documents for the cloud infra
- infra: Terraform code

## Critical Rules

- NEVER commit `.env` files, secrets, or real credentials. Use `.env.example`
  for documenting required variables.
- Don't read any files in folder `hidden`