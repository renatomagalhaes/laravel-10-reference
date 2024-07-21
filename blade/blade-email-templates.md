# Utilização de View Blade como Template de E-mail

Utilizar views Blade como templates de e-mail permite criar e-mails dinâmicos e personalizados com a mesma facilidade e funcionalidade das views regulares.

## Criando um Template de E-mail

Crie uma view chamada `email-template`:

```php
<!-- resources/views/emails/email-template.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title')</title>
</head>
<body>
    <h1>@yield('header')</h1>
    <p>@yield('body')</p>
</body>
</html>
```

## Enviando um E-mail com o Template

No controlador, utilize a view Blade para enviar um e-mail.

### Exemplo de Envio de E-mail

```php
use Illuminate\Support\Facades\Mail;

class MailController extends Controller
{
    public function sendEmail()
    {
        $data = [
            'title' => 'Título do E-mail',
            'header' => 'Cabeçalho do E-mail',
            'body' => 'Corpo do e-mail com conteúdo dinâmico.',
        ];

        Mail::send('emails.email-template', $data, function ($message) {
            $message->to('user@example.com')
                    ->subject('Assunto do E-mail');
        });

        return 'E-mail enviado com sucesso!';
    }
}
```

### Exemplo de Template de E-mail

```php
<!-- resources/views/emails/email-template.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>{{ $title }}</title>
</head>
<body>
    <h1>{{ $header }}</h1>
    <p>{{ $body }}</p>
</body>
</html>
```

## Resumo

Utilizar views Blade como templates de e-mail no Laravel permite criar e-mails dinâmicos e personalizados, mantendo a consistência e a reutilização de código. Com a flexibilidade do Blade, você pode incluir diretivas, componentes e outras funcionalidades para criar templates de e-mail eficientes e fáceis de manter.
