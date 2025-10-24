# 🚀 GitOps na Prática com Kubernetes e ArgoCD

> Projeto de deploy automatizado da aplicação **Online Boutique (Google)** usando **GitOps**, com **Kubernetes local (Rancher Desktop)** e **ArgoCD**.

<br>

<p align="center">
  <img src="https://skillicons.dev/icons?i=kubernetes,github" />
  <img src="https://argo-cd.readthedocs.io/en/stable/assets/logo.png" width="48" height="48" alt="ArgoCD"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/rancher/rancher-original.svg" width="48" height="48" alt="Rancher Desktop"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/yaml/yaml-original.svg" width="48" height="48" alt="YAML"/>
</p>

<br>

## 🧭 Introdução

O desenvolvimento moderno exige **entregas rápidas, seguras e versionadas**.  
Grandes empresas utilizam o **Kubernetes** para orquestrar containers em escala e adotam **GitOps** para automatizar seus deploys — tendo o **Git como a única fonte de verdade** da infraestrutura.

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

Ou copie a estrutura de pastas e o arquivo .yaml conforme mostrado neste repositório

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
🔗 http://localhost:8080

E você verá uma página assim:

| <img width="1914" height="540" alt="image" src="https://github.com/user-attachments/assets/e0b237f1-3ba2-4c50-9e05-308189c35541" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Painel ArgoCD* |

Credenciais padrão:

- Usuário: admin

- Senha: (use o comando abaixo para descobrir)

```
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

<br>

## 🧱 Etapa 4 – Criar o App no ArgoCD

No painel do ArgoCD, clique em NEW APP

Preencha os campos de acordo com a tabela a seguir:

| Campo | Valor | 
|-------------|--------|
| **Application Name** | online-boutique | 
| **Project** | default | 
| **Repository** | https://github.com/SEU_USUARIO/gitops-microservices.git | 
| **Revision** | main | 
| **Path** | k8s | 
| **Cluster URL** | https://kubernetes.default.svc | 
| **Namespace** | default | 

<br>

Ficando assim o preenchimento dos campos no ArgoCD:

| <img width="1883" height="871" alt="image" src="https://github.com/user-attachments/assets/7c96af3a-dddf-41a0-a188-34f3f6c4eb41" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Configuração Aplicação ArgoCD* |

| <img width="1919" height="866" alt="image" src="https://github.com/user-attachments/assets/9e18e7b2-04b1-466a-b915-c41c20b0ee11" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Configuração Aplicação ArgoCD* |

| <img width="1919" height="856" alt="image" src="https://github.com/user-attachments/assets/35cbf254-70ea-419a-9543-34d45a4f3df1" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Configuração Aplicação ArgoCD* |

- Clique em Create

- Depois, clique em SYNC → SYNCHRONIZE

- Verifique se os pods da aplicação estão rodando:

```
kubectl get pods
```

<br>

## 🖥️ Etapa 5 – Acessar o front-end (ClusterIP)

O serviço frontend roda como ClusterIP, portanto não é acessível externamente por padrão.
Para testar localmente, faça o port-forward:
  
```
kubectl port-forward svc/frontend 8081:80
```

Acesse no navegador:
🔗 http://localhost:8081

E você verá sua aplicação rodando:

| <img width="1917" height="868" alt="image" src="https://github.com/user-attachments/assets/f09e2683-d636-49de-8f46-4a6e7b4a36c6" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Aplicação Rodando* |

<br>

💡 Como o ArgoCD está sincronizado com o repositório Git, qualquer mudança feita no arquivo online-boutique.yaml (como número de réplicas, nome de serviço ou imagem do container) é detectada automaticamente e aplicada no cluster.

Como nesse caso, a mudança da logo e a frase "ArgoCD sincronizado":

| <img width="1919" height="866" alt="image" src="https://github.com/user-attachments/assets/9c8135df-3576-4bf8-a49e-2ca57dbc4c78" /> |
|-------------------------------------------------------------------------------------------------------------------------|
| *Figura - Aplicação Rodando* |

<br>

## 🧾 Conclusão

O projeto GitOps na Prática com Kubernetes e ArgoCD mostra, de forma simples e direta, como automatizar todo o processo de colocar uma aplicação no ar. Usando o Kubernetes para rodar os serviços e o ArgoCD para cuidar dos deploys, tudo fica controlado pelo Git, que guarda as versões e aplica as mudanças sozinho. Assim, o trabalho fica mais seguro, rápido e organizado.

---
🧑‍💻 Desenvolvido por [Pedro Albertini Fernandes Pinto](https://github.com/pedro-albertini)  
Projeto prático do módulo **GitOps com Kubernetes e ArgoCD**

