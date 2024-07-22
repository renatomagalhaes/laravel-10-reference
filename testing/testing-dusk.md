# Testes de UI com Dusk

Laravel Dusk é uma ferramenta para testes automatizados de interface de usuário (UI) que permite simular a interação do usuário com a aplicação. Ele facilita a criação de testes end-to-end para garantir que a aplicação funcione conforme o esperado do ponto de vista do usuário.

## Importância dos Testes de UI

### Benefícios

- **Verificação de Funcionalidade:** Garante que a interface do usuário funcione conforme o esperado.
- **Experiência do Usuário:** Assegura que a aplicação ofereça uma experiência de usuário consistente e sem erros.
- **Automatização:** Permite a automação de testes de interface, economizando tempo e esforço manual.

## Instalação e Configuração do Dusk

### Instalação do Dusk

Instale o Dusk via Composer:

```bash
composer require --dev laravel/dusk
```

### Inicialização do Dusk

Inicialize o Dusk no seu projeto:

```bash
php artisan dusk:install
```

### Configuração do Ambiente de Teste

Adicione a configuração de ambiente para o Dusk no arquivo `.env.dusk.local`:

```ini
APP_URL=http://localhost
```

## Criando Testes de UI com Dusk

### Exemplo de Teste de Login

Crie um teste para verificar o processo de login do usuário.

#### Exemplo com PHPUnit

Crie um arquivo de teste em `tests/Browser/LoginTest.php`:

```php
namespace Tests\Browser;

use Laravel\Dusk\Browser;
use Tests\DuskTestCase;

class LoginTest extends DuskTestCase
{
    public function testUserCanLogin()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/login')
                    ->type('email', 'test@example.com')
                    ->type('password', 'password')
                    ->press('Login')
                    ->assertPathIs('/home');
        });
    }
}
```

### Executando os Testes com Dusk

Execute os testes de UI com o comando Dusk:

```bash
php artisan dusk
```

## Práticas Recomendadas

### Usar Seletores Estáveis

Use seletores de elementos estáveis, como IDs ou classes específicas, para evitar que mudanças no HTML quebrem os testes.

### Agrupar Testes de Funcionalidade

Agrupe testes de funcionalidades relacionadas para manter os testes organizados e fáceis de gerenciar.

### Executar Testes em Ambientes de Staging

Execute testes de UI em ambientes de staging que espelhem o ambiente de produção para garantir a precisão dos testes.

## Exemplo Completo

### Teste de UI com Dusk

Crie um arquivo de teste em `tests/Browser/LoginTest.php`:

```php
namespace Tests\Browser;

use Laravel\Dusk\Browser;
use Tests\DuskTestCase;

class LoginTest extends DuskTestCase
{
    public function testUserCanLogin()
    {
        $this->browse(function (Browser $browser) {
            $browser->visit('/login')
                    ->type('email', 'test@example.com')
                    ->type('password', 'password')
                    ->press('Login')
                    ->assertPathIs('/home');
        });
    }
}
```

### Executando os Testes com Dusk

Execute os testes de UI com o comando Dusk:

```bash
php artisan dusk
```

## Resumo

Laravel Dusk facilita a criação de testes automatizados de interface de usuário, garantindo que a aplicação funcione conforme o esperado do ponto de vista do usuário. Com Dusk, você pode escrever testes end-to-end eficientes que asseguram a funcionalidade e a experiência do usuário na sua aplicação Laravel.
