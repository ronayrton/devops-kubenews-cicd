#  Automatizando o Deploy da KubeNews com GitHub Actions

Na continuação da Maratona DevOps + IA com Fabricio Veronez, o Dia 3 trouxe um dos temas mais importantes no mundo DevOps: Integração Contínua e Entrega Contínua (CI/CD).

Neste artigo, mostro como configurei um pipeline completo com GitHub Actions para a aplicação KubeNews, realizando build da imagem, push para o Docker Hub e deploy automático no Kubernetes.


---

##  Objetivos
- Criar workflow de CI/CD com GitHub Actions.
- Automatizar o build da imagem Docker.
- Publicar imagem no Docker Hub.
- Aplicar o manifesto Kubernetes automaticamente após o push.
- Garantir entregas rápidas, seguras e sem fricção.

---

##  Arquitetura CI/CD



---


## Estrutura do Projeto `devops-kubenews-cicd`

```plaintext
devops-kubenews-cicd/
│
├── .github/
│   └── workflows/
│       └── cicd.yaml              # Workflow GitHub Actions
│
├── k8s/                           # Manifests Kubernetes
│
├── Dockerfile                     # Build da imagem
├── README.md                      # Documentação CI/CD
```
---

##  Configurando o GitHub Actions
Crie um arquivo em:
.github/workflows/cicd.yaml

```yaml
name: CI/CD KubeNews

on:
  push:
    branches: [ "main" ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout do código
      uses: actions/checkout@v3

    - name: Login no Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}

    - name: Build da imagem Docker
      run: docker build -t ${{ secrets.DOCKERHUB_USERNAME }}/kubenews:latest .

    - name: Push da imagem
      run: docker push ${{ secrets.DOCKERHUB_USERNAME }}/kubenews:latest

    - name: Configurar kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'latest'

    - name: Aplicar manifesto no cluster
      env:
        KUBECONFIG: ${{ secrets.KUBECONFIG }}
      run: |
        echo "$KUBECONFIG" > kubeconfig
        export KUBECONFIG=$PWD/kubeconfig
        kubectl apply -f k8s/deployment.yaml
        kubectl apply -f k8s/service.yaml
```
-

## Secrets necessários no GitHub
Configure os seguintes segredos (Secrets) no repositório GitHub:

| Nome                 | Descrição                                       |
| -------------------- | ----------------------------------------------- |
| `DOCKERHUB_USERNAME` | Seu nome de usuário no Docker Hub               |
| `DOCKERHUB_TOKEN`    | Token de acesso gerado no Docker Hub            |
| `KUBECONFIG`         | Conteúdo do arquivo `~/.kube/config` do cluster |


## Dockerfile da aplicação
Certifique-se de ter um Dockerfile na raiz do projeto:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 8080
CMD ["npm", "start"]
```

## Resultado Final
Ao realizar um git push na branch main, o GitHub Actions irá:

1. Fazer build da imagem Docker.
2. Publicar no Docker Hub.
3. Aplicar os arquivos YAML no cluster Kubernetes.

Tudo isso de forma automática, garantindo uma pipeline robusta e eficiente.


## Dicas e Testes
- Faça um teste deletando o pod e veja o Kubernetes recriando com a nova imagem.
- Use kubectl describe para ver o histórico dos eventos.
- Combine com kubectl rollout restart deployment kube-news-deployment para atualizar sempre que desejar.

##  Autor
- https://www.linkedin.com/in/ronayrton-rocha-13a872a8/
