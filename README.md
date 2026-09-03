# togglemaster-gitops

Repositorio GitOps da plataforma ToggleMaster.

Proposito:
- Declarar o estado desejado das aplicacoes no cluster EKS.
- Separar a gestao de entrega continua da base de codigo das aplicacoes.

Responsabilidades:
- Helm Charts dos microsservicos.
- ApplicationSets do ArgoCD.
- Bootstrap declarativo de sincronizacao.

Modelo operacional:
- Um Application por microsservico.
- Um values.yaml dedicado por microsservico consumindo o mesmo chart Helm.
- Separacao de ownership por dominio funcional.
- Integracao com External Secrets Operator e Stakater Reloader.
- Application raiz `togglemaster-apps` para o ApplicationSet dos microsservicos.
- Promocao de imagens por Pull Request criado pelo GitHub Actions.
- Checklist operacional no ambiente dev: `docs/CHECKLIST-DEV.md`.
- Runbook operacional no ambiente dev: `docs/RUNBOOK-DEV.md`.

Dependencias externas:
- E monitorado pelo ArgoCD provisionado pelo `togglemaster-iac`.
- O `togglemaster-apps` publica imagens somente com tags `vMAJOR.MINOR.PATCH`.
- O workflow de Apps coleta o digest da imagem e cria um Pull Request que atualiza o values file correspondente.
- O `togglemaster-secrets-generator` cria secrets no AWS Secrets Manager; o External Secrets
	Operator, instalado pelos addons, os disponibiliza no cluster.
## Promocao de imagens

Os values dos microsservicos ficam em `environments/dev/apps/`. O workflow reutilizavel
`update-gitops.yml` valida o servico, a tag, o digest e o values alvo antes de abrir um Pull
Request para `main`. O workflow `Validate GitOps Dev` valida o YAML e renderiza o chart Helm
antes do merge. O ArgoCD precisa somente de acesso de leitura ao repositorio.

Os addons sao instalados localmente pelo script do repositorio `togglemaster-addons` e nao sao
reconciliados pelo ArgoCD.
