# Runbook de Operacao do Repositorio GitOps (Dev)

## Objetivo
Operar a entrega contínua declarativa dos microsservicos e da borda no ambiente `dev` via ArgoCD.

## Escopo
- ApplicationSets de addons e apps.
- Helm chart unico com values dedicados por microsservico.
- Sincronizacao automatica no cluster EKS.

## Pré-requisitos
- ArgoCD instalado e funcional no cluster `dev`.
- Repositório `togglemaster-addons` acessivel para leitura pelo ArgoCD.
- Imagens semver publicadas pelo `togglemaster-apps` no ECR.

## Estrutura esperada
- `bootstrap/applicationsets/addons.yaml`
- `bootstrap/applicationsets/apps.yaml`
- `charts/togglemaster/values.yaml`
- `environments/dev/apps/*-values.yaml`

## Procedimento de execução

### 1. Alterações em chart/manifests
1. Criar branch e aplicar alteração no chart ou ApplicationSet.
2. Abrir Pull Request para `main`.
3. Revisar impacto de sincronizacao em `addons` e `apps`.
4. Fazer merge após aprovação.

### 2. Sincronizacao no ArgoCD
1. Confirmar que os ApplicationSets estão `Synced`.
2. Confirmar que os Applications gerados estão `Healthy`.
3. Validar que cada microsservico continua apontando para seu arquivo de values dedicado.

### 3. Atualização de tags via CI
1. Confirmar que o `togglemaster-apps` publicou a imagem semver no ECR.
2. Confirmar Pull Request de promocao com tag e digest em `environments/dev/apps/`.
3. Confirmar que o workflow `Validate GitOps Dev` foi aprovado.
4. Confirmar que o ArgoCD reconciliou a nova imagem apos o merge.

## Comandos de apoio

```bash
# Estado de applications e applicationsets
kubectl get applications,applicationsets -n argocd

# Verificar status por aplicação
kubectl get applications -n argocd -o wide
```

## Troubleshooting
- ApplicationSet nao gera app: revisar sintaxe YAML e permissões de repo.
- App nao sincroniza: revisar `path`, `targetRevision` e `valueFiles`.
- Imagem nao atualiza: revisar o Pull Request gerado pelo `togglemaster-apps` e seu status de merge.
- Application edge nao atualiza imagem: isso e esperado; `edge` nao possui imagem de aplicacao.

## Critério de sucesso
- ApplicationSets e Applications em estado `Synced/Healthy`.
- Atualizacao de tag no GitOps refletida automaticamente no cluster.
- Segregacao de administração por microsservico mantida.
