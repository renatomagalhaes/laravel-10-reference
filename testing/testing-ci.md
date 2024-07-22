# Testes de Integração Contínua (CI)

A Integração Contínua (CI) é uma prática de desenvolvimento que envolve a execução automática de testes sempre que há uma alteração no código. Isso garante que o código esteja sempre em um estado estável e pronto para ser integrado.

## Importância dos Testes de Integração Contínua

### Benefícios

- **Detecção Precoce de Problemas:** Identifica erros e problemas de integração assim que eles são introduzidos no código.
- **Automatização:** Executa testes automaticamente, economizando tempo e esforço dos desenvolvedores.
- **Confiança no Código:** Garante que o código esteja sempre em um estado funcional, pronto para ser integrado ou liberado.

## Ferramentas para Integração Contínua

### GitHub Actions

GitHub Actions é uma plataforma de CI/CD que permite automatizar fluxos de trabalho diretamente no GitHub.

### GitLab CI/CD

GitLab CI/CD é uma ferramenta de CI/CD integrada ao GitLab que permite automatizar a construção, teste e implantação de aplicações.

## Configuração Básica para CI

### Configuração do GitHub Actions

Crie um arquivo de workflow em `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:

    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ROOT_PASSWORD: root
          MYSQL_DATABASE: testing
        ports:
          - 3306:3306
        options: >-
          --health-cmd="mysqladmin ping --silent"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=3

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: 8.0

    - name: Install dependencies
      run: composer install --prefer-dist --no-progress --no-suggest

    - name: Copy .env file
      run: cp .env.example .env

    - name: Generate app key
      run: php artisan key:generate

    - name: Run migrations
      run: php artisan migrate

    - name: Run tests
      run: php artisan test
```

### Configuração do GitLab CI/CD

Crie um arquivo de pipeline em `.gitlab-ci.yml`:

```yaml
stages:
  - test

variables:
  MYSQL_ROOT_PASSWORD: root
  MYSQL_DATABASE: testing

services:
  - mysql:5.7

test:
  stage: test
  image: php:8.0
  before_script:
    - apt-get update && apt-get install -y unzip
    - curl -sS https://getcomposer.org/installer | php
    - php composer.phar install
    - cp .env.example .env
    - php artisan key:generate
    - php artisan migrate
  script:
    - php artisan test
```

## Criando Testes de Integração Contínua

### Passo 1: Configurar o Ambiente

Certifique-se de que as variáveis de ambiente e as dependências estejam configuradas corretamente nos arquivos de configuração.

### Passo 2: Executar Testes

Configure os jobs de CI para executar os testes sempre que houver alterações no código.

### Passo 3: Analisar Resultados

Revise os resultados dos testes e corrija quaisquer problemas identificados pelo pipeline de CI.

## Práticas Recomendadas

### Automação Completa

Automatize todo o processo de construção, teste e implantação para garantir que o código esteja sempre em um estado funcional.

### Feedback Rápido

Configure o pipeline de CI para fornecer feedback rápido sobre o estado do código, permitindo que os desenvolvedores corrijam problemas imediatamente.

### Monitoramento Contínuo

Monitore continuamente o pipeline de CI para garantir que ele esteja funcionando corretamente e fornecendo feedback preciso.

## Exemplo Completo

### Configuração do GitHub Actions

Crie o arquivo `.github/workflows/ci.yml` com o seguinte conteúdo:

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:

    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ROOT_PASSWORD: root
          MYSQL_DATABASE: testing
        ports:
          - 3306:3306
        options: >-
          --health-cmd="mysqladmin ping --silent"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=3

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: 8.0

    - name: Install dependencies
      run: composer install --prefer-dist --no-progress --no-suggest

    - name: Copy .env file
      run: cp .env.example .env

    - name: Generate app key
      run: php artisan key:generate

    - name: Run migrations
      run: php artisan migrate

    - name: Run tests
      run: php artisan test
```

### Configuração do GitLab CI/CD

Crie o arquivo `.gitlab-ci.yml` com o seguinte conteúdo:

```yaml
stages:
  - test

variables:
  MYSQL_ROOT_PASSWORD: root
  MYSQL_DATABASE: testing

services:
  - mysql:5.7

test:
  stage: test
  image: php:8.0
  before_script:
    - apt-get update && apt-get install -y unzip
    - curl -sS https://getcomposer.org/installer | php
    - php composer.phar install
    - cp .env.example .env
    - php artisan key:generate
    - php artisan migrate
  script:
    - php artisan test
```

## Resumo

Os testes de integração contínua são essenciais para garantir que o código esteja sempre em um estado estável e pronto para ser integrado. Com ferramentas como GitHub Actions e GitLab CI/CD, você pode automatizar a execução de testes e obter feedback rápido sobre o estado do código, garantindo a confiabilidade e a qualidade da sua aplicação Laravel.
