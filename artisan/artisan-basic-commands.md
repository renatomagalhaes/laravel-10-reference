# Comandos Básicos do Artisan

O Artisan fornece uma variedade de comandos úteis para tarefas comuns de desenvolvimento e manutenção em um projeto Laravel. Este guia aborda alguns dos comandos básicos mais frequentemente usados.

## Listando Comandos Disponíveis

Para ver uma lista de todos os comandos disponíveis no Artisan, use o comando:

```bash
php artisan list
```

## Comandos Básicos

### Criando um Novo Controlador

Crie um novo controlador com o comando `make:controller`:

```bash
php artisan make:controller NomeDoControlador
```

### Criando um Novo Modelo

Crie um novo modelo com o comando `make:model`:

```bash
php artisan make:model NomeDoModelo
```

### Criando uma Nova Migração

Crie uma nova migração com o comando `make:migration`:

```bash
php artisan make:migration create_nome_da_tabela_table
```

### Executando Migrações

Execute todas as migrações pendentes com o comando `migrate`:

```bash
php artisan migrate
```

### Revertendo Migrações

Reverta a última migração executada com o comando `migrate:rollback`:

```bash
php artisan migrate:rollback
```

### Limpando o Cache da Aplicação

Limpe o cache da aplicação com o comando `cache:clear`:

```bash
php artisan cache:clear
```

### Limpando o Cache de Configuração

Limpe o cache de configuração com o comando `config:cache`:

```bash
php artisan config:cache
```

## Comandos de Desenvolvimento

### Executando o Servidor de Desenvolvimento

Inicie o servidor de desenvolvimento local com o comando `serve`:

```bash
php artisan serve
```

### Gerando Chave de Aplicação

Gere uma nova chave de aplicação com o comando `key:generate`:

```bash
php artisan key:generate
```

### Exibindo Rotas Registradas

Liste todas as rotas registradas na aplicação com o comando `route:list`:

```bash
php artisan route:list
```

## Resumo

Os comandos básicos do Artisan são ferramentas essenciais para o desenvolvimento e manutenção de aplicações Laravel. Utilizando esses comandos, você pode criar controladores, modelos, migrações e muito mais, facilitando a automação de tarefas comuns e aumentando a produtividade.
