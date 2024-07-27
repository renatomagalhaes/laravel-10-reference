# Deploy com Terraform

Terraform é uma ferramenta de infraestrutura como código (IaC) que permite definir e provisionar infraestrutura de TI utilizando uma linguagem de configuração declarativa. Utilizar Terraform para deploy permite gerenciar infraestrutura de maneira eficiente, segura e repetível.

## Configuração de Infraestrutura como Código

### Instalação do Terraform

1. **Instalação do Terraform:**
   - Baixe e instale o Terraform na sua máquina local.

```bash
curl -LO https://releases.hashicorp.com/terraform/0.15.0/terraform_0.15.0_linux_amd64.zip
unzip terraform_0.15.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

### Criação de Arquivos de Configuração

1. **Definição do Provedor:**
   - Configure o provedor de nuvem que você deseja usar (por exemplo, AWS, GCP, Azure).

#### Exemplo de Configuração do Provedor AWS

```hcl
provider "aws" {
  region = "us-west-2"
}
```

2. **Definição de Recursos:**
   - Defina os recursos que você deseja provisionar, como instâncias EC2, bancos de dados RDS, balanceadores de carga, etc.

#### Exemplo de Definição de Recursos

```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "AppServer"
  }
}

resource "aws_db_instance" "laravel_db" {
  allocated_storage    = 20
  engine               = "mysql"
  instance_class       = "db.t2.micro"
  name                 = "laravel"
  username             = "root"
  password             = "rootpassword"
  parameter_group_name = "default.mysql5.7"
}
```

### Inicialização e Aplicação do Terraform

1. **Inicialização do Terraform:**
   - Inicialize o Terraform para preparar o diretório de trabalho.

```bash
terraform init
```

2. **Plano de Execução:**
   - Crie um plano de execução para revisar as mudanças que serão aplicadas.

```bash
terraform plan
```

3. **Aplicação do Plano:**
   - Aplique o plano para provisionar a infraestrutura.

```bash
terraform apply
```

### Automação de Deploy com Terraform

1. **Script de Deploy:**
   - Crie um script de deploy para automatizar o provisionamento e a implantação da aplicação.

#### Exemplo de Script de Deploy

```bash
#!/bin/bash

# Inicializa o Terraform
terraform init

# Cria um plano de execução
terraform plan -out=tfplan

# Aplica o plano de execução
terraform apply "tfplan"

# Conecta-se ao servidor e implanta a aplicação
ssh -i my-key.pem ec2-user@$(terraform output -raw app_server_ip) << EOF
  cd /var/www/html
  git pull origin main
  composer install --no-dev --optimize-autoloader
  php artisan migrate --force
EOF
```

### Gestão de Estado

1. **Armazenamento Remoto do Estado:**
   - Armazene o estado do Terraform em um backend remoto para facilitar a colaboração e garantir a consistência.

#### Exemplo de Configuração de Backend Remoto

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "path/to/my/key"
    region = "us-west-2"
  }
}
```

### Versionamento de Infraestrutura

1. **Utilização de Módulos:**
   - Utilize módulos para organizar e reutilizar a configuração de infraestrutura.

#### Exemplo de Definição de Módulo

```hcl
module "app_server" {
  source = "./modules/app_server"

  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

module "laravel_db" {
  source = "./modules/laravel_db"

  allocated_storage = 20
  engine            = "mysql"
  instance_class    = "db.t2.micro"
  name              = "laravel"
  username          = "root"
  password          = "rootpassword"
}
```

## Práticas Recomendadas

### Segurança

1. **Gestão de Credenciais:**
   - Utilize ferramentas como AWS Secrets Manager ou HashiCorp Vault para gerenciar credenciais de forma segura.

### Monitoramento

1. **Monitoramento de Infraestrutura:**
   - Implemente monitoramento e alertas para acompanhar a saúde e o desempenho da infraestrutura provisionada.

### Backups

1. **Configuração de Backups:**
   - Configure backups regulares para garantir a recuperação de dados em caso de falhas.

## Resumo

Utilizar Terraform para deploy permite gerenciar a infraestrutura de maneira eficiente, segura e repetível. Com arquivos de configuração bem definidos, você pode provisionar e gerenciar recursos na nuvem, automatizar o deploy da aplicação Laravel e garantir a consistência do ambiente.
