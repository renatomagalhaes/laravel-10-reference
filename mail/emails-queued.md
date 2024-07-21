# Envio de Emails com Fila

Utilizar filas para o envio de emails permite que sua aplicação processe o envio de emails de forma assíncrona, melhorando a performance e a responsividade do sistema. O Laravel facilita a implementação de filas para emails com a classe Mailable.

## Passo 1: Configurar a Fila

### Arquivo `.env`

Certifique-se de que a configuração de fila está correta no arquivo `.env`.

```env
QUEUE_CONNECTION=database
```

### Configurar as Filas

No arquivo `config/queue.php`, configure a conexão de fila de acordo com o driver escolhido (neste caso, `database`).

```php
'default' => env('QUEUE_CONNECTION', 'database'),

'connections' => [
    'database' => [
        'driver' => 'database',
        'table' => 'jobs',
        'queue' => 'default',
        'retry_after' => 90,
    ],
    // Outras conexões...
],
```

## Passo 2: Criar a Tabela de Jobs

Se você estiver usando o driver `database`, crie a tabela de jobs usando o comando Artisan:

```bash
php artisan queue:table
php artisan migrate
```

## Passo 3: Criar uma Classe Mailable com Fila

Use o comando Artisan para criar uma classe Mailable que utilize filas:

```bash
php artisan make:mail QueuedMail --queued
```

### Exemplo de Classe Mailable com Fila

No arquivo `app/Mail/QueuedMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class QueuedMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.queued')
                    ->subject('Assunto do Email');
    }
}
```

## Passo 4: Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/queued.blade.php`:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $details['title'] }}</title>
</head>
<body>
    <h1>{{ $details['body'] }}</h1>
    <p>Obrigado!</p>
</body>
</html>
```

## Passo 5: Enfileirar o Email

No controlador, enfileire o email utilizando a classe Mailable com fila:

```php
use App\Mail\QueuedMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendQueuedEmail(Request $request)
    {
        $details = [
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.'
        ];

        Mail::to('destinatario@example.com')->queue(new QueuedMail($details));

        return response()->json(['message' => 'Email enfileirado para envio']);
    }
}
```

## Passo 6: Processar a Fila

Use o comando Artisan para iniciar o processamento da fila:

```bash
php artisan queue:work
```

## Benefícios do Envio de Emails com Fila

- **Melhoria da Performance**: Permite que o envio de emails seja processado em segundo plano, liberando a aplicação para outras tarefas.
- **Escalabilidade**: Facilita o processamento de grandes volumes de emails.
- **Gestão de Tarefas Assíncronas**: Gerencia eficientemente tarefas que podem demorar para serem completadas.

## Resumo

O uso de filas para o envio de emails no Laravel melhora significativamente a performance e a escalabilidade da sua aplicação. Com a configuração correta e o uso das classes Mailable, você pode enfileirar emails para envio assíncrono de forma eficiente.
