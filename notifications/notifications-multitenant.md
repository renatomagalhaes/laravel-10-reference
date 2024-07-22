# Emails Multitenant

Implementar uma solução de emails multitenant permite que uma aplicação sirva múltiplos clientes (ou locatários) com a capacidade de personalizar e isolar as comunicações de email para cada um deles. Este guia aborda como configurar notificações por email em um ambiente multitenant no Laravel.

## Importância de Emails Multitenant

### Benefícios

- **Isolamento de Dados:** Garante que os dados e as comunicações de um locatário não interfiram com os de outro.
- **Personalização:** Permite personalizar os emails com a identidade visual e informações específicas de cada locatário.
- **Escalabilidade:** Facilita o gerenciamento de múltiplos clientes em uma única aplicação, mantendo a eficiência e a segurança.

## Configuração Básica de Emails Multitenant

### Passo 1: Estrutura do Banco de Dados

Adapte sua estrutura de banco de dados para suportar múltiplos locatários. Por exemplo, adicione um campo `tenant_id` às tabelas relevantes:

```bash
php artisan make:migration add_tenant_id_to_users_table --table=users
```

No arquivo de migração gerado:

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->unsignedBigInteger('tenant_id')->after('id');
        $table->foreign('tenant_id')->references('id')->on('tenants')->onDelete('cascade');
    });
}
```

Execute a migração:

```bash
php artisan migrate
```

### Passo 2: Configuração de Email por Locatário

No arquivo `.env`, defina variáveis de ambiente para cada locatário. Alternativamente, use uma tabela de configuração para armazenar essas informações:

#### Exemplo de Configuração no `.env`

```env
TENANT_1_MAIL_MAILER=smtp
TENANT_1_MAIL_HOST=smtp.mailtrap.io
TENANT_1_MAIL_PORT=587
TENANT_1_MAIL_USERNAME=tenant1_username
TENANT_1_MAIL_PASSWORD=tenant1_password
TENANT_1_MAIL_ENCRYPTION=tls
TENANT_1_MAIL_FROM_ADDRESS=tenant1@example.com
TENANT_1_MAIL_FROM_NAME="Tenant 1"

TENANT_2_MAIL_MAILER=smtp
TENANT_2_MAIL_HOST=smtp.mailtrap.io
TENANT_2_MAIL_PORT=587
TENANT_2_MAIL_USERNAME=tenant2_username
TENANT_2_MAIL_PASSWORD=tenant2_password
TENANT_2_MAIL_ENCRYPTION=tls
TENANT_2_MAIL_FROM_ADDRESS=tenant2@example.com
TENANT_2_MAIL_FROM_NAME="Tenant 2"
```

### Passo 3: Definir Configuração de Email Dinâmica

Crie um serviço para carregar a configuração de email dinamicamente com base no locatário atual:

```php
namespace App\Services;

use Illuminate\Support\Facades\Config;

class TenantEmailConfigService
{
    public static function setConfig($tenant)
    {
        Config::set('mail.mailers.smtp.host', $tenant->mail_host);
        Config::set('mail.mailers.smtp.port', $tenant->mail_port);
        Config::set('mail.mailers.smtp.username', $tenant->mail_username);
        Config::set('mail.mailers.smtp.password', $tenant->mail_password);
        Config::set('mail.mailers.smtp.encryption', $tenant->mail_encryption);
        Config::set('mail.from.address', $tenant->mail_from_address);
        Config::set('mail.from.name', $tenant->mail_from_name);
    }
}
```

### Passo 4: Utilizar a Configuração de Email Dinâmica nas Notificações

Antes de enviar uma notificação, defina a configuração de email com base no locatário atual:

```php
use App\Services\TenantEmailConfigService;
use App\Models\Tenant;
use App\Notifications\TenantNotification;

$tenant = Tenant::find($tenantId);
TenantEmailConfigService::setConfig($tenant);

$user = User::where('tenant_id', $tenant->id)->first();
$details = [
    'body' => 'This is a tenant-specific notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new TenantNotification($details));
```

## Monitoramento e Gerenciamento

### Passo 1: Monitorar Envios de Emails

Use ferramentas como Laravel Horizon para monitorar o envio de emails e garantir que as notificações sejam entregues conforme esperado.

```bash
composer require laravel/horizon
php artisan horizon:install
php artisan migrate
php artisan horizon
```

### Passo 2: Gerenciamento de Configurações

Crie interfaces administrativas para gerenciar configurações de email para cada locatário. Isso pode incluir a atualização de credenciais SMTP, ajustes de templates de email e visualização de logs de envio.

```php
Route::middleware('auth')->group(function () {
    Route::get('/tenant-email-config', 'TenantEmailConfigController@index')->name('tenant-email-config.index');
    Route::post('/tenant-email-config', 'TenantEmailConfigController@update')->name('tenant-email-config.update');
});
```

## Exemplo Prático

### Backend

1. Estruture o banco de dados para suportar múltiplos locatários.
2. Defina configurações de email dinâmicas com base no locatário.
3. Utilize a configuração de email dinâmica antes de enviar notificações.
4. Monitore o envio de emails e gerencie configurações específicas de cada locatário.

### Frontend

Crie interfaces administrativas para gerenciar configurações de email de locatários, visualizar logs de envio e ajustar templates de email conforme necessário.

## Resumo

Implementar emails multitenant no Laravel garante que as comunicações de email sejam personalizadas e isoladas para cada locatário. Com uma configuração adequada, você pode gerenciar eficazmente as notificações por email para múltiplos clientes, garantindo segurança, desempenho e confiabilidade.
