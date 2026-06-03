# What is Personal Expense Dashboard

A dashboard where users can log daily expenses, categorize them, and view spending trends via interactive charts.

## Tech Stack

- Python (Django)
- HTMX
- Tailwind CSS
- Chart.js
- PostgreSQL

## Architecture & Infra Resources

**Application Hosting**: Azure App Service (Linux Web App)

**Database**: Azure Database for PostgreSQL (Flexible Server)

**Static Assets & Media**: Azure Blob Storage

**Secrets Management**: Key Vault

### IaC

- Terraform is used
- Remote state will be hosted in Azure, environment specific storage account
- You should be able to plan and apply terraform from developer's machine and also via pipelines
