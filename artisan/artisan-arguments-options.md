# Argumentos e Opções

Os comandos Artisan podem aceitar argumentos e opções, permitindo personalizar seu comportamento com base na entrada do usuário. Este guia aborda como definir e usar argumentos e opções em comandos Artisan no Laravel.

## Introdução aos Argumentos e Opções

### O que são Argumentos?

Argumentos são valores que você passa para um comando. Eles são obrigatórios ou opcionais e são usados para fornecer dados ao comando.

### O que são Opções?

Opções são valores que modificam o comportamento do comando. Elas podem ser booleanas ou aceitar valores, e também podem ser obrigatórias ou opcionais.

## Definindo Argumentos e Opções

### Adicionando Argumentos

Você pode definir argumentos no comando Artisan especificando-os na propriedade `signature`:

```php
protected $signature = 'comando:exemplo {argumento}';
```

### Argumentos Opcionais

Para definir um argumento opcional, adicione um ponto de interrogação `?` após o nome do argumento:

```php
protected $signature = 'comando:exemplo {argumento?}';
```

### Definindo Opções

Opções são definidas usando o prefixo `--` na propriedade `signature`:

```php
protected $signature = 'comando:exemplo {--opcao}';
```

### Opções com Valores

Você pode definir opções que aceitam valores:

```php
protected $signature = 'comando:exemplo {--opcao=default}';
```

## Recuperando Argumentos e Opções

### Recuperando Argumentos

Use o método `argument` para recuperar o valor de um argumento:

```php
public function handle()
{
    $argumento = $this->argument('argumento');
    $this->info("Argumento recebido: {$argumento}");
}
```

### Recuperando Opções

Use o método `option` para recuperar o valor de uma opção:

```php
public function handle()
{
    $opcao = $this->option('opcao');
    $this->info("Opção recebida: " . ($opcao ? 'true' : 'false'));
}
```

## Exemplos Práticos

### Comando com Argumentos e Opções

Crie um comando que aceite argumentos e opções e ajuste seu comportamento com base nesses valores:

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class ArgumentOptionCommand extends Command
{
    protected $signature = 'comando:exemplo {argumento} {--opcao}';
    protected $description = 'Exemplo de comando com argumentos e opções';

    public function handle()
    {
        $argumento = $this->argument('argumento');
        $opcao = $this->option('opcao');

        $this->info("Argumento: {$argumento}");
        $this->info("Opção: " . ($opcao ? 'true' : 'false'));
    }
}
```

### Validando Argumentos e Opções

Você pode adicionar lógica de validação para garantir que os argumentos e opções recebidos sejam válidos:

```php
public function handle()
{
    $argumento = $this->argument('argumento');
    $opcao = $this->option('opcao');

    if (!is_numeric($argumento)) {
        $this->error('O argumento deve ser um número.');
        return;
    }

    if ($opcao && !in_array($opcao, ['opcao1', 'opcao2'])) {
        $this->error('A opção deve ser opcao1 ou opcao2.');
        return;
    }

    $this->info("Argumento: {$argumento}");
    $this->info("Opção: {$opcao}");
}
```

## Uso de Argumentos e Opções em Comandos Agendados

### Passando Argumentos e Opções para Comandos Agendados

Você pode passar argumentos e opções para comandos agendados no arquivo `app/Console/Kernel.php`:

```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('comando:exemplo', ['argumento' => 'valor', '--opcao' => true])
             ->daily();
}
```

## Resumo

Os argumentos e opções nos comandos Artisan do Laravel permitem criar comandos flexíveis e poderosos que podem se adaptar a diferentes cenários de uso. Utilizando as ferramentas fornecidas pelo Artisan, você pode definir e recuperar argumentos e opções, validar entradas e personalizar o comportamento dos seus comandos, melhorando a interatividade e a funcionalidade da sua aplicação.
