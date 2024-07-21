# Personalização de Templates de Email

Personalizar templates de email permite que você mantenha uma aparência consistente e profissional nas suas comunicações. O Laravel facilita a personalização dos templates de email, permitindo o uso de Markdown e Blade para criar designs elegantes e reutilizáveis.

## Passo 1: Criar um Template de Email com Markdown

### Exemplo de Template de Email com Markdown

Crie um arquivo de view para o corpo do email em `resources/views/emails/template.blade.php`:

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

### Utilizando o Template

No arquivo da classe Mailable (`app/Mail/TemplateMail.php`), utilize o template criado:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class TemplateMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->markdown('emails.template')
                    ->subject('Assunto do Email');
    }
}
```

## Passo 2: Personalizar Componentes de Email

### Publicar Componentes Padrão

Para personalizar os componentes de email padrão, primeiro publique os arquivos utilizando o Artisan:

```bash
php artisan vendor:publish --tag=laravel-mail
```

Os componentes serão copiados para `resources/views/vendor/mail`. Você pode editar esses arquivos para personalizar a aparência dos seus emails.

### Exemplo de Personalização

Edite o arquivo `resources/views/vendor/mail/text/button.blade.php` para personalizar o botão do email:

```blade
<tr>
    <td class="button" align="center" valign="middle">
        <table border="0" cellpadding="0" cellspacing="0">
            <tr>
                <td align="center" bgcolor="#3869D4" style="border-radius: 3px;">
                    <a href="{{ $url }}" target="_blank" style="padding: 12px 24px; font-size: 16px; color: #FFF; text-decoration: none;">{{ $slot }}</a>
                </td>
            </tr>
        </table>
    </td>
</tr>
```

## Passo 3: Criar Componentes Personalizados

Você pode criar seus próprios componentes de email para reutilizar em diferentes partes dos seus templates.

### Exemplo de Componente Personalizado

Crie um novo componente de email em `resources/views/components/email/header.blade.php`:

```blade
<div style="background-color: #f8f9fa; padding: 20px; text-align: center;">
    <h1>{{ $slot }}</h1>
</div>
```

### Utilizando o Componente

No seu template de email, inclua o componente personalizado:

```blade
@component('mail::message')
@component('components.email.header')
    {{ $details['header'] }}
@endcomponent

# {{ $details['title'] }}

{{ $details['body'] }}

@component('mail::button', ['url' => $details['url']])
Clique Aqui
@endcomponent

Obrigado,<br>
{{ config('app.name') }}
@endcomponent
```

## Benefícios da Personalização de Templates

- **Consistência Visual**: Garante uma aparência consistente e profissional em todas as comunicações.
- **Reutilização de Componentes**: Facilita a reutilização de componentes personalizados, economizando tempo e esforço.
- **Flexibilidade**: Permite a criação de designs complexos e adaptados às necessidades específicas da sua aplicação.

## Resumo

A personalização de templates de email no Laravel é um processo que oferece flexibilidade e eficiência. Com o uso de Markdown e Blade, você pode criar designs consistentes e profissionais, reutilizar componentes personalizados e garantir que suas comunicações atendam às expectativas visuais da sua marca.
