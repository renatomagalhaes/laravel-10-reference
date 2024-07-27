# Deploy para Serviços Cloud

Implantar aplicações em serviços de nuvem como AWS, Google Cloud Platform e Azure oferece escalabilidade, alta disponibilidade e diversas opções de serviços gerenciados. Este guia cobre os passos básicos para deploy em cada uma dessas plataformas.

## Deploy na AWS

### Configuração do AWS CLI

1. **Instalação do AWS CLI:**
   ```bash
   curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
   sudo installer -pkg AWSCLIV2.pkg -target /
   ```

2. **Configuração do AWS CLI:**
   ```bash
   aws configure
   ```
   - Insira suas credenciais AWS (Access Key ID e Secret Access Key).

### Deploy com Elastic Beanstalk

1. **Inicialização do Elastic Beanstalk:**
   ```bash
   eb init -p php laravel-app --region us-west-2
   ```

2. **Criação do Ambiente:**
   ```bash
   eb create laravel-env
   ```

3. **Deploy da Aplicação:**
   ```bash
   eb deploy
   ```

### Configuração de Banco de Dados

1. **Criação de um RDS MySQL:**
   - No console da AWS, navegue até RDS e crie uma nova instância MySQL.
   - Configure as credenciais e o endpoint no arquivo `.env` do Laravel.

```dotenv
DB_CONNECTION=mysql
DB_HOST=<rds-endpoint>
DB_PORT=3306
DB_DATABASE=<database-name>
DB_USERNAME=<username>
DB_PASSWORD=<password>
```

## Deploy no Google Cloud Platform (GCP)

### Configuração do gcloud CLI

1. **Instalação do gcloud CLI:**
   ```bash
   curl https://sdk.cloud.google.com | bash
   exec -l $SHELL
   gcloud init
   ```

### Deploy com App Engine

1. **Configuração do App Engine:**
   ```bash
   gcloud app create --project=<project-id>
   ```

2. **Criação do arquivo app.yaml:**
   Crie um arquivo `app.yaml` na raiz do seu projeto:

```yaml
runtime: php
env: flex

runtime_config:
  document_root: public

env_variables:
  APP_ENV: production
  APP_KEY: your-app-key
  DB_HOST: /cloudsql/your-instance-connection-name
  DB_DATABASE: your-database-name
  DB_USERNAME: your-database-username
  DB_PASSWORD: your-database-password

beta_settings:
  cloud_sql_instances: your-instance-connection-name
```

3. **Deploy da Aplicação:**
   ```bash
   gcloud app deploy
   ```

### Configuração de Banco de Dados

1. **Criação de uma Instância Cloud SQL:**
   - No console do GCP, navegue até Cloud SQL e crie uma nova instância MySQL.
   - Configure as credenciais e o endpoint no arquivo `.env` do Laravel.

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=<database-name>
DB_USERNAME=<username>
DB_PASSWORD=<password>
```

## Deploy no Azure

### Configuração do Azure CLI

1. **Instalação do Azure CLI:**
   ```bash
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
   ```

2. **Login no Azure CLI:**
   ```bash
   az login
   ```

### Deploy com Azure App Service

1. **Criação do Grupo de Recursos:**
   ```bash
   az group create --name myResourceGroup --location "East US"
   ```

2. **Criação do Plano de Serviço:**
   ```bash
   az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
   ```

3. **Criação do Serviço Web:**
   ```bash
   az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name myLaravelApp --runtime "PHP|7.4"
   ```

4. **Configuração de Deploy:**
   - Configure o deploy contínuo através do GitHub ou do repositório local.

```bash
az webapp deployment source config --name myLaravelApp --resource-group myResourceGroup --repo-url <repository-url> --branch main --manual-integration
```

### Configuração de Banco de Dados

1. **Criação de um Banco de Dados MySQL:**
   - No portal do Azure, navegue até Azure Database for MySQL e crie uma nova instância.
   - Configure as credenciais e o endpoint no arquivo `.env` do Laravel.

```dotenv
DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_PORT=3306
DB_DATABASE=<database-name>
DB_USERNAME=<username>@<mysql-server-name>
DB_PASSWORD=<password>
```

## Resumo

Implantar aplicações em serviços de nuvem como AWS, GCP e Azure oferece várias vantagens, incluindo escalabilidade e alta disponibilidade. Seguir as melhores práticas e configurar corretamente cada serviço garante um deploy eficiente e seguro para a sua aplicação Laravel.
