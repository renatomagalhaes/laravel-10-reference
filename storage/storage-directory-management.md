# Gerenciamento de Diretórios

O gerenciamento de diretórios é uma funcionalidade essencial para organizar e manipular arquivos em uma aplicação web. O Laravel fornece uma API poderosa e flexível para criar, listar e excluir diretórios, facilitando o gerenciamento de estruturas de arquivos complexas.

## Criar Diretório

Você pode criar novos diretórios utilizando o método `makeDirectory` da API de armazenamento do Laravel.

### Exemplo de Criação de Diretório

```php
use Illuminate\Support\Facades\Storage;

Storage::makeDirectory('new_directory');
```

## Listar Diretórios

Para listar os diretórios dentro de um diretório específico, você pode usar o método `directories`.

### Exemplo de Listagem de Diretórios

```php
use Illuminate\Support\Facades\Storage;

$directories = Storage::directories('path/to/directory');
```

## Listar Arquivos

Para listar os arquivos dentro de um diretório específico, utilize o método `files`.

### Exemplo de Listagem de Arquivos

```php
use Illuminate\Support\Facades\Storage;

$files = Storage::files('path/to/directory');
```

## Listar Todos os Arquivos e Diretórios

Para listar todos os arquivos e diretórios dentro de um diretório específico, incluindo subdiretórios, use o método `allFiles`.

### Exemplo de Listagem de Todos os Arquivos e Diretórios

```php
use Illuminate\Support\Facades\Storage;

$allFiles = Storage::allFiles('path/to/directory');
```

## Excluir Diretório

Para excluir um diretório e todo o seu conteúdo, utilize o método `deleteDirectory`.

### Exemplo de Exclusão de Diretório

```php
use Illuminate\Support\Facades\Storage;

Storage::deleteDirectory('path/to/directory');
```

## Mover e Renomear Diretórios

Para mover ou renomear um diretório, você pode utilizar o método `move`.

### Exemplo de Mover e Renomear Diretório

```php
use Illuminate\Support\Facades\Storage;

Storage::move('path/to/old_directory', 'path/to/new_directory');
```

## Verificar Existência de Diretório

Para verificar se um diretório existe, utilize o método `exists`.

### Exemplo de Verificação de Existência de Diretório

```php
use Illuminate\Support\Facades\Storage;

if (Storage::exists('path/to/directory')) {
    // O diretório existe
}
```

## Copiar Diretório

O Laravel não fornece um método direto para copiar diretórios, mas você pode implementar isso listando todos os arquivos do diretório original e copiando-os para o novo diretório.

### Exemplo de Cópia de Diretório

```php
use Illuminate\Support\Facades\Storage;

$files = Storage::allFiles('path/to/old_directory');

foreach ($files as $file) {
    $newPath = str_replace('path/to/old_directory', 'path/to/new_directory', $file);
    Storage::copy($file, $newPath);
}
```

## Resumo

O gerenciamento de diretórios no Laravel é simplificado pela poderosa API de armazenamento do framework, que permite criar, listar, mover, renomear e excluir diretórios com facilidade. Essas operações são essenciais para organizar e manipular arquivos em uma aplicação web, garantindo que a estrutura de arquivos seja eficiente e bem gerenciada.
