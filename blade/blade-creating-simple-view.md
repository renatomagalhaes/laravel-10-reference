# Criando uma View Simples

O Blade é o mecanismo de template do Laravel que permite criar views de maneira simples e eficiente. Vamos explorar como criar uma view básica utilizando Blade.

## Criando a View

As views são armazenadas no diretório `resources/views`. Para criar uma nova view, basta criar um arquivo com a extensão `.blade.php`.

### Exemplo de View Simples

Crie um arquivo chamado `welcome.blade.php` em `resources/views`:

```php
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    <h1>Bem-vindo à minha aplicação Laravel</h1>
</body>
</html>
```

## Renderizando a View

Para renderizar a view, utilize a função `view` em uma rota ou controlador.

### Exemplo de Rota

```php
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});
```

### Exemplo de Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HomeController extends Controller
{
    public function index()
    {
        return view('welcome');
    }
}
```

## Passando Dados para a View

Você pode passar dados para a view utilizando um array associativo.

### Exemplo de Passagem de Dados

```php
Route::get('/', function () {
    return view('welcome', ['name' => 'John Doe']);
});
```

### Exibindo Dados na View

Use as chaves duplas `{{ }}` para exibir os dados passados para a view.

```php
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    <h1>Bem-vindo, {{ $name }}</h1>
</body>
</html>
```

## Resumo

Criar uma view simples com Blade no Laravel é fácil e direto. As views são armazenadas no diretório `resources/views` e podem ser renderizadas usando a função `view` em rotas ou controladores. Você pode passar dados para as views e exibi-los usando chaves duplas `{{ }}`.
