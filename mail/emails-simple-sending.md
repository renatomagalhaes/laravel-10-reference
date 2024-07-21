# Envio de Emails Simples

Enviar emails simples no Laravel é uma tarefa direta, graças ao seu sistema de mailers e ao uso de classes Mailable. Esta seção abordará como configurar e enviar um email básico.

## Passo 1: Configurar o Servidor de Email

Certifique-se de que o servidor de email está configurado corretamente no arquivo `.env`.

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailgun.org
MAIL_PORT=587
MAIL_USERNAME=your_mailgun_username
MAIL_PASSWORD=your_mailgun_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=your_email@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

## Passo 2: Criar uma Classe Mailable

Use o comando Artisan para criar uma classe Mailable:

```bash
php artisan make:mail SimpleMail
```

### Exemplo de Classe Mailable

No arquivo `app/Mail/SimpleMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class SimpleMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->subject('Assunto do Email')
                    ->view('emails.simple');
    }
}
```

## Passo 3: Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/simple.blade.php`:

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

## Passo 4: Enviar o Email

No controlador, envie o email utilizando a classe Mailable:

```php
use App\Mail\SimpleMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = [
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.'
        ];

        Mail::to('destinatario@example.com')->send(new SimpleMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Resumo

Enviar emails simples no Laravel é fácil com o uso de classes Mailable e a configuração correta do servidor de email. Com apenas alguns passos, você pode configurar e enviar emails básicos de forma eficiente.
