# Deploy Zero-Downtime

O deploy zero-downtime é uma prática que permite a implantação de novas versões de uma aplicação sem interrupção do serviço. Isso é essencial para garantir uma experiência contínua e sem falhas para os usuários.

## Técnicas de Zero-Downtime

### Deploy Atômico

1. **Deploy em Diretório Separado:**
   - O novo código é implantado em um diretório separado no servidor.
   - Após a conclusão do deploy, um link simbólico é atualizado para apontar para o novo diretório.

#### Exemplo de Deploy Atômico

```bash
# Define variáveis
RELEASE_DIR="/var/www/releases"
CURRENT_DIR="/var/www/current"
NEW_RELEASE_DIR="$RELEASE_DIR/$(date +%Y%m%d%H%M%S)"

# Cria o novo diretório de release
mkdir -p $NEW_RELEASE_DIR

# Copia os arquivos para o novo diretório de release
cp -R /path/to/new/code/* $NEW_RELEASE_DIR

# Atualiza o link simbólico
ln -sfn $NEW_RELEASE_DIR $CURRENT_DIR

# Reinicia o servidor web
systemctl restart nginx
```

### Blue-Green Deploy

1. **Definição de Ambientes Blue e Green:**
   - Mantenha dois ambientes idênticos, chamados Blue e Green.
   - Um ambiente está ativo (servindo tráfego), enquanto o outro é atualizado com o novo código.

2. **Alternância de Ambientes:**
   - Após a conclusão do deploy no ambiente inativo, o tráfego é redirecionado para ele, tornando-o o novo ambiente ativo.

#### Exemplo de Alternância de Ambientes

```bash
# Define variáveis
ACTIVE_ENV="blue"
INACTIVE_ENV="green"

# Atualiza o ambiente inativo
deploy_to_environment $INACTIVE_ENV

# Redireciona o tráfego para o ambiente inativo, tornando-o ativo
switch_traffic $INACTIVE_ENV

# Atualiza as variáveis de ambiente
TEMP=$ACTIVE_ENV
ACTIVE_ENV=$INACTIVE_ENV
INACTIVE_ENV=$TEMP
```

### Canary Deploy

1. **Implementação Gradual:**
   - O novo código é implantado em uma pequena porcentagem de servidores ou usuários.
   - Monitore o comportamento e a performance da aplicação.

2. **Expansão Gradual:**
   - Se não forem detectados problemas, aumente gradualmente a porcentagem de servidores ou usuários que recebem a nova versão.

#### Exemplo de Canary Deploy com Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-app
spec:
  replicas: 10
  selector:
    matchLabels:
      app: laravel
  template:
    metadata:
      labels:
        app: laravel
        version: canary
    spec:
      containers:
      - name: laravel
        image: laravel-app:latest
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-app-stable
spec:
  replicas: 90
  selector:
    matchLabels:
      app: laravel
  template:
    metadata:
      labels:
        app: laravel
        version: stable
    spec:
      containers:
      - name: laravel
        image: laravel-app:stable
        ports:
        - containerPort: 80
```

## Ferramentas para Deploy Zero-Downtime

### Laravel Envoyer

Laravel Envoyer facilita o deploy zero-downtime com funcionalidades como deploy atômico, gerenciamento de releases e monitoramento de saúde.

### Kubernetes

Kubernetes é uma plataforma poderosa para orquestração de contêineres, suportando estratégias avançadas de deploy como rolling updates, blue-green deploys e canary deploys.

#### Exemplo de Rolling Update com Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: laravel-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: laravel
  template:
    metadata:
      labels:
        app: laravel
    spec:
      containers:
      - name: laravel
        image: laravel-app:latest
        ports:
        - containerPort: 80
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

### Docker Swarm

Docker Swarm facilita a implantação de serviços em clusters, suportando deploys rolling updates e outros padrões de deploy.

#### Exemplo de Rolling Update com Docker Swarm

```bash
docker service update --update-parallelism 1 --update-delay 10s my-service
```

## Práticas Recomendadas

### Testes Automatizados

1. **Execução de Testes Antes do Deploy:**
   - Execute uma suíte completa de testes automatizados para garantir que o código está estável antes do deploy.

### Monitoramento Pós-Deploy

1. **Monitoramento Contínuo:**
   - Implemente monitoramento contínuo para detectar problemas rapidamente após o deploy.
   - Utilize ferramentas como Grafana, Prometheus e ELK Stack para monitoramento e análise de logs.

### Plano de Rollback

1. **Preparação de Rollback:**
   - Tenha um plano de rollback pronto para ser executado rapidamente em caso de falhas no deploy.
   - Automatize o processo de rollback sempre que possível.

## Resumo

O deploy zero-downtime é essencial para garantir a continuidade do serviço e uma boa experiência do usuário. Utilizando técnicas como deploy atômico, blue-green deploy e canary deploy, e ferramentas como Laravel Envoyer, Kubernetes e Docker Swarm, é possível implantar novas versões da aplicação sem interrupções. Seguir práticas recomendadas, como testes automatizados e monitoramento contínuo, garante um processo de deploy seguro e eficiente.
