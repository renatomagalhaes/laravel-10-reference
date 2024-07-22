# Testes de Componentes com Inertia Vue.js

Inertia.js é uma biblioteca que permite a construção de aplicações SPA usando frameworks do lado do servidor como Laravel. Com Inertia.js, você pode utilizar Vue.js para criar componentes dinâmicos e interativos. Testar esses componentes é crucial para garantir que a lógica de interação do usuário funcione corretamente.

## Importância dos Testes de Componentes

### Benefícios

- **Verificação de Funcionalidade:** Garante que os componentes funcionem conforme o esperado.
- **Experiência do Usuário:** Assegura que a interface ofereça uma experiência consistente e sem erros.
- **Automatização:** Permite a automação de testes de componentes, economizando tempo e esforço manual.

## Instalação e Configuração do Ambiente de Testes

### Instalação do Jest e Vue Test Utils

Jest é um framework de testes em JavaScript, enquanto Vue Test Utils fornece utilitários para testar componentes Vue.js.

```bash
npm install --save-dev jest @vue/test-utils vue-jest babel-jest
```

### Configuração do Jest

Crie um arquivo `jest.config.js` na raiz do seu projeto com a seguinte configuração:

```javascript
module.exports = {
  moduleFileExtensions: ['js', 'json', 'vue'],
  transform: {
    '^.+\\.vue$': 'vue-jest',
    '^.+\\.js$': 'babel-jest'
  },
  testMatch: [
    '**/tests/**/*.spec.js'
  ]
}
```

### Configuração do Babel

Adicione um arquivo `.babelrc` na raiz do seu projeto com a seguinte configuração:

```json
{
  "presets": ["@babel/preset-env"]
}
```

## Criando Testes de Componentes com Inertia Vue.js

### Estrutura Básica de um Teste

Crie um arquivo de teste em `tests/ExampleComponent.spec.js`:

```javascript
import { mount } from '@vue/test-utils';
import ExampleComponent from '@/Pages/ExampleComponent.vue';

describe('ExampleComponent', () => {
  test('renders correctly', () => {
    const wrapper = mount(ExampleComponent);
    expect(wrapper.text()).toContain('Example Component');
  });
});
```

### Testando Funcionalidades

Crie testes para verificar funcionalidades específicas do componente.

#### Exemplo de Teste de Formulário

Crie um arquivo de teste em `tests/FormComponent.spec.js`:

```javascript
import { mount } from '@vue/test-utils';
import FormComponent from '@/Pages/FormComponent.vue';

describe('FormComponent', () => {
  test('submits form correctly', async () => {
    const wrapper = mount(FormComponent);
    await wrapper.find('input[type="text"]').setValue('Test Value');
    await wrapper.find('form').trigger('submit.prevent');

    expect(wrapper.emitted('submit')[0][0]).toEqual({ text: 'Test Value' });
  });
});
```

## Executando Testes com Jest

Execute os testes com o Jest usando o comando:

```bash
npx jest
```

## Práticas Recomendadas

### Usar Descrições Claras

Use descrições claras e concisas nos testes para facilitar a compreensão do que está sendo testado.

### Agrupar Testes Relacionados

Agrupe testes relacionados para manter a organização e facilitar a manutenção.

### Integração com CI/CD

Integre os testes com seu pipeline de CI/CD para garantir que os testes sejam executados automaticamente em cada alteração de código.

## Exemplo Completo

### Teste Completo com Inertia Vue.js

Crie um arquivo de teste em `tests/Feature/UserComponent.spec.js`:

```javascript
import { mount } from '@vue/test-utils';
import UserComponent from '@/Pages/UserComponent.vue';

describe('UserComponent', () => {
  test('renders user data correctly', () => {
    const user = { name: 'John Doe', email: 'john@example.com' };
    const wrapper = mount(UserComponent, {
      propsData: { user }
    });

    expect(wrapper.text()).toContain('John Doe');
    expect(wrapper.text()).toContain('john@example.com');
  });

  test('emits event on button click', async () => {
    const wrapper = mount(UserComponent);
    await wrapper.find('button').trigger('click');

    expect(wrapper.emitted('user-clicked')).toBeTruthy();
  });
});
```

### Executando os Testes

Execute os testes com o comando:

```bash
npx jest
```

## Resumo

Inertia.js e Vue.js facilitam a criação de componentes dinâmicos e interativos, e testar esses componentes é crucial para garantir que eles funcionem corretamente. Com Jest e Vue Test Utils, você pode escrever testes de componentes de forma eficiente, garantindo uma experiência de usuário consistente e confiável na sua aplicação Laravel.
