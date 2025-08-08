# ☸️ KubeNews com Kubernetes – Maratona DevOps + IA

Este repositório contém o **Lab do Dia 2** da Maratona DevOps + IA com [Fabricio Veronez](https://www.linkedin.com/in/fabriciovenorez/), onde aplicamos na prática o uso de **Kubernetes** para orquestrar a aplicação KubeNews, promovendo escalabilidade, resiliência e automação declarativa.


---

## 📌 Objetivos

- Criar e configurar um cluster Kubernetes local e na nuvem.
- Implantar uma aplicação em containers com escalabilidade e resiliência.
- Utilizar `kubectl` para gerenciar objetos do cluster.
- Entender a arquitetura do Kubernetes.
- Testar serviços do tipo LoadBalancer.
- Simular cenários de falha e recuperação automática (resiliência).
- Utilizar o Ask Gordon para análises do cluster.

---

## ☸️ Arquitetura Kubernetes

### 🔹 Cluster (Conjunto de máquinas)
- **Control Plane**
  - API Server
  - etcd
  - Scheduler
  - Controller Manager
- **Worker Node**
  - Kubelet
  - Kube-Proxy
  - Container Runtime (ContainerD, CRI-O)

---

## 🛠️ Tecnologias Utilizadas

- Kubernetes (kubectl)
- Digital Ocean (Kubernetes as a Service)
- Minikube / Kind (para testes locais)
- Chocolatey (Instalação no Windows)
- Visual Studio Code

---

## 🧱 Componentes criados

| Objeto Kubernetes | Descrição |
|-------------------|-----------|
| **Namespace**     | Isolamento lógico dos recursos da aplicação |
| **Deployment**    | Gerencia os pods e réplicas da aplicação |
| **ReplicaSet**    | Garante que o número desejado de pods esteja sempre em execução |
| **Pod**           | Unidade mínima de execução, onde o container roda |
| **Service**       | Expõe os pods para acesso externo via LoadBalancer |

---


## 📦 Estrutura do Projeto `devops-kubenews-k8s`

```plaintext
devops-kubenews-k8s/
│
├── k8s/                         # Arquivos de definição Kubernetes (YAML)
│   └── deployment.yaml
│   
│
├── src/                         # Código-fonte da aplicação
│   └── [aplicação Node.js]
│
├── Dockerfile                   # Criação da imagem container
├── .dockerignore                # Exclusões do contexto de build
├── README.md                    # Documentação detalhada
└── docs/                        # Prints, apresentações, evidências
    ├── apresentacao.pdf
    └── imagens/
        ├── dashboard-cluster.png
        ├── kubectl-get-all.png
        └── service-exposed.png
```
---

## ⚙️ Instalação do Kubectl no Windows

```bash
choco install kubernetes-cli
kubectl version --client
```
## Inicializando um cluster local com Minikube
```bash
minikube start --kubernetes-version=v1.29.0 --driver=docker --memory=4096 --cpus=2
```
Se der erro:
```bash
minikube stop
minikube delete --all --purge
```

## 🚀 Deploy da Aplicação
```bash
kubectl apply -f deployment.yaml
kubectl get all
```
-

## ☁️ Cluster na Digital Ocean
- Criado cluster com:
  2 vCPU | 2 GB RAM

- Obter crédito de $200: Link de Indicação

## 🔐 Configuração do Kubectl
1. Baixar o arquivo de configuração do cluster da Digital Ocean.

2. Copiar o conteúdo para:

  - Linux/macOS: ~/.kube/config

  - Windows: C:\Users\seu_usuario\.kube\config


## 🚀 Deploy da Aplicação
```bash
kubectl apply -f deployment.yaml
kubectl get all
```

## 🧱 Manifesto deployment.yaml
```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-news-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: kube-news-app
  template:
    metadata:
      labels:
        app: kube-news-app
    spec:
      containers:
      - name: kube-news
        image: <sua-imagem>
        ports:
        - containerPort: 8080
```


## 🌐 Criando o Service
Expondo via LoadBalancer
```yaml

apiVersion: v1
kind: Service
metadata:
  name: kube-news-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: kube-news-app
```

## 📈 Escalabilidade e Resiliência
- Escalabilidade
Aumentar número de réplicas:

```yaml
spec:
  replicas: 10
```

- Resiliência
Simular falha:

```bash
kubectl delete pod <nome-do-pod>
```
O pod será recriado automaticamente.

## 🧪 Rollback de Deploy
```bash
kubectl rollout history deployment kube-news-deployment
kubectl rollout undo deployment kube-news-deployment
```

## 🧹 Cleanup
```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

## 🧪 Labs Extras (Planejados)
- Deploy com imagem: fabricioverones/web-color

- Integração com banco de dados PostgreSQL externo

- Uso de PersistentVolumes (PV/PVC)

## 📊 Análise do Cluster
- Usar Ask Gordon para analisar a segurança e performance do cluster Kubernetes.

## 📚 Recursos Úteis
- kubectl api-resources

- Documentação Oficial Kubernetes

## ✍️ Autor
- https://www.linkedin.com/in/ronayrton-rocha-13a872a8/