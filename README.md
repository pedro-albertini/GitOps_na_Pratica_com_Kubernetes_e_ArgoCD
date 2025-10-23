# 🚀 GitOps na Prática com Kubernetes e ArgoCD

> Projeto de deploy automatizado da aplicação **Online Boutique (Google)** usando **GitOps**, com **Kubernetes local (Rancher Desktop)** e **ArgoCD**.

<br>

## 🧭 Introdução

O desenvolvimento moderno exige **entregas rápidas, seguras e versionadas**.  
Empresas como **Netflix** e **Nubank** utilizam o **Kubernetes** para orquestrar containers em escala e adotam **GitOps** para automatizar seus deploys — tendo o **Git como a única fonte de verdade** da infraestrutura.

Este projeto demonstra exatamente isso, em um ambiente local, utilizando:

- 🐳 **Rancher Desktop** — para rodar Kubernetes localmente  
- ⚙️ **ArgoCD** — para aplicar o modelo GitOps  
- 🧩 **Online Boutique** — aplicação de microserviços da Google Cloud  
- ☁️ **GitHub** — repositório público com os manifests Kubernetes

<br>

## 🎯 Objetivo

Implantar e versionar a aplicação **Online Boutique** em um cluster Kubernetes local, totalmente controlado via **ArgoCD** e **GitHub**, aplicando o conceito de **GitOps**.

<br>

## ⚙️ Pré-requisitos

Antes de iniciar, tenha os seguintes itens instalados e configurados:

| Ferramenta | Função | Verificação |
|-------------|--------|--------------|
| **Rancher Desktop** | Cluster Kubernetes local | Kubernetes habilitado |
| **kubectl** | CLI para interagir com o cluster | `kubectl get nodes` |
| **Git** | Controle de versão | `git --version` |
| **GitHub** | Hospedagem do repositório | Conta pública criada |
| **Docker** | (opcional) Testes locais | `docker ps` |

<br>

## 🧩 Etapa 1 – Criar o repositório Git de manifests

1. Faça o **fork** do repositório oficial:
   
   🔗 [microservices-demo (Google)](https://github.com/GoogleCloudPlatform/microservices-demo)
   
2. Crie um **novo repositório** no seu GitHub, chamado por exemplo:
   
   `gitops-microservices`
   
3. Estrutura de pastas recomendada:
   
   gitops-microservices/ <br>
    └── k8s/ <br>
    └── online-boutique.yaml
  
4. Copie o conteúdo do arquivo original:
   
- [`release/kubernetes-manifests.yaml`](https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/release/kubernetes-manifests.yaml)
- Cole dentro de `k8s/online-boutique.yaml`
  
5. Faça o commit e push:
   
```bash
git add .
git commit -m "Adiciona manifests do Online Boutique"
git push -u origin main
```

Ou simplesmente copie como está nesse repositório

<br>

## ⚙️ Etapa 2 – Instalar o ArgoCD no cluster

No terminal:

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verifique os pods:

```
kubectl get pods -n argocd
```

Todos devem aparecer com status Running.✔️

<br>

## 🌐 Etapa 3 – Acessar o painel do ArgoCD

Crie o port-forward:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Acesse no navegador:
🔗 https://localhost:8080

E você verá uma página assim:

<img width="1914" height="auto" alt="image" src="https://github.com/user-attachments/assets/e0b237f1-3ba2-4c50-9e05-308189c35541" />

<br>

Credenciais padrão:

Usuário: admin

Senha: <use o comando abaixo para descobrir>

```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

<br>

## 🧱 Etapa 4 – Criar o App no ArgoCD

No painel do ArgoCD, clique em NEW APP

Preencha os campos:

| Campo | Valor | 
|-------------|--------|
| **Application Name** | online-boutique | 
| **Project** | default | 
| **Repository** | https://github.com/SEU_USUARIO/gitops-microservices.git | 
| **Revision** | main | 
| **Path** | k8s | 
| **Cluster URL** | https://kubernetes.default.svc | 
| **Namespace** | default | 


Clique em Create

Depois, clique em SYNC → SYNCHRONIZE
