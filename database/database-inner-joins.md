# Consultas Otimizadas com Inner Joins

Para melhorar a performance das consultas no banco de dados, é possível utilizar joins para combinar dados de várias tabelas. Aqui mostramos como fazer isso usando SQL puro e o Query Builder do Laravel.

## Usando SQL Puro

Com SQL puro, você pode escrever consultas complexas diretamente no Laravel utilizando o método `select` do Query Builder.

### Exemplo de Inner Join com SQL Puro

```php
$results = DB::select('
    SELECT users.name, posts.title
    FROM users
    INNER JOIN posts ON users.id = posts.user_id
    WHERE users.active = 1
');
```

## Usando o Query Builder

O Query Builder do Laravel oferece uma interface fluente para construir consultas SQL de forma programática.

### Exemplo de Inner Join com o Query Builder

```php
$results = DB::table('users')
    ->join('posts', 'users.id', '=', 'posts.user_id')
    ->where('users.active', 1)
    ->select('users.name', 'posts.title')
    ->get();
```

## Prós e Contras

### Usando SQL Puro

**Prós:**
- Maior controle sobre a consulta SQL.
- Pode ser mais performático em consultas extremamente complexas.

**Contras:**
- Menos legível e mais propenso a erros.
- Não se beneficia das abstrações e proteções do Query Builder.

### Usando o Query Builder

**Prós:**
- Mais legível e fácil de entender.
- Se beneficia das abstrações do Laravel e é mais seguro contra injeção de SQL.
- Facilita a construção dinâmica de consultas.

**Contras:**
- Pode ser menos performático em consultas extremamente complexas comparado ao SQL puro.

## Cenário Avançado: Multiplas Conexões e Bancos de Dados

No Laravel, você também pode realizar joins entre tabelas de diferentes conexões de banco de dados. Para isso, é necessário especificar a conexão no Query Builder.

### Exemplo de Join entre Conexões Diferentes

```php
$results = DB::connection('mysql')->table('users')
    ->join(DB::connection('mysql2')->getTablePrefix() . 'posts', 'users.id', '=', 'posts.user_id')
    ->where('users.active', 1)
    ->select('users.name', 'posts.title')
    ->get();
```
