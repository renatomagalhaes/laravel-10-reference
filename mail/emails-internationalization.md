# Internacionalização de Emails

A internacionalização (i18n) permite que sua aplicação suporte múltiplos idiomas, proporcionando uma experiência personalizada para usuários de diferentes regiões. O Laravel facilita a tradução de emails utilizando seus recursos de localização.

## Passo 1: Configurar os Arquivos de Localização

### Estrutura de Pastas

Crie pastas de localização no diretório `resources/lang`. Por exemplo, para suportar inglês e português, crie as pastas `en` e `pt`.

### Arquivos de Tradução

Crie arquivos de tradução para emails dentro dessas pastas. Por exemplo, `emails.php`:

#### `resources/lang/en/emails.php`

```php
return [
    'greeting' => 'Hello!',
    'body' => 'This is the body of the email.',
    'thanks' => 'Thank you for using our application!',
];
```

#### `resources/lang/pt/emails.php`

```php
return [
    'greeting' => 'Olá!',
    'body' => 'Este é o corpo do email.',
    'thanks' => 'Obrigado por usar nossa aplicação!',
];
```

## Passo 2: Utilizar as Traduções no Template de Email

### Exemplo de Template de Email

No template de email, utilize a função `__('key')` para acessar as traduções:

```blade
@component('mail::message')
# {{ __('emails.greeting') }}

{{ __('emails.body') }}

@component('mail::button', ['url' => $details['url']])
{{ __('emails.button') }}
@endcomponent

{{ __('emails.thanks') }},<br>
{{ config('app.name') }}
@endcomponent
```

## Passo 3: Definir o Idioma do Usuário

### Middleware para Definir o Idioma

Crie um middleware para definir o idioma do usuário com base na preferência armazenada ou no cabeçalho da requisição:

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\App;

class SetLocale
{
    public function handle($request, Closure $next)
    {
        $locale = $request->user()->locale ?? $request->header('Accept-Language');
        App::setLocale($locale);

        return $next($request);
    }
}
```

### Registrar o Middleware

No arquivo `app/Http/Kernel.php`, registre o middleware:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'set.locale' => \App\Http\Middleware\SetLocale::class,
];
```

### Aplicar o Middleware nas Rotas

Aplique o middleware às rotas que lidam com o envio de emails:

```php
Route::post('/send-email', 'EmailController@send')->middleware('set.locale');
```

## Passo 4: Enviar Emails Localizados

### Exemplo de Código para Enviar Emails Localizados

No controlador, envie o email utilizando as traduções:

```php
use App\Mail\LocalizedMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = [
            'url' => 'https://example.com'
        ];

        Mail::to('destinatario@example.com')->send(new LocalizedMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

### Classe Mailable Localizada

Crie a classe Mailable localizada em `app/Mail/LocalizedMail.php`:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class LocalizedMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.localized')
                    ->subject(__('emails.subject'));
    }
}
```

## Benefícios da Internacionalização de Emails

- **Experiência Personalizada**: Proporciona uma experiência de usuário melhorada e personalizada.
- **Suporte Multilíngue**: Permite que sua aplicação alcance um público global.
- **Flexibilidade e Escalabilidade**: Facilita a adição de novos idiomas e a manutenção das traduções.

## Resumo

A internacionalização de emails no Laravel é um processo direto que oferece flexibilidade e personalização para os usuários. Com a configuração correta dos arquivos de localização e o uso das traduções nos templates de email, você pode fornecer comunicações adaptadas a diferentes idiomas de forma eficiente e escalável.
