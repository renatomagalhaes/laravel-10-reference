# Emails Multitenant

Em aplicações multitenant, é comum ter a necessidade de enviar emails personalizados para cada tenant (locatário) com configurações específicas, como remetente, credenciais SMTP e templates de email. O Laravel facilita a implementação de emails multitenant utilizando o conceito de contextos e configurações dinâmicas.

## Passo 1: Configurar a Estrutura do Projeto

### Definir o Modelo de Tenant

Crie um modelo para representar os tenants na sua aplicação. Este modelo armazenará as informações de configuração de email específicas de cada tenant.

```php
php artisan make:model Tenant
```

### Exemplo de Modelo de Tenant

No arquivo `app/Models/Tenant.php`, defina o modelo:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Tenant extends Model
{
    use HasFactory;

    protected $fillable = [
        'name', 'email_from_address', 'email_from_name', 'smtp_host', 'smtp_port', 'smtp_username', 'smtp_password', 'smtp_encryption'
    ];
}
```

## Passo 2: Configurar o Contexto de Tenant

### Middleware para Definir o Tenant Atual

Crie um middleware para definir o tenant atual com base no subdomínio ou outra lógica específica.

```php
php artisan make:middleware SetTenant
```

### Exemplo de Middleware

No arquivo `app/Http/Middleware/SetTenant.php`, defina a lógica para definir o tenant atual:

```php
namespace App\Http\Middleware;

use Closure;
use App\Models\Tenant;

class SetTenant
{
    public function handle($request, Closure $next)
    {
        $tenant = Tenant::where('subdomain', $request->getHost())->firstOrFail();
        app()->instance(Tenant::class, $tenant);

        return $next($request);
    }
}
```

### Registrar o Middleware

No arquivo `app/Http/Kernel.php`, registre o middleware:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'set.tenant' => \App\Http\Middleware\SetTenant::class,
];
```

## Passo 3: Configurar Dinamicamente o Mailer

### Alterar Configurações de Email em Tempo de Execução

No controlador, altere as configurações de email com base nas informações do tenant atual.

```php
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $tenant = app(Tenant::class);

        config([
            'mail.mailers.smtp.host' => $tenant->smtp_host,
            'mail.mailers.smtp.port' => $tenant->smtp_port,
            'mail.mailers.smtp.username' => $tenant->smtp_username,
            'mail.mailers.smtp.password' => $tenant->smtp_password,
            'mail.mailers.smtp.encryption' => $tenant->smtp_encryption,
            'mail.from.address' => $tenant->email_from_address,
            'mail.from.name' => $tenant->email_from_name,
        ]);

        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        Mail::to($details['email'])->send(new \App\Mail\TenantMail($details));

        return response()->json(['message' => 'Email enviado com sucesso!']);
    }
}
```

## Passo 4: Criar a Classe Mailable

### Exemplo de Classe Mailable

No arquivo `app/Mail/TenantMail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class TenantMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.tenant')
                    ->subject($this->details['subject']);
    }
}
```

### Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/tenant.blade.php`:

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

## Benefícios dos Emails Multitenant

- **Personalização**: Permite personalizar as configurações de email para cada tenant.
- **Isolamento**: Garante que cada tenant utilize suas próprias credenciais e configurações de SMTP.
- **Flexibilidade**: Facilita a gestão de múltiplos tenants em uma única aplicação.

## Resumo

Implementar emails multitenant no Laravel oferece flexibilidade e personalização, permitindo que cada tenant utilize suas próprias configurações de email. Utilizando middlewares e configuração dinâmica, você pode garantir que os emails sejam enviados de forma correta e personalizada para cada tenant.
