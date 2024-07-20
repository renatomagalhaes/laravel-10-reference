# Inspeção de DB e Modelos

Ferramentas e técnicas para inspecionar e verificar o mapeamento entre o banco de dados e os modelos Eloquent.

## Visualizando Estruturas de Tabelas

Use o comando `php artisan tinker` para inspecionar modelos e dados do banco de dados.

```bash
php artisan tinker
\>>> App\Models\User::first();
```

## Ferramentas de Terceiros

Ferramentas como Laravel Debugbar e Telescope podem ajudar a inspecionar queries e interações do Eloquent com o banco de dados.

### Instalando Laravel Debugbar

```bash
composer require barryvdh/laravel-debugbar --dev
```
