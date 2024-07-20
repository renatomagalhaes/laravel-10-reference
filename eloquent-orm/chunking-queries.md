# Consulta com Chunking

Utilizando chunking para processar grandes conjuntos de dados em partes menores.

## Exemplo de Chunking

```php
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // Processar cada usuário
    }
});
```
