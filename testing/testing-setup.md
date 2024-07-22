# Configuração Inicial

Antes de começar a escrever testes, é importante configurar corretamente o ambiente de testes. O Laravel fornece todas as ferramentas necessárias para configurar e executar testes com facilidade.

## Configurando o Ambiente de Testes

### Passo 1: Instalação de Dependências

Certifique-se de que as dependências necessárias para testes estão instaladas. O PHPUnit já vem incluído com o Laravel, mas se você quiser usar o Pest, pode instalá-lo conforme as instruções abaixo.

#### Instalação do PHPUnit

O PHPUnit é instalado automaticamente com o Laravel. Para verificar se o PHPUnit está instalado, execute:

```bash
vendor/bin/phpunit --version
```

#### Instalação do Pest

Para instalar o Pest, execute o seguinte comando:

```bash
composer require pestphp/pest --dev
```

Em seguida, inicialize o Pest:

```bash
php artisan pest:install
```

### Passo 2: Configuração do PHPUnit

O Laravel vem com um arquivo de configuração do PHPUnit (`phpunit.xml`) na raiz do projeto. Você pode personalizar este arquivo conforme suas necessidades.

#### Exemplo de Configuração Básica

```xml
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="vendor/phpunit/phpunit/phpunit.xsd" bootstrap="bootstrap/autoload.php">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
</phpunit>
```

### Passo 3: Configuração do Pest

O Pest não requer configuração adicional além da instalação, mas você pode personalizar suas configurações no arquivo `pest.php` que é criado durante a instalação.

### Passo 4: Banco de Dados de Testes

Para testes que interagem com o banco de dados, é recomendável configurar um banco de dados separado. No arquivo `.env.testing`, configure as variáveis de ambiente do banco de dados:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=test_database
DB_USERNAME=root
DB_PASSWORD=password
```

### Passo 5: Migrar o Banco de Dados de Testes

Antes de executar testes que dependem do banco de dados, certifique-se de migrar o banco de dados de testes:

```bash
php artisan migrate --env=testing
```

### Passo 6: Executando Testes

#### Executando Testes com PHPUnit

Para executar todos os testes usando o PHPUnit, execute:

```bash
vendor/bin/phpunit
```

#### Executando Testes com Pest

Para executar todos os testes usando o Pest, execute:

```bash
./vendor/bin/pest
```

## Ferramentas Adicionais

### Laravel Dusk

O Laravel Dusk é usado para testes de browser automatizados. Para instalar o Laravel Dusk, execute:

```bash
composer require --dev laravel/dusk
php artisan dusk:install
```

### Xdebug

O Xdebug é uma ferramenta para depuração e análise de cobertura de código. Para instalar o Xdebug, siga as instruções de instalação do Xdebug para o seu sistema operacional. Em seguida, configure o `php.ini` para ativar o Xdebug:

```ini
zend_extension=xdebug.so
xdebug.mode=coverage
```

## Exemplo de Teste de Configuração

### Teste Unitário Simples

Crie um teste unitário simples em `tests/Unit/ExampleTest.php`:

```php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function testBasicTest()
    {
        $this->assertTrue(true);
    }
}
```

### Teste de Feature Simples

Crie um teste de feature simples em `tests/Feature/ExampleTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ExampleTest extends TestCase
{
    use RefreshDatabase;

    public function testHomePage()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

## Resumo

Configurar o ambiente de testes é um passo crucial para garantir que seus testes sejam executados de maneira eficiente e confiável. Com a configuração adequada, você pode escrever testes unitários, de feature e de integração que ajudem a manter a qualidade do seu código.
