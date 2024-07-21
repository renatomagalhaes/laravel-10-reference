# Monitoramento e Logging de Emails

Monitorar e logar o envio de emails é essencial para garantir a entrega e diagnosticar problemas. O Laravel oferece várias ferramentas para facilitar o monitoramento e logging de emails.

## Passo 1: Habilitar Logging de Emails

### Configurar o Canal de Logging

No arquivo `config/logging.php`, configure um canal específico para logar os emails:

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['single'],
    ],
    'single' => [
        'driver' => 'single',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
    ],
    'email' => [
        'driver' => 'single',
        'path' => storage_path('logs/email.log'),
        'level' => 'info',
    ],
],
```

### Logar Emails no Controlador

No controlador, adicione o código para logar os detalhes do email enviado:

```php
use Illuminate\Support\Facades\Log;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        // Envio do email
        Mail::to($details['email'])->send(new SimpleMail($details));

        // Logar o email enviado
        Log::channel('email')->info('Email enviado', $details);

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Passo 2: Monitoramento de Emails com Event Listeners

### Criar um Listener para Eventos de Email

Use o comando Artisan para criar um listener:

```bash
php artisan make:listener LogSentMessage
```

### Exemplo de Listener

No arquivo `app/Listeners/LogSentMessage.php`, defina a lógica para logar os emails enviados:

```php
namespace App\Listeners;

use Illuminate\Mail\Events\MessageSent;
use Illuminate\Support\Facades\Log;

class LogSentMessage
{
    public function handle(MessageSent $event)
    {
        $message = $event->message;
        $logData = [
            'to' => $message->getTo(),
            'subject' => $message->getSubject(),
            'body' => $message->getBody(),
        ];

        Log::channel('email')->info('Email enviado', $logData);
    }
}
```

### Registrar o Listener

No arquivo `app/Providers/EventServiceProvider.php`, registre o listener para o evento `MessageSent`:

```php
protected $listen = [
    'Illuminate\Mail\Events\MessageSent' => [
        'App\Listeners\LogSentMessage',
    ],
];
```

## Passo 3: Monitoramento de Emails com Horizon

### Instalar e Configurar o Horizon

Se você estiver utilizando filas, o Laravel Horizon pode ser uma excelente ferramenta para monitorar o processamento de jobs, incluindo o envio de emails.

```bash
composer require laravel/horizon
```

Publique as configurações do Horizon:

```bash
php artisan horizon:install
```

Configure o Horizon no arquivo `config/horizon.php` e defina suas filas e processos de monitoramento.

### Executar o Horizon

Inicie o Horizon para monitorar o processamento das filas:

```bash
php artisan horizon
```

### Acessar o Dashboard do Horizon

O Horizon fornece um dashboard interativo para monitorar as filas. Acesse `http://your-app/horizon` para visualizar o status dos jobs e detalhes de monitoramento.

## Benefícios do Monitoramento e Logging de Emails

- **Diagnóstico de Problemas**: Facilita a identificação e resolução de problemas relacionados ao envio de emails.
- **Auditoria**: Mantém um registro detalhado dos emails enviados, ajudando na auditoria e conformidade.
- **Monitoramento em Tempo Real**: Com o Horizon, você pode monitorar o processamento das filas em tempo real, garantindo que os emails sejam enviados conforme esperado.

## Resumo

Monitorar e logar o envio de emails no Laravel é uma prática essencial para garantir a entrega eficiente e diagnosticar problemas. Utilizando os recursos de logging e monitoramento do Laravel, você pode manter um controle detalhado das suas comunicações por email e garantir que tudo esteja funcionando corretamente.
