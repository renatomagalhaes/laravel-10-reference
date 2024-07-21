# Read

Facades no Laravel são uma maneira prática e expressiva de acessar classes do framework. Utilizar facades para leitura de dados permite que você realize consultas de forma simples e eficiente.

## Utilizando Facades para Ler Registros

As facades podem ser usadas para consultar dados do banco de dados. Vamos ver como podemos usar a facade DB para realizar consultas.

### Exemplo Básico de Seleção

```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')->get();
```

### Usando Eloquent com Facades

Além de utilizar a facade DB, você pode utilizar facades junto com Eloquent para consultar registros.

```php
use App\Models\User;

$users = User::all();
```

### Consultas com Condições

Você pode adicionar condições às suas consultas para filtrar os resultados.

```php
$users = DB::table('users')->where('active', 1)->get();
```

### Selecionando Campos Específicos

Você pode selecionar campos específicos para otimizar suas consultas e reduzir a quantidade de dados retornados.

```php
$users = DB::table('users')->select('name', 'email')->get();
```

### Ordenando Resultados

Você pode ordenar os resultados da consulta usando a cláusula `orderBy`.

```php
$users = DB::table('users')->orderBy('name', 'asc')->get();
```

### Paginando Resultados

Para lidar com grandes volumes de dados, você pode paginar os resultados da consulta.

```php
$users = DB::table('users')->paginate(15);
```

## Resumo

Utilizar facades para ler registros do banco de dados é uma maneira prática e eficiente de realizar consultas. As facades simplificam o acesso a várias funcionalidades do framework, facilitando a realização de operações de leitura.
