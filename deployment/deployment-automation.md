# Automação de Deploy

Automatizar o processo de deploy é essencial para garantir que as aplicações sejam implantadas de maneira consistente, eficiente e sem erros. A automação reduz o risco de falhas humanas e permite um processo de deploy repetível e confiável.

## Ferramentas de Automação

### Ansible

Ansible é uma ferramenta de automação de TI que facilita a configuração de sistemas, a implantação de software e a execução de tarefas de TI. Ele usa uma linguagem simples (YAML) para descrever tarefas de automação em arquivos chamados playbooks.

#### Exemplo de Playbook Ansible

```yaml
- hosts: webservers
  become: yes
  tasks:
    - name: Atualizar o sistema
      apt:
        update_cache: yes

    - name: Instalar dependências
      apt:
        name:
          - nginx
          - php-fpm
          - git
        state: present

    - name: Clonar repositório do Git
      git:
        repo: 'https://github.com/seu-usuario/seu-repositorio.git'
        dest: /var/www/html
        version: main

    - name: Configurar Nginx
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/default

    - name: Reiniciar Nginx
      service:
        name: nginx
        state: restarted
```

### Terraform

Terraform é uma ferramenta de infraestrutura como código (IaC) que permite definir e provisionar infraestrutura de TI usando uma linguagem de configuração declarativa. Ele é usado para automatizar a criação, modificação e destruição de recursos em vários provedores de nuvem.

#### Exemplo de Configuração Terraform

```hcl
provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "web-server"
  }
}

resource "aws_security_group" "web_sg" {
  name_prefix = "web-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

### Chef

Chef é uma ferramenta de automação de TI que facilita a configuração e gerenciamento de infraestrutura de TI. Ele usa uma linguagem de definição de configuração baseada em Ruby para definir receitas e cookbooks que descrevem como configurar e gerenciar sistemas.

#### Exemplo de Receita Chef

```ruby
package 'nginx' do
  action :install
end

service 'nginx' do
  action [:enable, :start]
end

git '/var/www/html' do
  repository 'https://github.com/seu-usuario/seu-repositorio.git'
  revision 'main'
  action :sync
end

template '/etc/nginx/sites-available/default' do
  source 'nginx.conf.erb'
  notifies :reload, 'service[nginx]'
end
```

## CI/CD para Deploys

### Integração Contínua (CI)

A Integração Contínua (CI) é uma prática de desenvolvimento que envolve a integração frequente de código em um repositório compartilhado, seguido de execução automática de testes para detectar problemas o mais cedo possível.

### Entrega Contínua (CD)

A Entrega Contínua (CD) é uma prática de desenvolvimento que estende a CI ao automatizar a implantação de código em ambientes de produção. Com a CD, cada alteração de código é testada e implantada automaticamente, permitindo lançamentos frequentes e confiáveis.

### Exemplo de Pipeline CI/CD com GitLab

Crie um arquivo `.gitlab-ci.yml` na raiz do seu projeto:

```yaml
stages:
  - build
  - test
  - deploy

variables:
  MYSQL_ROOT_PASSWORD: root
  MYSQL_DATABASE: testing

services:
  - mysql:5.7

build:
  stage: build
  script:
    - composer install

test:
  stage: test
  script:
    - vendor/bin/phpunit

deploy:
  stage: deploy
  script:
    - apt-get update && apt-get install -y ansible
    - ansible-playbook -i inventory/production deploy.yml
  only:
    - main
```

### Exemplo de Pipeline CI/CD com GitHub Actions

Crie um arquivo `deploy.yml` em `.github/workflows`:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.0
      - name: Install dependencies
        run: composer install

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.0
      - name: Install dependencies
        run: composer install
      - name: Run tests
        run: vendor/bin/phpunit

  deploy:
    runs-on: ubuntu-latest
    needs: [build, test]
    steps:
      - uses: actions/checkout@v2
      - name: Install Ansible
        run: sudo apt-get update && sudo apt-get install -y ansible
      - name: Run Ansible Playbook
        run: ansible-playbook -i inventory/production deploy.yml
```

## Resumo

Automatizar o processo de deploy é essencial para garantir consistência, eficiência e redução de erros humanos. Ferramentas como Ansible, Terraform, Chef e pipelines CI/CD (como GitLab CI/CD e GitHub Actions) facilitam a automação do deploy, tornando o processo repetível e confiável.
