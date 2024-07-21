# Envio de Emails em Lote

Enviar emails em lote é uma prática comum para campanhas de marketing, newsletters e outras comunicações em massa. O Laravel facilita o envio de emails em lote utilizando filas para garantir que o processo seja eficiente e escalável.

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

## Passo 3: Criar uma Classe Mailable

Use o comando Artisan para criar uma classe Mailable:

```bash
php artisan make:mail BulkMail
```

### Exemplo de Classe Mailable

No arquivo `app/Mail/BulkMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class BulkMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.bulk')
                    ->subject($this->details['subject']);
    }
}
```

## Passo 4: Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/bulk.blade.php`:

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

## Passo 5: Enviar Emails em Lote

### Controlador para Enviar Emails em Lote

No controlador, enfileire os emails para envio em lote utilizando a classe Mailable:

```php
use App\Mail\BulkMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendBulkEmail(Request $request)
    {
        $details = [
            'subject' => 'Assunto da Campanha',
            'body' => 'Este é o corpo do email da campanha.'
        ];

        $users = User::all(); // Selecionar os destinatários

        foreach ($users as $user) {
            Mail::to($user->email)->queue(new BulkMail($details));
        }

        return response()->json(['message' => 'Emails em lote enfileirados para envio']);
    }
}
```

### Processar a Fila

Use o comando Artisan para iniciar o processamento da fila:

```bash
php artisan queue:work
```

## Passo 6: Monitorar o Envio de Emails em Lote

### Monitorar com Horizon

Se você estiver utilizando filas, o Laravel Horizon pode ser uma excelente ferramenta para monitorar o processamento de jobs, incluindo o envio de emails em lote.

```bash
composer require laravel/horizon
```

Publique as configurações do Horizon:

```bash
php artisan horizon:install
```

Inicie o Horizon para monitorar o processamento das filas:

```bash
php artisan horizon
```

Acesse o dashboard do Horizon em `http://your-app/horizon` para visualizar o status dos jobs e detalhes de monitoramento.

## Benefícios do Envio de Emails em Lote

- **Eficiência**: Permite o envio de grandes volumes de emails de forma escalável e eficiente.
- **Performance**: Utiliza filas para processar os emails em segundo plano, melhorando a responsividade da aplicação.
- **Monitoramento**: Ferramentas como o Horizon permitem monitorar o processamento dos emails em tempo real.

## Resumo

O envio de emails em lote no Laravel é um processo eficiente e escalável, utilizando filas para garantir que os emails sejam enviados de forma assíncrona. Com a configuração correta e o uso das classes Mailable, você pode gerenciar campanhas de email em massa de forma eficaz.
