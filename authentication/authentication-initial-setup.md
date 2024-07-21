# Configuração Inicial

A configuração inicial da autenticação no Laravel é um processo simples graças às ferramentas e comandos fornecidos pelo framework. Vamos explorar como configurar a autenticação básica utilizando o comando `php artisan make:auth`.

## Configurando a Autenticação

### Passo 1: Instalar o Laravel

Se você ainda não instalou o Laravel, comece instalando uma nova aplicação Laravel:

```bash
composer create-project --prefer-dist laravel/laravel nome-do-projeto
```

### Passo 2: Configurar o Banco de Dados

Configure seu banco de dados no arquivo `.env`. Por exemplo, para um banco de dados MySQL:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nome-do-banco
DB_USERNAME=usuario
DB_PASSWORD=senha
```

### Passo 3: Executar as Migrações

Laravel vem com migrações padrão para a tabela de usuários. Execute as migrações para criar as tabelas necessárias:

```bash
php artisan migrate
```

### Passo 4: Gerar Scaffolding de Autenticação

O Laravel UI é um pacote que fornece scaffolding básico de autenticação. Instale o pacote e gere a autenticação:

```bash
composer require laravel/ui
php artisan ui vue --auth
npm install && npm run dev
```

### Passo 5: Configurar o Servidor de Desenvolvimento

Inicie o servidor de desenvolvimento do Laravel:

```bash
php artisan serve
```

Agora você deve ter um sistema básico de autenticação funcionando, com páginas para login, registro, recuperação de senha, e verificação de e-mail.

### Passo 6: Configuração Adicional

Para ajustar configurações adicionais, edite o arquivo `config/auth.php`. Aqui você pode configurar guardiões, provedores e senhas.

### Exemplo de Configuração de Guardião Personalizado

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => false,
    ],
],
```

## Resumo

Configurar a autenticação básica no Laravel é um processo direto que envolve a instalação do Laravel, configuração do banco de dados, execução de migrações e geração do scaffolding de autenticação. Com esses passos, você terá uma aplicação Laravel pronta para gerenciar autenticação de usuários.
