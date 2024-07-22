# Testes com API com Postman

Postman é uma ferramenta poderosa para testar APIs, permitindo a criação de requisições, validação de respostas e automação de testes. Ele facilita a verificação de que as APIs funcionem conforme o esperado, garantindo a integridade e a confiabilidade dos serviços.

## Importância dos Testes de API

### Benefícios

- **Verificação de Funcionalidade:** Garante que as APIs respondam corretamente às requisições.
- **Automatização:** Permite a automação de testes de API, economizando tempo e esforço manual.
- **Integração Contínua:** Facilita a integração dos testes de API com pipelines de CI/CD.

## Instalação e Configuração do Postman

### Download e Instalação

Baixe e instale o Postman a partir do site oficial: [Postman Download](https://www.postman.com/downloads/).

### Configuração Básica

1. Abra o Postman e crie uma nova coleção para agrupar seus testes de API.
2. Adicione um novo request para testar um endpoint específico.

## Criando Testes de API com Postman

### Exemplo de Teste de Endpoint

Crie um teste para verificar um endpoint de API.

#### Configuração do Request

1. Adicione um novo request à sua coleção.
2. Configure o método HTTP (GET, POST, etc.) e a URL do endpoint.
3. Adicione os parâmetros necessários (headers, query params, body, etc.).

#### Adicionando Testes ao Request

Adicione testes ao request para validar a resposta.

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response time is less than 200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});

pm.test("Response has valid JSON structure", function () {
    pm.response.to.be.json;
    pm.response.to.have.jsonBody({
        success: true
    });
});
```

### Executando Testes de API

Execute os testes manualmente no Postman ou utilize a Collection Runner para executar múltiplos testes de uma vez.

## Automatizando Testes de API com Newman

### Instalação do Newman

Newman é uma ferramenta de linha de comando para executar coleções de testes do Postman.

```bash
npm install -g newman
```

### Executando Testes com Newman

Exporte sua coleção do Postman e execute os testes com Newman:

```bash
newman run path/to/your_postman_collection.json
```

## Integração com CI/CD

### GitHub Actions

Configure um workflow do GitHub Actions para executar os testes de API com Newman.

```yaml
name: API Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Install Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'

      - name: Install Newman
        run: npm install -g newman

      - name: Run API Tests
        run: newman run path/to/your_postman_collection.json
```

### GitLab CI/CD

Configure um pipeline do GitLab CI/CD para executar os testes de API com Newman.

```yaml
stages:
  - test

api_tests:
  stage: test
  image: node:14
  script:
    - npm install -g newman
    - newman run path/to/your_postman_collection.json
```

## Práticas Recomendadas

### Testar Todos os Endpoints

Garanta que todos os endpoints da API sejam cobertos pelos testes.

### Validar Estrutura de Resposta

Valide a estrutura da resposta para garantir que os dados retornados estejam no formato esperado.

### Monitorar Desempenho

Monitore o desempenho da API, verificando tempos de resposta e identificando possíveis gargalos.

## Exemplo Completo

### Teste de Endpoint com Postman

1. Crie um novo request para o endpoint de login.
2. Configure o método POST e a URL do endpoint.
3. Adicione os parâmetros de corpo da requisição (email e senha).
4. Adicione os seguintes testes ao request:

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response time is less than 200ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(200);
});

pm.test("Response has valid JSON structure", function () {
    pm.response.to.be.json;
    pm.response.to.have.jsonBody({
        success: true
    });
});
```

### Executando os Testes com Newman

1. Exporte a coleção do Postman.
2. Execute os testes com o seguinte comando:

```bash
newman run path/to/your_postman_collection.json
```

## Resumo

Postman e Newman facilitam a criação e automação de testes de API, garantindo que suas APIs funcionem conforme o esperado e oferecendo uma integração contínua com pipelines de CI/CD. Usando essas ferramentas, você pode escrever e executar testes de API eficientes, garantindo a integridade e a confiabilidade dos seus serviços.
