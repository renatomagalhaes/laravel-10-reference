# Mocking

O mocking é uma técnica usada em testes para simular o comportamento de objetos e verificar interações entre diferentes partes do código. Isso é especialmente útil quando se deseja isolar a unidade de código que está sendo testada, evitando dependências externas como bancos de dados ou APIs externas.

## Importância do Mocking

### Benefícios

- **Isolamento:** Permite testar unidades de código de forma isolada, sem depender de outros componentes ou serviços.
- **Velocidade:** Reduz o tempo de execução dos testes, já que as dependências externas são simuladas.
- **Confiabilidade:** Aumenta a confiabilidade dos testes ao eliminar variáveis externas imprevisíveis.

## Ferramentas de Mocking no Laravel

### PHPUnit Mock Builder

O PHPUnit inclui um builder de mocks que permite criar mocks e definir seus comportamentos de forma detalhada.

### Mockery

O Mockery é uma biblioteca de mocking que fornece uma sintaxe mais fluida e expressiva. Ele é integrado ao Laravel e pode ser usado em conjunto com o PHPUnit.

## Criando Mocks com PHPUnit

### Exemplo Básico

Crie um mock de uma classe `Mailer`:

```php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use App\Services\Mailer;

class MailerTest extends TestCase
{
    public function testSendEmail()
    {
        $mailer = $this->createMock(Mailer::class);

        $mailer->method('send')
               ->willReturn(true);

        $this->assertTrue($mailer->send('test@example.com', 'Test Subject', 'Test Message'));
    }
}
```

### Exemplo Avançado

Crie um mock com expectativas:

```php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;
use App\Services\Mailer;

class MailerTest extends TestCase
{
    public function testSendEmail()
    {
        $mailer = $this->createMock(Mailer::class);

        $mailer->expects($this->once())
               ->method('send')
               ->with(
                   $this->equalTo('test@example.com'),
                   $this->equalTo('Test Subject'),
                   $this->equalTo('Test Message')
               )
               ->willReturn(true);

        $this->assertTrue($mailer->send('test@example.com', 'Test Subject', 'Test Message'));
    }
}
```

## Criando Mocks com Mockery

### Exemplo Básico

Crie um mock de uma classe `Mailer` usando Mockery:

```php
namespace Tests\Unit;

use Mockery;
use PHPUnit\Framework\TestCase;
use App\Services\Mailer;

class MailerTest extends TestCase
{
    public function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    public function testSendEmail()
    {
        $mailer = Mockery::mock(Mailer::class);

        $mailer->shouldReceive('send')
               ->once()
               ->with('test@example.com', 'Test Subject', 'Test Message')
               ->andReturn(true);

        $this->assertTrue($mailer->send('test@example.com', 'Test Subject', 'Test Message'));
    }
}
```

### Exemplo Avançado

Crie um mock com múltiplas expectativas:

```php
namespace Tests\Unit;

use Mockery;
use PHPUnit\Framework\TestCase;
use App\Services\Mailer;

class MailerTest extends TestCase
{
    public function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    public function testSendEmail()
    {
        $mailer = Mockery::mock(Mailer::class);

        $mailer->shouldReceive('send')
               ->once()
               ->with('test@example.com', 'Test Subject', 'Test Message')
               ->andReturn(true);

        $mailer->shouldReceive('queue')
               ->once()
               ->with('test@example.com', 'Test Subject', 'Test Message')
               ->andReturn(true);

        $this->assertTrue($mailer->send('test@example.com', 'Test Subject', 'Test Message'));
        $this->assertTrue($mailer->queue('test@example.com', 'Test Subject', 'Test Message'));
    }
}
```

## Testando Interações com Mocks

### Exemplo de Interações

Crie um teste que verifica interações entre objetos:

```php
namespace Tests\Unit;

use Mockery;
use PHPUnit\Framework\TestCase;
use App\Services\OrderProcessor;
use App\Services\Mailer;
use App\Models\Order;

class OrderProcessorTest extends TestCase
{
    public function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    public function testProcessOrder()
    {
        $mailer = Mockery::mock(Mailer::class);
        $mailer->shouldReceive('send')
               ->once()
               ->with('customer@example.com', 'Order Confirmation', 'Your order has been processed.')
               ->andReturn(true);

        $order = Mockery::mock(Order::class);
        $order->shouldReceive('complete')
              ->once()
              ->andReturn(true);

        $processor = new OrderProcessor($mailer);
        $this->assertTrue($processor->process($order, 'customer@example.com'));
    }
}
```

## Práticas Recomendadas

### Usar Mocks para Isolamento

Use mocks para isolar a unidade de código que está sendo testada, garantindo que o teste seja focado e preciso.

### Definir Expectativas Claras

Defina expectativas claras sobre como os mocks devem se comportar durante o teste. Isso inclui chamadas de métodos, argumentos esperados e valores de retorno.

### Limpar Mocks Após o Teste

Certifique-se de limpar os mocks após cada teste para evitar interferências entre testes. Isso pode ser feito usando os métodos `tearDown` ou `tearDownAfterClass`.

## Exemplo Completo

### Classe Mailer

```php
namespace App\Services;

class Mailer
{
    public function send($to, $subject, $message)
    {
        // Lógica de envio de email
    }

    public function queue($to, $subject, $message)
    {
        // Lógica de enfileiramento de email
    }
}
```

### Teste de Mocking

No arquivo `tests/Unit/MailerTest.php`, escreva o teste de mocking:

```php
namespace Tests\Unit;

use Mockery;
use PHPUnit\Framework\TestCase;
use App\Services\Mailer;

class MailerTest extends TestCase
{
    public function tearDown(): void
    {
        Mockery::close();
        parent::tearDown();
    }

    public function testSendEmail()
    {
        $mailer = Mockery::mock(Mailer::class);

        $mailer->shouldReceive('send')
               ->once()
               ->with('test@example.com', 'Test Subject', 'Test Message')
               ->andReturn(true);

        $this->assertTrue($mailer->send('test@example.com', 'Test Subject', 'Test Message'));
    }
}
```

## Resumo

O mocking é uma técnica poderosa para testar unidades de código de forma isolada, simulando dependências externas. Com PHPUnit e Mockery, você pode criar mocks e definir seus comportamentos de forma detalhada, garantindo que seus testes sejam rápidos, confiáveis e focados. 
