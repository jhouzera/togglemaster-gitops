# togglemaster-gitops

Repositório central de **GitOps** da plataforma ToggleMaster, operado via **ArgoCD**.

## 🎯 Propósito
Agir como a "única fonte da verdade" (Single Source of Truth) para o estado desejado das aplicações no cluster EKS. Este repositório contém o Master Helm Chart `togglemaster`, que gerencia todos os microsserviços através da declaração de `values.yaml` por ambiente.

## ⚙️ Como Funciona
Este repositório é o "cérebro" das implantações e segue o padrão **Pull-based GitOps**.
O **ArgoCD**, que reside dentro do cluster EKS, monitora ativamente este repositório via Webhooks do GitHub. Quando o arquivo `values.yaml` de uma aplicação tem a versão de imagem alterada (geralmente por conta do bot do `togglemaster-apps`), o ArgoCD acorda instantaneamente.
Ele calcula o "drift" (a diferença entre o que está rodando no cluster versus o que está escrito nos arquivos YAML) e aplica a nova configuração interagindo com a API do Kubernetes. Se a mudança falhar no cluster, o próprio ArgoCD tem a capacidade de sinalizar o erro, mantendo tudo declarativo e auditável.

## 🚀 Como Utilizar

O ArgoCD (instalado no cluster) fica continuamente observando este repositório. Sempre que uma nova *tag* de imagem é promovida pela pipeline de CI (`togglemaster-apps`) via um novo Pull Request aqui, basta você aprovar o merge na `main` e o ArgoCD cuidará do *deployment* e do *rollout* no EKS automaticamente.

### Exemplo Simples de Atualização Manual (Escalando Pods)

Para escalar o `auth-service` manualmente sem mexer no código-fonte do app:
1. Edite o arquivo `environments/dev/apps/auth-values.yaml`
2. Modifique o campo de `replicas` de `2` para `4`
3. Comite a alteração para a branch `main`.
4. O ArgoCD atualizará o cluster instantaneamente usando o Webhook configurado.

## 🔐 Segurança e Boas Práticas
- O repositório armazena apenas **manifestos YAML declarativos** e *templates*. 
- Dados confidenciais são carregados dinamicamente no cluster via `ExternalSecret` (buscando direto do AWS Secrets Manager de forma criptografada), sem nunca passarem em texto puro neste repositório.
- A comunicação e roteamento HTTP ocorrem unicamente via **NGINX Gateway Fabric** e **HTTPRoutes** da API Gateway.
