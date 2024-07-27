# Deploy com Kubernetes

Kubernetes é uma plataforma open-source para orquestração de contêineres, que facilita o gerenciamento de aplicações em contêineres em ambientes de cluster. Utilizar Kubernetes para deploy garante escalabilidade, alta disponibilidade e automação de operações.

## Configuração de Kubernetes para Laravel

### Instalação do Kubernetes

1. **Instalação do Minikube (Ambiente de Desenvolvimento):**
   - Minikube é uma ferramenta que facilita a execução de um cluster Kubernetes local.

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start
```

2. **Instalação do kubectl:**
   - `kubectl` é a ferramenta de linha de comando para interagir com o cluster Kubernetes.

```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

### Configuração de um Cluster Kubernetes

1. **Configuração de um Cluster no Google Kubernetes Engine (GKE):**
   - GKE facilita a criação e gerenciamento de clusters Kubernetes na Google Cloud.

```bash
gcloud container clusters create my-cluster --num-nodes=3 --zone us-central1-a
gcloud container clusters get-credentials my-cluster --zone us-central1-a
```

## Deploy usando Helm Charts

### Instalação do Helm

1. **Instalação do Helm:**
   - Helm é um gerenciador de pacotes para Kubernetes que facilita a instalação e atualização de aplicações.

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### Criação de um Chart Helm para Laravel

1. **Inicialização de um Novo Chart:**
   - Crie um novo Chart Helm para a aplicação Laravel.

```bash
helm create laravel-app
```

2. **Configuração do Chart:**
   - Edite os arquivos `values.yaml` e os templates em `templates/` para configurar a aplicação Laravel.

#### Exemplo de Configuração do values.yaml

```yaml
replicaCount: 3

image:
  repository: laravel-app
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: LoadBalancer
  port: 80

ingress:
  enabled: true
  annotations: {}
  hosts:
    - host: laravel.local
      paths: ["/"]
  tls: []

resources: {}
nodeSelector: {}
tolerations: []
affinity: {}
```

3. **Deploy da Aplicação:**
   - Use o Helm para instalar o Chart e implantar a aplicação no cluster Kubernetes.

```bash
helm install laravel-app ./laravel-app
```

### Configuração de Banco de Dados

1. **Deploy de um Banco de Dados MySQL:**
   - Utilize um Chart Helm para instalar um banco de dados MySQL no cluster.

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-mysql bitnami/mysql
```

2. **Configuração da Conexão com o Banco de Dados:**
   - Configure a conexão com o banco de dados no arquivo `.env` da aplicação Laravel.

```dotenv
DB_CONNECTION=mysql
DB_HOST=my-mysql
DB_PORT=3306
DB_DATABASE=<database-name>
DB_USERNAME=root
DB_PASSWORD=<password>
```

## Monitoramento e Escalabilidade

### Monitoramento com Prometheus e Grafana

1. **Instalação do Prometheus:**
   - Utilize um Chart Helm para instalar o Prometheus.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
```

2. **Instalação do Grafana:**
   - Utilize um Chart Helm para instalar o Grafana.

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm install grafana grafana/grafana
```

### Escalabilidade Horizontal

1. **Configuração de Auto Scaling:**
   - Configure a escalabilidade automática com Horizontal Pod Autoscaler (HPA).

#### Exemplo de Configuração de HPA

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: laravel-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: laravel-app
  minReplicas: 3
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```

```bash
kubectl apply -f hpa.yaml
```

## Resumo

Utilizar Kubernetes para deploy garante escalabilidade, alta disponibilidade e automação das operações. Com ferramentas como Helm, Prometheus e Grafana, você pode gerenciar, monitorar e escalar sua aplicação Laravel de maneira eficiente e confiável.
