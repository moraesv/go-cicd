# CI/CD com GitHub Actions - Go

Este repositório contém um projeto de estudos focado em CI/CD utilizando GitHub Actions para um aplicativo desenvolvido em Go.

## Estrutura do Projeto

```
├── .github/workflows/
│   ├── go.yml  # Workflow principal
│   ├── Docker.yml  # Workflow para criação de imagem Docker
│   ├── Deploy.yml  # (Comentado) Workflow para deploy
│   ├── K8s.yml  # Workflow para Kubernetes
│
├── main.go  # Código-fonte principal
├── main_test.go  # Testes unitários
├── docker-compose.yml  # Configuração do ambiente com banco de dados
├── infra-k8s/
│   ├── go.yaml  # Configuração do Kubernetes
```

## Workflow Principal (`.github/workflows/go.yml`)

O workflow é acionado nos eventos `push` e `pull_request` para qualquer branch.

### Jobs do Workflow

#### 1. **Test**
Executa testes unitários em diferentes versões do Go.

- Configura o ambiente com banco de dados usando Docker
- Executa os testes com `go test`

```yaml
runs-on: ${{ matrix.os }}
strategy:
  matrix:
    go_version: ["1.18", "1.17", ">=1.18"]
    os: ["ubuntu-latest"]
```

#### 2. **Build**
Compila o aplicativo e armazena o binário como artefato no GitHub Actions.

```yaml
- name: Build
  run: go build -v main.go
```

#### 3. **Docker**
Cria uma imagem Docker utilizando um workflow separado (`Docker.yml`).

```yaml
uses: ./.github/workflows/Docker.yml
secrets: inherit
```

#### 4. **Deploy**
O deploy final é realizado via Kubernetes (`K8s.yml`).

```yaml
uses: ./.github/workflows/K8s.yml
secrets: inherit
```

## Outros Workflows

- **Entrega Contínua (`.github/workflows/Deploy.yml`)**: Faz deploy automatizado do binário em um servidor remoto utilizando SSH.
- **Docker (`.github/workflows/Docker.yml`)**: Responsável por criar e publicar a imagem Docker no Docker Hub.
- **Kubernetes (`.github/workflows/K8s.yml`)**: Configura e implanta o aplicativo no Google Kubernetes Engine (GKE).

## Como Rodar Localmente

1. Clone o repositório:
   ```sh
   git clone https://github.com/moraesv/go-cicd.git
   ```
2. Suba os serviços com Docker Compose:
   ```sh
   docker-compose up -d --build
   ```
3. Execute os testes:
   ```sh
   go test -v main_test.go
   ```
