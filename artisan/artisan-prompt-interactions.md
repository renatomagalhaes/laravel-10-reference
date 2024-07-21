# Interações via Prompt

Os comandos Artisan podem ser interativos, permitindo que os desenvolvedores façam perguntas ao usuário e recebam respostas. Este guia aborda como criar comandos interativos no Laravel, utilizando métodos de prompt para obter entradas do usuário.

## Introdução às Interações via Prompt

### O que são Interações via Prompt?

Interações via prompt permitem que um comando Artisan solicite informações do usuário durante a execução, tornando os comandos mais dinâmicos e adaptáveis às necessidades do momento.

### Benefícios das Interações via Prompt

- **Flexibilidade:** Permite ajustar o comportamento do comando com base na entrada do usuário.
- **Interatividade:** Melhora a experiência do usuário ao permitir uma interação direta com o comando.
- **Personalização:** Facilita a personalização de comandos para diferentes cenários de uso.

## Criando Comandos Interativos

### Perguntando ao Usuário

Use o método `ask` para perguntar ao usuário e obter uma resposta de texto:

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class InteractiveCommand extends Command
{
    protected $signature = 'command:interactive';
    protected $description = 'Comando interativo de exemplo';

    public function handle()
    {
        $name = $this->ask('Qual é o seu nome?');
        $this->info("Olá, {$name}!");
    }
}
```

### Confirmando Ação

Use o método `confirm` para obter uma resposta booleana do usuário:

```php
public function handle()
{
    if ($this->confirm('Você deseja continuar?')) {
        $this->info('Continuando...');
    } else {
        $this->info('Ação cancelada.');
    }
}
```

### Escolhendo entre Opções

Use o método `choice` para apresentar uma lista de opções ao usuário:

```php
public function handle()
{
    $language = $this->choice('Qual linguagem de programação você prefere?', ['PHP', 'JavaScript', 'Python'], 0);
    $this->info("Você escolheu: {$language}");
}
```

### Solicitando Informações Secretas

Use o método `secret` para solicitar informações que devem ser ocultadas, como senhas:

```php
public function handle()
{
    $password = $this->secret('Qual é a sua senha?');
    $this->info('Senha recebida.');
}
```

## Exemplos Práticos

### Criação de Usuários

Crie um comando interativo para adicionar um novo usuário ao sistema:

```php
public function handle()
{
    $name = $this->ask('Qual é o nome do usuário?');
    $email = $this->ask('Qual é o email do usuário?');
    $password = $this->secret('Qual é a senha do usuário?');

    User::create([
        'name' => $name,
        'email' => $email,
        'password' => bcrypt($password),
    ]);

    $this->info("Usuário {$name} criado com sucesso!");
}
```

### Configuração de Ambiente

Crie um comando para configurar as variáveis de ambiente do projeto:

```php
public function handle()
{
    $appName = $this->ask('Qual é o nome da aplicação?');
    $appEnv = $this->choice('Qual é o ambiente da aplicação?', ['local', 'production'], 0);
    $appDebug = $this->confirm('Habilitar debug?');

    $envFile = base_path('.env');
    file_put_contents($envFile, "APP_NAME={$appName}\nAPP_ENV={$appEnv}\nAPP_DEBUG={$appDebug}\n", FILE_APPEND);

    $this->info('Configuração de ambiente atualizada com sucesso!');
}
```

## Resumo

Comandos interativos do Artisan tornam a linha de comando do Laravel mais flexível e adaptável, permitindo que os desenvolvedores façam perguntas ao usuário e ajustem o comportamento do comando com base nas respostas. Usando métodos como `ask`, `confirm`, `choice` e `secret`, você pode criar comandos dinâmicos que melhoram a experiência do usuário e facilitam a personalização das operações.
