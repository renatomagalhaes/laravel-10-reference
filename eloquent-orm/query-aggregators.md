# Agregadores no Query Builder

Utilizando funções agregadoras no Query Builder do Eloquent.

## Exemplo de Funções Agregadoras

```php
// Contar o número de usuários
$count = User::count();

// Obter a média de idade dos usuários
$averageAge = User::avg('age');
```
