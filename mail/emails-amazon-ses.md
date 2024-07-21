# Trabalhando com Amazon SES para envio de email

Amazon Simple Email Service (SES) é um serviço de envio de emails flexível e escalável da AWS, que facilita o envio de emails transacionais e marketing em grande escala. Integrar o Amazon SES com Laravel é simples e oferece alta entregabilidade.

## Passo 1: Configurar Amazon SES

### Configurar Credenciais IAM

1. Acesse o console da AWS e vá para a seção IAM.
2. Crie uma nova política com permissões para Amazon SES.
3. Crie um novo usuário e anexe a política criada.
4. Anote o `Access Key ID` e `Secret Access Key` gerados.

### Verificar o Domínio

1. Acesse o console do Amazon SES.
2. Vá para a seção "Domínios" e clique em "Verificar um Novo Domínio".
3. Siga as instruções para adicionar os registros DNS necessários ao seu provedor de domínio.

## Passo 2: Configurar o Laravel para Usar Amazon SES

### Instalar o SDK da AWS

Instale o pacote AWS SDK utilizando o Composer:

```bash
composer require aws/aws-sdk-php
```

### Configurar as Credenciais no Arquivo `.env`

Adicione suas credenciais do Amazon SES no arquivo `.env`:

```env
MAIL_MAILER=ses
AWS_ACCESS_KEY_ID=your_access_key_id
AWS_SECRET_ACCESS_KEY=your_secret_access_key
AWS_DEFAULT_REGION=your_aws_region
MAIL_FROM_ADDRESS=your_email@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Configurar o Mailer no Arquivo `config/mail.php`

Certifique-se de que as configurações de email estão corretas no arquivo `config/mail.php`:

```php
return [
    'default' => env('MAIL_MAILER', 'smtp'),

    'mailers' => [
        'ses' => [
            'transport' => 'ses',
            'key' => env('AWS_ACCESS_KEY_ID'),
            'secret' => env('AWS_SECRET_ACCESS_KEY'),
            'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
        ],
        // Outras configurações...
    ],

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
];
```

## Passo 3: Enviar Emails com Amazon SES

### Criar uma Classe Mailable

Use o comando Artisan para criar uma classe Mailable:

```bash
php artisan make:mail SESMail
```

### Exemplo de Classe Mailable

No arquivo `app/Mail/SESMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class SESMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.ses')
                    ->subject($this->details['subject']);
    }
}
```

### Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/ses.blade.php`:

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
use App\Mail\SESMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendSESEmail(Request $request)
    {
        $details = [
            'subject' => 'Assunto do Email',
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.'
        ];

        Mail::to('destinatario@example.com')->send(new SESMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Benefícios de Usar Amazon SES

- **Alta Entregabilidade**: Alta taxa de entrega devido à infraestrutura da AWS.
- **Escalabilidade**: Capacidade de enviar grandes volumes de emails de forma eficiente.
- **Custo-efetividade**: Preços competitivos para envio de emails em larga escala.

## Resumo

Integrar Amazon SES com Laravel é um processo simples que oferece alta entregabilidade e escalabilidade para suas campanhas de email. Com as configurações corretas e o uso das classes Mailable, você pode enviar emails de forma eficiente e monitorar o desempenho das suas comunicações diretamente no console da AWS.
