# Statements

Facades no Laravel fornecem uma maneira prática e expressiva de executar declarações SQL brutas diretamente no banco de dados. Utilizar facades para executar declarações SQL permite realizar operações que não são suportadas diretamente pelo Eloquent ou Query Builder.

## Utilizando Facades para Executar Statements

As facades podem ser usadas para executar declarações SQL brutas. Vamos ver como podemos usar a facade DB para executar essas declarações.

### Exemplo Básico de Execução de Statement

```php
use Illuminate\Support\Facades\DB;

DB::statement('DROP TABLE users');
```

### Executando Statements com Parâmetros

Você pode passar parâmetros para declarações SQL usando o método `statement`.

```php
DB::statement('INSERT INTO users (name, email) VALUES (?, ?)', ['John Doe', 'john@example.com']);
```

### Executando Statements de Seleção

Para executar uma declaração de seleção e obter os resultados, você pode usar o método `select`.

```php
$users = DB::select('SELECT * FROM users WHERE active = ?', [1]);
```

### Executando Statements de Atualização e Exclusão

Para executar declarações de atualização ou exclusão, você pode usar os métodos `update` e `delete`.

```php
DB::update('UPDATE users SET active = 0 WHERE id = ?', [1]);

DB::delete('DELETE FROM users WHERE id = ?', [1]);
```

### Executando Statements com Várias Declarações

Você pode executar várias declarações em uma única chamada usando o método `unprepared`.

```php
DB::unprepared('
    DROP TABLE IF EXISTS users;
    CREATE TABLE users (
        id INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(100),
        email VARCHAR(100) UNIQUE
    );
');
```

## Resumo

Utilizar facades para executar declarações SQL brutas permite realizar operações complexas e não suportadas diretamente pelo Eloquent ou Query Builder. As facades simplificam o acesso a várias funcionalidades do framework, facilitando a execução de operações SQL avançadas.
