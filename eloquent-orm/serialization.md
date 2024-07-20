# Serialização

O Eloquent permite a serialização de modelos e coleções para JSON e arrays.

## Exemplo de Serialização

```php
$user = User::find(1);

// Para JSON
$json = $user->toJson();

// Para Array
$array = $user->toArray();
```
