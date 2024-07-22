# Configuração de Git Hooks para Testes

Git hooks são scripts que são executados em eventos específicos do Git, como commits, push e merges. Configurar hooks para rodar testes automaticamente ajuda a garantir que o código enviado para o repositório esteja sempre em um estado funcional.

## Importância dos Git Hooks

### Benefícios

- **Automatização:** Automatiza a execução de testes e outras verificações em eventos do Git.
- **Qualidade do Código:** Garante que o código esteja sempre em um estado funcional antes de ser enviado para o repositório.
- **Prevenção de Erros:** Identifica erros e problemas antes que eles sejam integrados ao código principal.

## Configuração Básica para Git Hooks

### Diretório de Hooks

Os hooks do Git são armazenados no diretório `.git/hooks`. Para tornar os hooks compartilháveis, crie um diretório `hooks` na raiz do seu projeto e configure o Git para usar este diretório.

### Configuração do Diretório de Hooks

Configure o Git para usar o diretório `hooks`:

```bash
git config core.hooksPath hooks
```

### Criando Hooks

Crie um arquivo chamado `pre-commit` no diretório `hooks` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o commit
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Commit aborted."
  exit 1
fi
```

### Tornar o Hook Executável

Torne o hook executável:

```bash
chmod +x hooks/pre-commit
```

## Exemplos de Git Hooks

### Hook de Pre-Commit

O hook de pre-commit é executado antes de um commit ser criado. Use este hook para rodar testes e verificações de qualidade de código.

#### Exemplo de Hook de Pre-Commit

Crie um arquivo `hooks/pre-commit` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o commit
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Commit aborted."
  exit 1
fi
```

### Hook de Pre-Push

O hook de pre-push é executado antes de um push ser realizado. Use este hook para rodar testes e verificações adicionais.

#### Exemplo de Hook de Pre-Push

Crie um arquivo `hooks/pre-push` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o push
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Push aborted."
  exit 1
fi
```

### Hook de Pre-Merge

O hook de pre-merge é executado antes de um merge ser realizado. Use este hook para garantir que o código esteja em um estado funcional antes de ser integrado.

#### Exemplo de Hook de Pre-Merge

Crie um arquivo `hooks/pre-merge` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o merge
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Merge aborted."
  exit 1
fi
```

## Práticas Recomendadas

### Automação de Testes

Automatize a execução de testes em eventos importantes do Git para garantir a qualidade do código.

### Integração com CI/CD

Integre os hooks com seu pipeline de CI/CD para garantir que as verificações sejam executadas tanto localmente quanto no servidor de CI.

### Feedback Imediato

Forneça feedback imediato para os desenvolvedores quando os testes falharem, permitindo que corrijam os problemas antes de enviar o código.

## Exemplo Completo

### Hook de Pre-Commit

Crie o arquivo `hooks/pre-commit` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o commit
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Commit aborted."
  exit 1
fi
```

### Hook de Pre-Push

Crie o arquivo `hooks/pre-push` com o seguinte conteúdo:

```bash
#!/bin/sh

# Executa os testes antes de permitir o push
./vendor/bin/phpunit

# Verifica se os testes passaram
if [ $? -ne 0 ]; then
  echo "Tests failed. Push aborted."
  exit 1
fi
```

### Configuração do Diretório de Hooks

Configure o Git para usar o diretório `hooks`:

```bash
git config core.hooksPath hooks
```

### Tornar os Hooks Executáveis

Torne os hooks executáveis:

```bash
chmod +x hooks/pre-commit
chmod +x hooks/pre-push
```

## Resumo

Os Git hooks são uma maneira eficaz de automatizar a execução de testes e outras verificações em eventos específicos do Git. Configurando hooks como pre-commit e pre-push, você pode garantir que o código esteja sempre em um estado funcional antes de ser enviado para o repositório, melhorando a qualidade e a confiabilidade da sua aplicação Laravel.
