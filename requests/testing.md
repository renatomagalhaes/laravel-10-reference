# Testes

Os testes são uma parte essencial do desenvolvimento de software, garantindo que o código funcione conforme esperado e que mudanças futuras não introduzam novos problemas. O Laravel fornece um framework robusto para a escrita e execução de testes.

## Tipos de Testes

### Passo 1: Testes Unitários

Os testes unitários verificam o funcionamento de pequenas partes isoladas do código, como métodos e funções.

#### Exemplo de Teste Unitário

```php
use Tests\TestCase;
use App\Models\User;

class UserTest extends TestCase
{
    public function test_user_creation()
    {
        $user = User::factory()->create();

        $this->assertInstanceOf(User::class, $user);
        $this->assertDatabaseHas('users', [
            'email' => $user->email,
        ]);
    }
}
```

### Passo 2: Testes de Integração

Os testes de integração verificam a interação entre diferentes partes do sistema, garantindo que funcionem bem juntas.

#### Exemplo de Teste de Integração

```php
use Tests\TestCase;
use App\Models\User;
use App\Models\Post;

class PostTest extends TestCase
{
    public function test_user_can_create_post()
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->post('/posts', [
            'title' => 'Novo Post',
            'content' => 'Conteúdo do post',
        ]);

        $response->assertStatus(201);
        $this->assertDatabaseHas('posts', [
            'title' => 'Novo Post',
        ]);
    }
}
```

### Passo 3: Testes de Feature

Os testes de feature (ou testes funcionais) verificam a funcionalidade do sistema como um todo, garantindo que uma feature completa funcione conforme o esperado.

#### Exemplo de Teste de Feature

```php
use Tests\TestCase;

class LoginTest extends TestCase
{
    public function test_user_can_login()
    {
        $user = User::factory()->create([
            'password' => bcrypt($password = 'password'),
        ]);

        $response = $this->post('/login', [
            'email' => $user->email,
            'password' => $password,
        ]);

        $response->assertRedirect('/home');
        $this->assertAuthenticatedAs($user);
    }
}
```

## Configuração de Ambiente de Teste

### Passo 1: Configurar o Arquivo `phpunit.xml`

No arquivo `phpunit.xml`, configure o ambiente de teste e o banco de dados.

```xml
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/9.5/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
    </testsuites>
    <php>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="QUEUE_CONNECTION" value="sync"/>
        <env name="MAIL_MAILER" value="array"/>
    </php>
</phpunit>
```

### Passo 2: Executar Testes

Você pode executar os testes utilizando o comando `php artisan test` ou `vendor/bin/phpunit`.

```bash
php artisan test
```

## Cobertura de Testes

### Passo 1: Instalar o Xdebug

Para medir a cobertura de testes, instale a extensão Xdebug no PHP.

```bash
pecl install xdebug
```

### Passo 2: Configurar o PHP para Usar o Xdebug

Adicione a configuração do Xdebug no arquivo `php.ini`.

```ini
zend_extension="xdebug.so"
xdebug.mode=coverage
```

### Passo 3: Gerar Relatório de Cobertura

Utilize o PHPUnit para gerar um relatório de cobertura de testes.

```bash
vendor/bin/phpunit --coverage-html coverage
```

## Testes de API

### Passo 1: Configurar Testes de API

Você pode usar o Laravel para testar endpoints de API utilizando o `withoutMiddleware` para desativar middleware durante os testes.

#### Exemplo de Teste de API

```php
use Tests\TestCase;

class ApiTest extends TestCase
{
    public function test_get_users()
    {
        $response = $this->withoutMiddleware()->get('/api/users');

        $response->assertStatus(200)
                 ->assertJsonStructure([
                     '*' => ['id', 'name', 'email', 'created_at', 'updated_at'],
                 ]);
    }
}
```

## Resumo

Os testes são uma parte fundamental do desenvolvimento de software, garantindo que o código funcione conforme esperado e que futuras alterações não introduzam novos problemas. O Laravel fornece ferramentas robustas para escrever e executar testes unitários, de integração e de feature, além de suporte para medir a cobertura de testes. Com uma boa prática de testes, você pode garantir a qualidade e a confiabilidade do seu software.
