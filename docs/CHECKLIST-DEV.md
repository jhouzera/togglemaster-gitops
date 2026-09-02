# Checklist de Integracao GitHub + GitOps (Ambiente Dev)

## 1. Preparacao inicial
- [ ] Confirmar que o ArgoCD esta provisionado e acessivel no cluster `dev`.
- [ ] Confirmar que o repositório `togglemaster-gitops` esta com os ApplicationSets versionados.
- [ ] Confirmar que o chart Helm `charts/togglemaster` esta consistente.

## 2. Estrutura obrigatoria do repositório
- [ ] `bootstrap/applicationsets/apps.yaml` presente.
- [ ] `charts/togglemaster/Chart.yaml` presente.
- [ ] `charts/togglemaster/values.yaml` presente.
- [ ] `charts/togglemaster/apps/*-values.yaml` presente para cada microsservico.

## 3. Configuracao de Actions no GitHub
Em `Settings > Actions > General`:
- [ ] Execucao de workflows permitida.
- [ ] Actions de terceiros permitidas pela organizacao.
- [ ] Permissoes de workflow adequadas para automacao de validação.

## 4. Protecao da branch main (recomendado)
- [ ] Exigir Pull Request para merge.
- [ ] Exigir revisão de codigo.
- [ ] Exigir checks obrigatorios antes de merge.
- [ ] Bloquear merge com checks falhando.

## 5. Integracao com togglemaster-apps
- [ ] Confirmar que o token usado em `togglemaster-apps` tem escrita neste repositório.
- [ ] Confirmar que o arquivo de values correto e atualizado por microsservico.
- [ ] Confirmar que alteracoes automatizadas de tag chegam na branch esperada (`main`).

## 6. Validacao dos ApplicationSets
- [ ] `apps.yaml` define um Application por microsservico.
- [ ] Cada Application dos microsservicos referencia um `values.yaml` dedicado.
- [ ] O Application de `edge` esta isolado para Gateway/rotas.

## 7. Validacao no ArgoCD
- [ ] Confirmar existencia dos ApplicationSets no namespace `argocd`.
- [ ] Confirmar criacao das Applications de microsservicos.
- [ ] Confirmar `Sync Status = Synced` e `Health = Healthy` para todos os Applications.
- [ ] Confirmar auto-sync habilitado (`prune` e `selfHeal`).

## 8. Teste funcional de sincronizacao
- [ ] Alterar manualmente um `*-values.yaml` de microsservico em PR.
- [ ] Fazer merge na `main`.
- [ ] Confirmar sincronizacao automatica do ArgoCD e rollout do deployment correspondente.

## 9. Troubleshooting rapido
- [ ] Se ApplicationSet nao gerar Applications: revisar sintaxe e permissao do ArgoCD ao repo.
- [ ] Se app nao sincronizar: revisar path do chart, values e branch monitorada.
- [ ] Se imagem nao atualizar: confirmar update do values vindo de `togglemaster-apps`.

## 10. Criterio de pronto
- [ ] Microsservicos sincronizam automaticamente no `dev`.
- [ ] Atualizacoes de tag vindas do CI sao aplicadas sem intervenção manual.
- [ ] Estrutura GitOps permanece segregada por Application e por dominio.
