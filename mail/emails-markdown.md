# Uso de Markdown para Emails

O Laravel oferece suporte ao Markdown para facilitar a criação de emails formatados de forma elegante. Com o uso de Markdown, você pode criar templates de email que são fáceis de ler e manter.

## Passo 1: Criar uma Classe Mailable com Markdown

Use o comando Artisan para criar uma classe Mailable que utiliza Markdown:

```bash
php artisan make:mail MarkdownMail --markdown=emails.markdown
```

### Exemplo de Classe Mailable com Markdown

No arquivo `app/Mail/MarkdownMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class MarkdownMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->markdown('emails.markdown')
                    ->subject('Assunto do Email');
    }
}
```

## Passo 2: Criar a View de Markdown

Crie um arquivo de view para o corpo do email em `resources/views/emails/markdown.blade.php`:

```blade
@component('mail::message')
# {{ $details['title'] }}

{{ $details['body'] }}

@component('mail::button', ['url' => $details['url']])
Clique Aqui
@endcomponent

Obrigado,<br>
{{ config('app.name') }}
@endcomponent
```

## Passo 3: Enviar o Email

No controlador, envie o email utilizando a classe Mailable com Markdown:

```php
use App\Mail\MarkdownMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendMarkdownEmail(Request $request)
    {
        $details = [
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.',
            'url' => 'https://example.com'
        ];

        Mail::to('destinatario@example.com')->send(new MarkdownMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Personalização de Componentes de Markdown

Você pode personalizar os componentes de Markdown utilizados nos emails. O Laravel fornece uma série de componentes que podem ser personalizados de acordo com suas necessidades.

### Publicar os Componentes Padrão

Para personalizar os componentes, primeiro publique os componentes padrão utilizando o Artisan:

```bash
php artisan vendor:publish --tag=laravel-mail
```

Os componentes serão copiados para `resources/views/vendor/mail`. Você pode editar esses arquivos para personalizar a aparência dos seus emails.

## Benefícios do Uso de Markdown

- **Leitura e Manutenção**: Facilita a leitura e manutenção dos templates de email.
- **Componentização**: Permite o uso de componentes reutilizáveis e personalizados.
- **Consistência Visual**: Garante uma aparência consistente para todos os emails.

## Resumo

O uso de Markdown para emails no Laravel simplifica a criação e manutenção de templates de email elegantes e consistentes. Utilizando classes Mailable com Markdown e personalizando os componentes, você pode criar emails que são fáceis de ler, manter e personalizar.
