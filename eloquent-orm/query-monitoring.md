# Monitoramento de Queries

Monitorar e analisar as queries executadas pelo Eloquent pode ajudar a otimizar a performance da aplicação.

## Usando o Laravel Debugbar

O Laravel Debugbar fornece uma interface visual para monitorar queries.

```php
// Exemplo de uso do Laravel Debugbar
\Debugbar::info($users);
```

## Usando o Laravel Telescope

O Laravel Telescope é uma ferramenta avançada para monitoramento de queries e outras atividades.

### Instalando Laravel Telescope

```bash
composer require laravel/telescope
php artisan telescope:install
php artisan migrate
php artisan telescope:run
```
