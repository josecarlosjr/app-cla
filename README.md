# app-cla
## simple app version 1

### 1. Arquitetura e repositórios

Você separou o fluxo em dois repositórios:

- app-cla-image: build e push das imagens

- app-cla: manifests Kubernetes/GitOps

A estrutura com frontend e backend desacoplados foi mantida, com um Dockerfile para cada serviço.

### 2. Build e publicação de imagens

- O workflow do GitHub Actions no repositório de imagens foi criado e passou a funcionar.

- O pipeline de build/push para o Docker Hub foi validado.

Os repositórios no Docker Hub existem e foram usados:

- josecarlosjr/app-cla-backend

- josecarlosjr/app-cla-frontend

- As imagens foram publicadas com tags do tipo sha-....

### 3. Backend

O problema inicial do build do backend foi resolvido.

O backend foi construído e publicado.

O endpoint de health foi validado com sucesso:

- https://dashboard.local/api/health

- retorno OK com JSON (status: healthy)

### 4. SOPS + age

Você abandonou Sealed Secrets e seguiu com SOPS + age.

No servidor Ubuntu ARM64 MASTER01:

- age ficou instalado

- sops foi instalado manualmente e está funcional

- A chave age foi gerada com sucesso.

A chave privada foi armazenada no cluster como secret:

- argocd/sops-age

### 5. ArgoCD + plugin CMP + KSOPS

O Config Management Plugin do ArgoCD foi configurado.

O argocd-repo-server foi ajustado com:

- sidecar do plugin

- montagem da chave do SOPS

- correção de permissões do socket

- ajuste de *KUSTOMIZE_PLUGIN_HOME*

O plugin passou a funcionar com o nome:

sops-kustomize-v1.0

### 6. Repositório GitOps K8s

- O arquivo .sops.yaml foi movido para a raiz do repositório app-cla.

- O secret k8s/01-secret.yaml foi criptografado com SOPS com sucesso.

- O ksops-generator.yaml foi criado.

- O kustomization.yaml passou a usar o gerador KSOPS.

### 7. Git

O problema de push via HTTPS foi resolvido.

Você conseguiu:

- configurar identidade git

- fazer commit

- fazer push corretamente para o GitHub

### 8. Deploy com ArgoCD

A Application app-cla foi criada no ArgoCD.

O ArgoCD conseguiu:

- ler o repositório

- descriptografar o secret com SOPS

- gerar manifests

- sincronizar com sucesso

O deploy criou os recursos no namespace dashboard-app:

- Namespace

- Secret

- Services

- Deployments

- HPAs

- Ingress

### 9. Cluster / aplicação

- Os manifests foram corrigidos ao longo do caminho (incluindo erro de apiVersion no backend).

- As imagens dos deployments foram ajustadas para usar imagens reais do Docker Hub.

- Em um momento posterior, os pods ficaram Running e a aplicação passou a responder.

### 10. Acesso pela rede

O acesso externo foi validado através do seu HAProxy.

Ficou claro que externamente você deve usar:

- porta 443 do HAProxy e não o NodePort diretamente

- O hosts no Windows foi configurado e validado:

- dashboard.local resolve para 192.168.1.210

- O ping dashboard.local funcionou.

- O acesso HTTPS ao domínio passou pelo HAProxy/Ingress corretamente.

### 11. Diagnóstico final atual

A página em branco foi diagnosticada corretamente.

A causa encontrada foi objetiva:

o arquivo frontend/index.html no repositório app-cla-image está vazio (0 lines, 1 byte)

Isso explica por que:

- a API responde

- a rede funciona

- mas a página principal fica em branco


# Markdown
- [x] Github actions - app-cla-images
- [x] Dockerhub - app-cla-images
- [x] Backend publicado 
- [x] SOPS + age (instalado no master server)
- [x] Integração SOPS no ArgoCD
- [x] sync GitOps com ArgoCD
- [x] Deploy no cluster
- [x] Acesso via HAproxy + Ingress
- [x] Backend acessível pela URL final
- [x] Frontend funcional (*Falta*)
- [ ] ISTIO (*Falta*)

