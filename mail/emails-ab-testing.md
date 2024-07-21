# Como Trabalhar com Teste A/B com Controle de Porcentagem

Testes A/B são uma prática eficaz para comparar diferentes versões de emails e determinar qual deles tem melhor desempenho em termos de abertura, cliques e conversões. Implementar testes A/B com controle de porcentagem no Laravel envolve a divisão de sua lista de emails em grupos e o envio de diferentes versões de emails para cada grupo.

## Passo 1: Configurar a Estrutura do Projeto

### Definir o Modelo de Teste A/B

Crie um modelo para representar os testes A/B na sua aplicação. Este modelo armazenará as informações sobre os diferentes grupos de teste e suas versões de emails.

```php
php artisan make:model ABTest
```

### Exemplo de Modelo de Teste A/B

No arquivo `app/Models/ABTest.php`, defina o modelo:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class ABTest extends Model
{
    use HasFactory;

    protected $fillable = [
        'name', 'group_a_percentage', 'group_b_percentage', 'email_version_a', 'email_version_b'
    ];
}
```

## Passo 2: Criar a Tabela de Testes A/B

### Criar a Migração

Crie uma migração para a tabela de testes A/B usando o comando Artisan:

```bash
php artisan make:migration create_ab_tests_table
```

### Exemplo de Migração

No arquivo de migração gerado, defina a estrutura da tabela:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateAbTestsTable extends Migration
{
    public function up()
    {
        Schema::create('ab_tests', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->integer('group_a_percentage');
            $table->integer('group_b_percentage');
            $table->string('email_version_a');
            $table->string('email_version_b');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('ab_tests');
    }
}
```

### Executar a Migração

Execute a migração para criar a tabela:

```bash
php artisan migrate
```

## Passo 3: Implementar a Lógica de Distribuição

### Middleware para Distribuição de Testes A/B

Crie um middleware para distribuir os usuários em grupos de teste A/B com base na porcentagem configurada.

```php
php artisan make:middleware DistributeABTest
```

### Exemplo de Middleware

No arquivo `app/Http/Middleware/DistributeABTest.php`, defina a lógica de distribuição:

```php
namespace App\Http\Middleware;

use Closure;
use App\Models\ABTest;

class DistributeABTest
{
    public function handle($request, Closure $next)
    {
        $abTest = ABTest::first();

        if ($abTest) {
            $randomNumber = mt_rand(1, 100);

            if ($randomNumber <= $abTest->group_a_percentage) {
                $request->attributes->set('email_version', 'A');
            } else {
                $request->attributes->set('email_version', 'B');
            }
        }

        return $next($request);
    }
}
```

### Registrar o Middleware

No arquivo `app/Http/Kernel.php`, registre o middleware:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'ab.test' => \App\Http\Middleware\DistributeABTest::class,
];
```

### Aplicar o Middleware às Rotas

Aplique o middleware `ab.test` às rotas de envio de emails:

```php
Route::post('/send-email', 'EmailController@send')->middleware('ab.test');
```

## Passo 4: Enviar Emails de Teste A/B

### Controlador para Enviar Emails de Teste A/B

No controlador, envie as diferentes versões de email com base no grupo de teste:

```php
use App\Mail\VersionAEmail;
use App\Mail\VersionBEmail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        $emailVersion = $request->attributes->get('email_version');

        if ($emailVersion === 'A') {
            Mail::to($details['email'])->send(new VersionAEmail($details));
        } else {
            Mail::to($details['email'])->send(new VersionBEmail($details));
        }

        return response()->json(['message' => 'Email de teste A/B enviado com sucesso!']);
    }
}
```

### Criar as Classes Mailable

Crie as classes Mailable para cada versão do email:

```bash
php artisan make:mail VersionAEmail
php artisan make:mail VersionBEmail
```

#### Exemplo de Classe Mailable para Versão A

No arquivo `app/Mail/VersionAEmail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class VersionAEmail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.version_a')
                    ->subject($this->details['subject']);
    }
}
```

#### Exemplo de Classe Mailable para Versão B

No arquivo `app/Mail/VersionBEmail.php`, defina a estrutura do email:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class VersionBEmail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function build()
    {
        return $this->view('emails.version_b')
                    ->subject($this->details['subject']);
    }
}
```

### Criar as Views dos Emails

Crie os arquivos de view para cada versão do email em `resources/views/emails/version_a.blade.php` e `resources/views/emails/version_b.blade.php`.

#### Exemplo de View para Versão A

```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $details['title'] }}</title>
</head>
<body>
    <h1>{{ $details['body'] }}</h1>
    <p>Esta é a versão A do email.</p>
</body>
</html>
```

#### Exemplo de View para Versão B

```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $details['title'] }}</title>
</head>
<body>
    <h1>{{ $details['body'] }}</h1>
    <p>Esta é a versão B do email.</p>
</body>
</html>
```

## Benefícios dos Testes A/B

- **Otimização de Conteúdo**: Identifica a versão de email que melhor ressoa com os destinatários.
- **Melhoria da Entregabilidade**: Ajusta elementos que podem influenciar a taxa de abertura e cliques.
- **Análise de Desempenho**: Fornece dados valiosos sobre o comportamento dos usuários em resposta a diferentes versões de email.

## Resumo

Implementar testes A/B com controle de porcentagem no Laravel permite otimizar suas campanhas de email e obter insights valiosos sobre o comportamento dos usuários. Utilizando modelos, middlewares e classes Mailable, você pode gerenciar eficientemente a distribuição e o envio de diferentes versões de emails para realizar testes A/B eficazes.
