# Configuração de Testes de Cobertura (Coverage)

Os testes de cobertura medem a quantidade de código que é coberto pelos testes automatizados. Eles ajudam a identificar áreas do código que não foram testadas, garantindo uma cobertura abrangente e aumentando a confiabilidade do software.

## Importância dos Testes de Cobertura

### Benefícios

- **Confiança no Código:** Garante que o maior número possível de caminhos do código seja testado.
- **Identificação de Lacunas:** Ajuda a identificar partes do código que não foram testadas.
- **Qualidade do Software:** Aumenta a qualidade e a confiabilidade do software ao garantir uma cobertura abrangente.

## Ferramentas para Testes de Cobertura

### PHPUnit

PHPUnit tem suporte embutido para geração de relatórios de cobertura de código.

### Pest

Pest, sendo construído sobre PHPUnit, também suporta a geração de relatórios de cobertura de código.

### Xdebug

Xdebug é uma extensão do PHP que ajuda na geração de relatórios de cobertura de código.

## Configuração Básica para Testes de Cobertura

### Instalação do Xdebug

Certifique-se de que o Xdebug está instalado e configurado no seu ambiente de desenvolvimento.

### Configuração do PHPUnit para Cobertura de Código

Adicione a configuração de cobertura de código no arquivo `phpunit.xml`:

```xml
<phpunit bootstrap="vendor/autoload.php"
         colors="true"
         verbose="true">
    <logging>
        <log type="coverage-html" target="tests/coverage-report" />
    </logging>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
</phpunit>
```

### Geração de Relatórios de Cobertura com PHPUnit

Execute os testes com cobertura de código:

```bash
vendor/bin/phpunit --coverage-html tests/coverage-report
```

### Configuração do Pest para Cobertura de Código

Adicione a configuração de cobertura de código no arquivo `phpunit.xml` para Pest:

```xml
<phpunit bootstrap="vendor/autoload.php"
         colors="true"
         verbose="true">
    <logging>
        <log type="coverage-html" target="tests/coverage-report" />
    </logging>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
</phpunit>
```

### Geração de Relatórios de Cobertura com Pest

Execute os testes com cobertura de código:

```bash
./vendor/bin/pest --coverage --min=80
```

## Integração com IDEs

### PHPStorm

Configure o PHPStorm para utilizar a cobertura de código:

1. Abra `Settings/Preferences`.
2. Vá para `Languages & Frameworks > PHP > PHPUnit`.
3. Configure o PHPUnit para usar o `phpunit.xml` do seu projeto.
4. Vá para `Run/Debug Configurations`.
5. Crie uma nova configuração de PHPUnit e marque a opção `Generate code coverage report`.

### VSCode

Configure o VSCode para utilizar a cobertura de código com a extensão PHPUnit:

1. Instale a extensão `PHPUnit`.
2. Configure o PHPUnit no arquivo de configurações do workspace:

```json
{
    "phpunit.phpunit": "./vendor/bin/phpunit",
    "phpunit.args": ["--configuration", "phpunit.xml", "--coverage-html", "tests/coverage-report"]
}
```

## Práticas Recomendadas

### Cobertura de Código Mínima

Defina uma meta mínima de cobertura de código para garantir que a maioria do código seja testada. Por exemplo, 80%.

### Relatórios de Cobertura

Gere e revise regularmente os relatórios de cobertura para identificar áreas que precisam de mais testes.

### Automação

Automatize a geração de relatórios de cobertura no pipeline de CI para garantir que a cobertura seja verificada em cada alteração de código.

## Exemplo Completo

### Configuração do PHPUnit

Adicione a configuração de cobertura de código no arquivo `phpunit.xml`:

```xml
<phpunit bootstrap="vendor/autoload.php"
         colors="true"
         verbose="true">
    <logging>
        <log type="coverage-html" target="tests/coverage-report" />
    </logging>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./app</directory>
        </include>
    </coverage>
</phpunit>
```

### Execução dos Testes com Cobertura

Execute os testes com cobertura de código:

```bash
vendor/bin/phpunit --coverage-html tests/coverage-report
```

### Exibição dos Relatórios

Abra o relatório gerado em `tests/coverage-report/index.html` para visualizar a cobertura de código.

## Resumo

Os testes de cobertura são essenciais para garantir que o maior número possível de caminhos do código seja testado. Usando ferramentas como PHPUnit, Pest e Xdebug, você pode gerar relatórios de cobertura de código e integrar esses relatórios com IDEs como PHPStorm e VSCode, garantindo a qualidade e a confiabilidade da sua aplicação Laravel.
