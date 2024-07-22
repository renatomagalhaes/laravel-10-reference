# Configuração de Debug na IDE com Xdebug

Xdebug é uma extensão PHP que oferece poderosas ferramentas de debug, permitindo que os desenvolvedores inspecionem e depurem seu código de forma eficiente. Configurar o Xdebug na sua IDE pode aumentar significativamente sua produtividade ao resolver problemas de código.

## Importância do Debug com Xdebug

### Benefícios

- **Inspeção de Código:** Permite inspecionar variáveis e a execução do código passo a passo.
- **Resolução de Problemas:** Facilita a identificação e correção de erros no código.
- **Produtividade:** Aumenta a eficiência do desenvolvimento ao fornecer ferramentas avançadas de depuração.

## Instalação do Xdebug

### Instalação via PECL

Instale o Xdebug via PECL:

```bash
pecl install xdebug
```

### Configuração do PHP

Adicione as configurações do Xdebug ao arquivo `php.ini`:

```ini
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=127.0.0.1
xdebug.client_port=9003
xdebug.log=/tmp/xdebug.log
```

## Configuração do Debug na IDE

### PHPStorm

#### Configuração do PHPStorm

1. Abra `Settings/Preferences`.
2. Vá para `Languages & Frameworks > PHP > Debug`.
3. Configure o Debugger Port para `9003`.
4. Vá para `Languages & Frameworks > PHP > Servers`.
5. Adicione um novo servidor com o nome `localhost`, configure a URL para `http://localhost` e marque a opção `Use path mappings`.
6. Mapear o diretório do projeto local para o diretório no servidor.

#### Iniciar Sessão de Debug

1. Defina um breakpoint no código.
2. Clique em `Run > Start Listening for PHP Debug Connections`.
3. Acesse a URL no navegador para iniciar a sessão de debug.

### VSCode

#### Extensão PHP Debug

Instale a extensão `PHP Debug` no VSCode.

#### Configuração do VSCode

Adicione a configuração de lançamento ao arquivo `launch.json`:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003
        }
    ]
}
```

#### Iniciar Sessão de Debug

1. Defina um breakpoint no código.
2. Inicie a sessão de debug clicando em `Run > Start Debugging` ou pressionando `F5`.
3. Acesse a URL no navegador para iniciar a sessão de debug.

## Práticas Recomendadas

### Usar Breakpoints

Defina breakpoints estratégicos para pausar a execução do código e inspecionar o estado das variáveis.

### Inspecionar Variáveis

Use a ferramenta de inspeção de variáveis da IDE para verificar os valores e estados das variáveis durante a execução.

### Step-by-Step Debugging

Use os controles de step-into, step-over e step-out para executar o código passo a passo e entender o fluxo de execução.

## Exemplo Completo

### Configuração do Xdebug

Adicione as configurações do Xdebug ao arquivo `php.ini`:

```ini
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=127.0.0.1
xdebug.client_port=9003
xdebug.log=/tmp/xdebug.log
```

### Configuração do PHPStorm

1. Abra `Settings/Preferences`.
2. Vá para `Languages & Frameworks > PHP > Debug`.
3. Configure o Debugger Port para `9003`.
4. Vá para `Languages & Frameworks > PHP > Servers`.
5. Adicione um novo servidor com o nome `localhost`, configure a URL para `http://localhost` e marque a opção `Use path mappings`.
6. Mapear o diretório do projeto local para o diretório no servidor.

### Configuração do VSCode

Adicione a configuração de lançamento ao arquivo `launch.json`:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003
        }
    ]
}
```

### Iniciar Sessão de Debug no PHPStorm

1. Defina um breakpoint no código.
2. Clique em `Run > Start Listening for PHP Debug Connections`.
3. Acesse a URL no navegador para iniciar a sessão de debug.

### Iniciar Sessão de Debug no VSCode

1. Defina um breakpoint no código.
2. Inicie a sessão de debug clicando em `Run > Start Debugging` ou pressionando `F5`.
3. Acesse a URL no navegador para iniciar a sessão de debug.

## Resumo

Configurar o Xdebug na sua IDE pode aumentar significativamente sua produtividade ao resolver problemas de código. Usando ferramentas avançadas de depuração, como breakpoints e inspeção de variáveis, você pode identificar e corrigir erros de forma eficiente, garantindo a qualidade e a confiabilidade da sua aplicação Laravel.
