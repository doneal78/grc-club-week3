\# Week 3: Build the Gate

Status: gate verified with a passing pull request on July 12, 2026.

A GitHub Actions workflow that runs the Week 2 Rego policies against a Terraform plan on every pull request and blocks merges when a compliance control fails.



\## What this gate enforces



Three NIST 800-53 controls, checked automatically on every pull request:



SC-28 Protection of Information at Rest: fails if any aws\_s3\_bucket has no matching encryption configuration.



AC-3 Access Enforcement: fails if any aws\_s3\_bucket has a missing or incomplete public access block.



CM-6 Configuration Settings: fails if any taggable resource is missing one of four required tags.



\## What happens when a control breaks



The workflow runs on every pull request targeting main. It installs Conftest at a pinned version, runs all three policy namespaces against the committed plan.json, and writes results to an evidence artifact regardless of pass or fail. If any policy reports a violation, the job exits non-zero and the pull request check goes red. With branch protection enabled and the grc-gate check marked required, a pull request with a red check cannot be merged by anyone until the control is fixed.



\## The two-PR proof



A compliant pull request was opened first to confirm the gate passes cleanly and the PR is mergeable.



A second pull request was opened with a plan.json regenerated from a workspace missing the SC-28 encryption configuration. The gate correctly failed, the check went red, and with branch protection enabled the merge button was blocked.



\## Why the plan is committed instead of generated in CI



This week uses the simple path: plan.json is generated locally and committed to the repo. This keeps the workflow free of any cloud credentials or secrets. The production version of this pipeline would have CI generate the plan itself by assuming an AWS role through GitHub OIDC, with no stored keys at all. That is a stretch goal for a future iteration.



\## Files



\- `.github/workflows/grc-gate.yml`: the CI workflow

\- `policies/`: the three Rego policies and their tests from Week 2

\- `terraform/`: the Week 1 Terraform configuration

\- `plan.json`: the committed compliant plan the gate runs against



\## Related work



Week 1: https://gitlab.com/doneal78-group/grc-engineering-portfolio/grc-club-week1

Week 2: https://gitlab.com/doneal78-group/grc-engineering-portfolio/grc-club-week2

