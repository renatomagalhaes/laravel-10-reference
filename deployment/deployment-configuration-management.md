# Gestão de Configurações

A gestão de configurações é essencial para garantir que a aplicação funcione corretamente em diferentes ambientes, como desenvolvimento, teste e produção. Utilizar práticas de gestão de configurações ajuda a manter a consistência e a segurança dos dados sensíveis.

## Variáveis de Ambiente

### Uso de Variáveis de Ambiente

1. **Configuração no Arquivo .env:**
   - Utilize o arquivo `.env` para armazenar variáveis de ambiente, que podem ser diferentes em cada ambiente.

#### Exemplo de Arquivo .env

```dotenv
APP_NAME=Laravel
APP_ENV=production
APP_KEY=base64:...

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=password
```

2. **Acesso às Variáveis de Ambiente no Código:**
   - Utilize a função `env()` para acessar variáveis de ambiente no código.

#### Exemplo de Uso de Variáveis de Ambiente

```php
$databaseHost = env('DB_HOST', '127.0.0.1');
```

### Segurança das Variáveis de Ambiente

1. **Exclusão do .env do Controle de Versão:**
   - Adicione o arquivo `.env` ao `.gitignore` para evitar que ele seja incluído no controle de versão.

```gitignore
.env
```

## Gerenciamento de Configurações com Config Cache

### Cache de Configurações

1. **Comando de Cache de Configurações:**
   - Utilize o comando de cache de configurações para melhorar a performance da aplicação.

```bash
php artisan config:cache
```

### Limpeza do Cache de Configurações

1. **Comando de Limpeza de Cache:**
   - Utilize o comando de limpeza de cache quando alterar as configurações.

```bash
php artisan config:clear
```

## Gestão de Configurações Sensíveis

### Uso de Secret Managers

1. **AWS Secrets Manager:**
   - Utilize o AWS Secrets Manager para armazenar e gerenciar segredos, como credenciais de banco de dados e chaves API.

#### Exemplo de Uso do AWS Secrets Manager

```php
use Aws\SecretsManager\SecretsManagerClient;

$client = new SecretsManagerClient([
    'version' => 'latest',
    'region' => 'us-west-2',
]);

$result = $client->getSecretValue([
    'SecretId' => 'my-secret-id',
]);

$secret = $result['SecretString'];
```

2. **HashiCorp Vault:**
   - Utilize o HashiCorp Vault para gerenciar segredos e proteger dados sensíveis.

#### Exemplo de Uso do HashiCorp Vault

```bash
vault kv put secret/myapp db_username="root" db_password="password"
vault kv get -field=db_password secret/myapp
```

## Gestão de Configurações por Ambiente

### Arquivos de Configuração por Ambiente

1. **Configuração de Arquivos por Ambiente:**
   - Utilize arquivos de configuração por ambiente para armazenar configurações específicas de cada ambiente.

#### Exemplo de Configuração por Ambiente

```php
return [
    'local' => [
        'database' => 'mysql',
        'host' => '127.0.0.1',
    ],
    'production' => [
        'database' => 'mysql',
        'host' => '192.168.1.1',
    ],
];
```

2. **Seleção de Configuração por Ambiente:**
   - Utilize a variável de ambiente `APP_ENV` para selecionar a configuração apropriada.

#### Exemplo de Seleção de Configuração

```php
$env = env('APP_ENV', 'local');
$config = config("environments.$env");
```

## Monitoramento de Configurações

### Auditoria de Configurações

1. **Registro de Alterações de Configurações:**
   - Implemente uma auditoria para registrar alterações de configurações, garantindo rastreabilidade.

#### Exemplo de Auditoria de Configurações

```php
use Illuminate\Support\Facades\Log;

$configBefore = config('app');
config(['app.debug' => true]);
$configAfter = config('app');

Log::info('Configuração alterada', ['before' => $configBefore, 'after' => $configAfter]);
```

## Práticas Recomendadas

### Centralização de Configurações

1. **Uso de Serviços de Gestão de Configurações:**
   - Utilize serviços de gestão de configurações centralizados para gerenciar configurações em todos os ambientes de forma consistente.

### Automação de Gestão de Configurações

1. **Integração com CI/CD:**
   - Integre a gestão de configurações com pipelines de CI/CD para automatizar a aplicação de configurações durante o deploy.

## Resumo

A gestão de configurações é crucial para manter a consistência e segurança da aplicação em diferentes ambientes. Utilizando variáveis de ambiente, secret managers e arquivos de configuração por ambiente, você pode garantir que a aplicação funcione corretamente e com segurança. Implementar práticas recomendadas e monitorar alterações de configurações ajudam a manter a aplicação estável e segura.
