# Trabalhando com SendGrid

SendGrid é um dos provedores de serviços de email mais populares, oferecendo uma API robusta para o envio de emails transacionais e marketing em larga escala. Integrar o SendGrid com Laravel é um processo direto que pode melhorar significativamente a entregabilidade dos seus emails.

## Passo 1: Configurar SendGrid no Laravel

### Instalar o Pacote SendGrid

Primeiro, instale o pacote SendGrid utilizando o Composer:

```bash
composer require sendgrid/sendgrid
```

### Configurar as Credenciais no Arquivo `.env`

Adicione suas credenciais do SendGrid no arquivo `.env`:

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.sendgrid.net
MAIL_PORT=587
MAIL_USERNAME=apikey
MAIL_PASSWORD=your_sendgrid_api_key
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=your_email@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Configurar o Mailer no Arquivo `config/mail.php`

Certifique-se de que as configurações de email estão corretas no arquivo `config/mail.php`:

```php
return [
    'default' => env('MAIL_MAILER', 'smtp'),

    'mailers' => [
        'smtp' => [
            'transport' => 'smtp',
            'host' => env('MAIL_HOST', 'smtp.sendgrid.net'),
            'port' => env('MAIL_PORT', 587),
            'encryption' => env('MAIL_ENCRYPTION', 'tls'),
            'username' => env('MAIL_USERNAME'),
            'password' => env('MAIL_PASSWORD'),
            'timeout' => null,
        ],
        // Outras configurações...
    ],

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
];
```

## Passo 2: Enviar Emails com SendGrid

### Criar uma Classe Mailable

Use o comando Artisan para criar uma classe Mailable:

```bash
php artisan make:mail SendGridMail
```

### Exemplo de Classe Mailable

No arquivo `app/Mail/SendGridMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class SendGridMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.sendgrid')
                    ->subject($this->details['subject']);
    }
}
```

### Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/sendgrid.blade.php`:

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

### Controlador para Enviar Emails

No controlador, envie o email utilizando a classe Mailable:

```php
use App\Mail\SendGridMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendSendGridEmail(Request $request)
    {
        $details = [
            'subject' => 'Assunto do Email',
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.'
        ];

        Mail::to('destinatario@example.com')->send(new SendGridMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Passo 3: Monitorar e Gerenciar Emails no SendGrid

### Dashboard do SendGrid

Acesse o painel de controle do SendGrid para monitorar e gerenciar os emails enviados. O SendGrid fornece análises detalhadas, incluindo taxas de abertura, cliques, bounces e relatórios de spam.

### Configurar Webhooks para Monitoramento

Configure webhooks no painel do SendGrid para receber notificações sobre eventos importantes, como bounces, reclamações de spam e aberturas de email.

## Benefícios de Usar SendGrid

- **Alta Entregabilidade**: Melhor taxa de entrega devido à infraestrutura robusta e boa reputação de IP.
- **Escalabilidade**: Capacidade de enviar grandes volumes de emails sem comprometer a performance.
- **Análises Detalhadas**: Acesso a métricas detalhadas para monitorar o desempenho das suas campanhas de email.

## Resumo

Integrar o SendGrid com Laravel é um processo simples que oferece várias vantagens, incluindo alta entregabilidade, escalabilidade e análises detalhadas. Com as configurações corretas e o uso das classes Mailable, você pode enviar emails de forma eficiente e monitorar o desempenho das suas campanhas diretamente no painel do SendGrid.
