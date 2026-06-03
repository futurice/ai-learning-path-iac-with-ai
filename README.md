# AI Learning Module: AI-native decision making workflows with Claude code + IaC with AI

## What we will do
- Use specs-driven development methods
- Generate structured "High Level Design" document that has all the required decisions for cloud infrastructure
- Generate structured "Low Level Design" document based on the High Level Design
- Generate Terraform based on the specs

## What we will learn
- Slash commands and how they differ from skills
- Using cascading best practices
- Using references and separate documents for specific cases (for example separata Azure and AWS best practices)
- Iterative approach: how you can regenerate specification and code and improve it bit by bit
- Using AI to facilitate decision making

# Instructions

## Prerequisites

- Install Terraform and test that it works

## Exercise steps

1. Clone the repo, run claude in the cloned folder
2. Generating first drafts of the documentation
  - Run "/generate-infra-design" and explain that you are just started and very rudimentary High Level Design is enough for now
3. Review the documents generated
4. Ask AI to facilitate decison making
  - Run "/generate-infra-design" and ask AI to list all decisions that must be decided before Terraform code can be created and applied, ask it to spar the answers with you
5. Generate Terraform
  - /generate-terraform
6. Modify High Level Design, either manually or ask AI to redecide some aspect, for example location (from West Europe to Sweden Central or vice versa)
7. Rerun both the specification generation and Terraform generation
  - see how new decisions change the Low Level Design and Terraform code

## Bonus steps

Increase the quality of Terraform generation
- Add best practices
  - Add Terraform cloud specific best practices to .claude/references/azure|aws-best-practices.md
  - You can ask AI to generate it
- Add MCP
  - Use Hashicorp Terraform or Microsoft Azure MCP server

Increase the quality of document generation
- Modify tone of generated documents
  - Modify slash commands, for example ask AI to stop writing in active first person ("I will update this...", "I decided to use xxx...") and to use passive 3rd person instead ("needs updating", "xxx is used")
- Modify CLAUDE.md to give project specific rules
  - Use examples. Read the generated document and those bits that you hate, add to CLAUDE.md and explain how they should be different

Apply to your cloud
- You need working Azure subscrption or AWS account
- Follow the generated README.md to bootstrap

Create feedback loop
- If/when there are errors in Terraform plan or apply, feed those errors and their solutions to AI (for example to CLAUDE.md or  .claude/references)
