# Controle de Taxa de Envio

Controlar a taxa de envio de emails é essencial para evitar sobrecarregar os servidores de email, prevenir bloqueios de provedores de email e garantir que suas mensagens sejam entregues de forma consistente. O Laravel oferece várias maneiras de implementar o controle de taxa de envio.

## Passo 1: Configurar Rate Limiting no Laravel

### Middleware de Rate Limiting

O Laravel fornece middleware para controle de taxa que pode ser aplicado a rotas específicas. Você pode usar o middleware `throttle` para limitar a taxa de envio de emails.

#### Exemplo de Middleware

No arquivo `app/Http/Kernel.php`, defina um middleware personalizado para limitar a taxa de envio de emails:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
];
```

### Aplicar o Middleware às Rotas

Aplique o middleware `throttle` às rotas de envio de emails para limitar a taxa de envio. Por exemplo, para permitir 5 solicitações por minuto:

```php
Route::post('/send-email', 'EmailController@send')->middleware('throttle:5,1');
```

## Passo 2: Usar Filas para Controlar a Taxa de Envio

### Configurar a Fila

No arquivo `.env`, configure a conexão de fila:

```env
QUEUE_CONNECTION=database
```

### Criar um Job para Enviar Emails

Use o comando Artisan para criar um job para enviar emails:

```bash
php artisan make:job SendEmailJob
```

#### Exemplo de Job

No arquivo `app/Jobs/SendEmailJob.php`, defina a lógica para enviar emails:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Mail;
use App\Mail\SimpleMail;

class SendEmailJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function handle()
    {
        Mail::to($this->details['email'])->send(new SimpleMail($this->details));
    }
}
```

### Enfileirar Emails para Envio

No controlador, enfileire os emails para envio utilizando o job:

```php
use App\Jobs\SendEmailJob;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        SendEmailJob::dispatch($details)->onQueue('emails');

        return response()->json(['message' => 'Email enfileirado para envio']);
    }
}
```

### Processar a Fila com Controle de Taxa

Use o comando Artisan para processar a fila com um intervalo entre os jobs, controlando a taxa de envio:

```bash
php artisan queue:work --sleep=5 --tries=3
```

## Passo 3: Usar Serviços de Email com Controle de Taxa Integrado

### Configurar Rate Limiting no Provedor de Email

Muitos provedores de email, como SendGrid e Amazon SES, oferecem configurações de controle de taxa. Configure estas opções no painel de controle do provedor para garantir que a taxa de envio esteja dentro dos limites recomendados.

#### Exemplo de Configuração no SendGrid

1. Acesse o painel de controle do SendGrid.
2. Vá para a seção "Settings" e configure o "Rate Limiting" de acordo com suas necessidades.

## Benefícios do Controle de Taxa de Envio

- **Prevenção de Bloqueios**: Evita que seus emails sejam bloqueados por provedores de email devido a picos de envio.
- **Melhoria da Entregabilidade**: Garante que seus emails sejam entregues de forma consistente e em tempo hábil.
- **Proteção da Infraestrutura**: Evita sobrecarregar seus servidores de email, mantendo a estabilidade da aplicação.

## Resumo

Implementar o controle de taxa de envio de emails é crucial para garantir a entregabilidade e prevenir problemas com provedores de email. Utilizando middleware de rate limiting, filas e configurando opções de controle de taxa nos provedores de email, você pode gerenciar a taxa de envio de forma eficaz e garantir que suas campanhas de email sejam bem-sucedidas.
