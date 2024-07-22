# Testes de Integração Contínua com Jenkins

Jenkins é uma ferramenta de automação de código aberto que facilita a integração contínua e a entrega contínua. Ele permite automatizar a construção, teste e implantação de aplicações, garantindo que o código esteja sempre em um estado funcional.

## Importância dos Testes de Integração Contínua

### Benefícios

- **Automatização:** Automatiza o processo de construção, teste e implantação de aplicações.
- **Detecção Precoce de Problemas:** Identifica erros e problemas de integração assim que eles são introduzidos no código.
- **Confiança no Código:** Garante que o código esteja sempre em um estado funcional, pronto para ser integrado ou liberado.

## Instalação e Configuração do Jenkins

### Instalação do Jenkins

Para instalar o Jenkins, siga as instruções oficiais de instalação disponíveis no [site do Jenkins](https://www.jenkins.io/doc/book/installing/).

### Configuração do Jenkins

1. **Inicie o Jenkins:** Após a instalação, inicie o Jenkins e acesse o painel de administração.
2. **Instale os Plugins Necessários:** Instale plugins essenciais como "Git", "GitHub", "Pipeline" e "SSH Agent".
3. **Configure as Credenciais:** Adicione as credenciais necessárias para acessar o repositório de código e outros serviços externos.

## Configuração do Pipeline no Jenkins

### Criando um Pipeline

1. **Crie um Novo Item:** No painel do Jenkins, clique em "Novo Item" e selecione "Pipeline".
2. **Configure o Pipeline:** Adicione um nome para o pipeline e configure-o.

### Exemplo de Pipeline

Aqui está um exemplo de configuração de pipeline Jenkins usando um `Jenkinsfile`:

#### Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        MYSQL_ROOT_PASSWORD = 'root'
        MYSQL_DATABASE = 'testing'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/seu-usuario/seu-repositorio.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'composer install'
                sh 'cp .env.example .env'
                sh 'php artisan key:generate'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'php artisan migrate'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'vendor/bin/phpunit'
            }
        }
    }

    post {
        always {
            junit 'tests/reports/*.xml'
            cleanWs()
        }
    }
}
```

### Execução do Pipeline

1. **Adicione um Webhook no GitHub:** Configure um webhook no repositório GitHub para notificar o Jenkins sobre novas alterações no código.
2. **Execute o Pipeline:** No painel do Jenkins, clique em "Build Now" para iniciar a execução do pipeline.

## Práticas Recomendadas

### Automação Completa

Automatize todo o processo de construção, teste e implantação para garantir que o código esteja sempre em um estado funcional.

### Feedback Rápido

Configure o pipeline para fornecer feedback rápido sobre o estado do código, permitindo que os desenvolvedores corrijam problemas imediatamente.

### Monitoramento Contínuo

Monitore continuamente o pipeline para garantir que ele esteja funcionando corretamente e fornecendo feedback preciso.

## Exemplo Completo

### Jenkinsfile Completo

Crie um arquivo `Jenkinsfile` na raiz do seu repositório com o seguinte conteúdo:

```groovy
pipeline {
    agent any

    environment {
        MYSQL_ROOT_PASSWORD = 'root'
        MYSQL_DATABASE = 'testing'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/seu-usuario/seu-repositorio.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'composer install'
                sh 'cp .env.example .env'
                sh 'php artisan key:generate'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'php artisan migrate'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'vendor/bin/phpunit'
            }
        }
    }

    post {
        always {
            junit 'tests/reports/*.xml'
            cleanWs()
        }
    }
}
```

### Configuração do Webhook no GitHub

1. Vá para as configurações do repositório no GitHub.
2. Selecione "Webhooks" e clique em "Add webhook".
3. Adicione a URL do seu servidor Jenkins e selecione o tipo de evento "push".

### Execução do Pipeline no Jenkins

1. No painel do Jenkins, clique no seu pipeline.
2. Clique em "Build Now" para iniciar a execução do pipeline.
3. Monitore a execução e verifique os resultados dos testes.

## Resumo

Jenkins é uma poderosa ferramenta de automação que facilita a integração contínua e a entrega contínua de aplicações. Configurando pipelines de CI com Jenkins, você pode automatizar a construção, teste e implantação de código, garantindo a qualidade e a confiabilidade da sua aplicação Laravel.
