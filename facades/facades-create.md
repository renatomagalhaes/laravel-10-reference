# Create

Facades no Laravel fornecem uma interface estática para classes no container de serviços do Laravel. Elas permitem o uso de métodos estáticos para acessar várias funcionalidades do framework de maneira simples e expressiva.

## Utilizando Facades para Criar Registros

As facades podem ser usadas para realizar operações CRUD, incluindo a criação de registros. Vamos ver como podemos usar a facade DB para inserir dados em uma tabela.

### Exemplo Básico de Inserção

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->insert([
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);
```

### Usando Eloquent com Facades

Além de utilizar a facade DB, você pode utilizar facades junto com Eloquent para criar registros.

```php
use App\Models\User;

User::create([
    'name' => 'Jane Doe',
    'email' => 'jane@example.com',
    'password' => bcrypt('password'),
]);
```

### Validações ao Criar Registros

É uma boa prática validar os dados antes de criar registros no banco de dados.

```php
use Illuminate\Http\Request;
use App\Models\User;

public function store(Request $request)
{
    $validated = $request->validate([
        'name' => 'required|max:255',
        'email' => 'required|email|unique:users',
        'password' => 'required|min:8',
    ]);

    User::create([
        'name' => $validated['name'],
        'email' => $validated['email'],
        'password' => bcrypt($validated['password']),
    ]);
}
```

## Resumo

Utilizar facades para criar registros no banco de dados é uma maneira eficiente e expressiva de interagir com o Laravel. Elas simplificam o acesso a várias funcionalidades do framework, facilitando a realização de operações CRUD.
