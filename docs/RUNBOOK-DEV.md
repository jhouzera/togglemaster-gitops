# Operacao GitOps - Dev

O bootstrap e a operacao do ArgoCD estao centralizados em `togglemaster-iac/docs/RUNBOOK-DEV.md`.

Este repositorio mantem exclusivamente os ApplicationSets, o chart Helm e os values de `dev`.
Altere-os por Pull Request para `main`; o workflow `Validate GitOps Dev` deve aprovar antes do merge.
