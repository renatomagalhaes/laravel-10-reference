# Deploy

O processo de deploy é crucial para garantir que sua aplicação Laravel esteja disponível de forma eficiente e segura em um ambiente de produção. Existem várias abordagens e ferramentas que podem ser utilizadas para facilitar e automatizar o deploy.

## Preparação para o Deploy

### Passo 1: Configurar o Ambiente

Antes de realizar o deploy, certifique-se de que o ambiente de produção está corretamente configurado. Isso inclui a instalação de todos os serviços necessários, como PHP, MySQL, e servidores web como Nginx ou Apache.

#### Exemplo de Configuração de Ambiente

```bash
sudo apt-get update
sudo apt-get install php php-fpm php-mysql nginx mysql-server
```

### Passo 2: Configurar o Arquivo `.env`

No ambiente de produção, você precisa configurar o arquivo `.env` com as credenciais corretas de banco de dados e outras configurações específicas.

```env
APP_NAME=Laravel
APP_ENV=production
APP_KEY=base64:...
APP_DEBUG=false
APP_URL=https://yourdomain.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

## Deploy Manual

### Passo 1: Transferir Arquivos

Utilize ferramentas de transferência de arquivos como SCP ou FTP para copiar os arquivos da aplicação para o servidor de produção.

#### Exemplo de Transferência com SCP

```bash
scp -r /local/path/to/your/project username@yourserver:/remote/path
```

### Passo 2: Instalar Dependências

No servidor de produção, navegue até o diretório da aplicação e instale as dependências com Composer.

```bash
cd /remote/path/to/your/project
composer install --optimize-autoloader --no-dev
```

### Passo 3: Executar Migrações

Execute as migrações de banco de dados para garantir que a estrutura do banco esteja atualizada.

```bash
php artisan migrate --force
```

### Passo 4: Compilar Assets

Compile os assets front-end utilizando Laravel Mix.

```bash
npm install
npm run production
```

### Passo 5: Configurar Permissões

Garanta que as permissões corretas estejam definidas para os diretórios `storage` e `bootstrap/cache`.

```bash
chown -R www-data:www-data /remote/path/to/your/project/storage
chown -R www-data:www-data /remote/path/to/your/project/bootstrap/cache
```

## Deploy Automatizado

### Passo 1: Utilizar Ferramentas de CI/CD

Ferramentas de Integração Contínua/Entrega Contínua (CI/CD) como GitLab CI, GitHub Actions, ou Jenkins podem automatizar o processo de deploy.

#### Exemplo de Configuração de GitLab CI

Crie um arquivo `.gitlab-ci.yml` na raiz do seu projeto.

```yaml
stages:
  - deploy

deploy_production:
  stage: deploy
  script:
    - apt-get update -y
    - apt-get install -y rsync ssh
    - rsync -avz --delete --exclude '.env' . username@yourserver:/remote/path
    - ssh username@yourserver "cd /remote/path && composer install --optimize-autoloader --no-dev"
    - ssh username@yourserver "cd /remote/path && php artisan migrate --force"
    - ssh username@yourserver "cd /remote/path && npm install && npm run production"
  only:
    - master
```

### Passo 2: Configurar Hooks de Deploy

Ferramentas como Laravel Envoy permitem definir scripts de deploy com passos específicos.

#### Exemplo de Script com Laravel Envoy

Crie um arquivo `Envoy.blade.php`.

```php
@servers(['web' => 'username@yourserver'])

@task('deploy')
    cd /remote/path/to/your/project
    git pull origin master
    composer install --optimize-autoloader --no-dev
    php artisan migrate --force
    npm install
    npm run production
    php artisan config:cache
    php artisan route:cache
    php artisan view:cache
@endtask
```

Execute o deploy com o comando:

```bash
envoy run deploy
```

## Monitoramento e Manutenção

### Passo 1: Configurar Logging e Monitoramento

Ferramentas como Sentry, New Relic, ou ELK Stack podem ser integradas para monitorar a aplicação e registrar logs de erros e performance.

### Passo 2: Backup e Recuperação

Implemente uma estratégia de backup regular para os dados e arquivos da aplicação. Ferramentas como Laravel Backup podem automatizar esse processo.

#### Exemplo de Backup com Laravel Backup

Instale o pacote:

```bash
composer require spatie/laravel-backup
```

Configure o backup no arquivo `config/backup.php`.

```php
return [
    'backup' => [
        'name' => env('APP_NAME', 'laravel-backup'),
        // outras configurações...
    ],
    // outras configurações...
];
```

Execute o backup com o comando:

```bash
php artisan backup:run
```

## Resumo

O processo de deploy no Laravel pode ser realizado manualmente ou automatizado utilizando ferramentas de CI/CD. Configurações adequadas, execução de migrações, compilação de assets, e configuração de permissões são etapas essenciais. Monitoramento contínuo e estratégias de backup garantem a estabilidade e a segurança da aplicação em produção.
