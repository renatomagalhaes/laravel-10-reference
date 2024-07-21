# Configuração em Produção

Configurar o envio de emails em um ambiente de produção é essencial para garantir a confiabilidade e a segurança das suas comunicações por email. No Laravel, isso envolve ajustar as configurações do servidor de email, otimizar a performance e garantir a conformidade com as melhores práticas de segurança.

## Passo 1: Configurar o Servidor de Email

### Arquivo `.env` de Produção

Certifique-se de que as credenciais do servidor de email estão corretamente configuradas no arquivo `.env` do ambiente de produção.

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailgun.org
MAIL_PORT=587
MAIL_USERNAME=your_production_mailgun_username
MAIL_PASSWORD=your_production_mailgun_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=no-reply@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Otimizar o `config/mail.php`

No arquivo `config/mail.php`, confirme que as configurações utilizam as variáveis de ambiente do arquivo `.env`.

```php
return [
    'default' => env('MAIL_MAILER', 'smtp'),

    'mailers' => [
        'smtp' => [
            'transport' => 'smtp',
            'host' => env('MAIL_HOST', 'smtp.mailgun.org'),
            'port' => env('MAIL_PORT', 587),
            'encryption' => env('MAIL_ENCRYPTION', 'tls'),
            'username' => env('MAIL_USERNAME'),
            'password' => env('MAIL_PASSWORD'),
            'timeout' => null,
            'auth_mode' => null,
        ],
    ],

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
];
```

## Passo 2: Otimizar a Performance

### Usar Filas (Queues)

Para evitar sobrecarregar o servidor de email e garantir que os emails sejam enviados de forma assíncrona, utilize filas.

#### Configurar Queues

No arquivo `config/queue.php`, configure a fila para usar o driver desejado, como `database` ou `redis`.

```php
'default' => env('QUEUE_CONNECTION', 'database'),
```

#### Enviar Emails com Fila

No seu controlador, enfileire os emails para envio:

```php
use App\Jobs\SendEmailJob;
use Illuminate\Http\Request;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        SendEmailJob::dispatch($details['email'], $details['subject'], $details['body'])->onQueue('emails');

        return response()->json(['message' => 'Email enfileirado para envio']);
    }
}
```

## Passo 3: Garantir a Segurança

### Configurar SPF, DKIM e DMARC

Certifique-se de que seu domínio está configurado corretamente com SPF, DKIM e DMARC para garantir a autenticidade dos emails enviados.

#### Exemplo de Registro SPF

```txt
v=spf1 include:sendgrid.net ~all
```

#### Configuração DKIM

Siga as instruções do seu provedor de email (por exemplo, SendGrid ou Amazon SES) para configurar DKIM no seu domínio.

#### Exemplo de Registro DMARC

```txt
v=DMARC1; p=none; rua=mailto:dmarc-reports@example.com
```

## Passo 4: Monitorar e Logar Emails

### Habilitar Logging de Emails

Configure o Laravel para logar os emails enviados, facilitando o monitoramento e a auditoria.

```php
use Illuminate\Support\Facades\Mail;
use Illuminate\Support\Facades\Log;

Mail::alwaysSending(function ($message) {
    Log::info('Email enviado para ' . $message->getTo());
});
```

## Passo 5: Manter a Conformidade

### Políticas de Privacidade e Conformidade

Certifique-se de que seus emails estão em conformidade com leis e regulamentos de privacidade, como o GDPR.

#### Incluir Informações de Contato e Opt-Out

Inclua sempre informações de contato claras e uma opção de cancelamento de inscrição (opt-out) nos seus emails.

### Exemplo de Opt-Out

```html
<p>Se você não deseja mais receber nossos emails, <a href="{{ unsubscribe_link }}">clique aqui para cancelar a inscrição</a>.</p>
```

## Resumo

Configurar o envio de emails em produção no Laravel envolve ajustar as configurações do servidor de email, otimizar a performance utilizando filas, garantir a segurança com SPF, DKIM e DMARC, monitorar e logar emails enviados e manter a conformidade com leis de privacidade. Com essas práticas, você pode garantir que seus emails sejam entregues de forma eficiente, segura e confiável.
