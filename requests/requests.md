# Autenticação

O Laravel oferece um sistema robusto de autenticação, com várias ferramentas e recursos para implementar e gerenciar autenticação de usuários de forma segura e eficiente. Neste guia, cobriremos desde as configurações básicas até tópicos avançados, como controle de sessão e autenticação multitenant.

## Índice

1. [Configuração Inicial](./authentication-initial-setup.md)
2. [Registro e Login](./authentication-register-login.md)
3. [Recuperação de Senha](./authentication-password-recovery.md)
4. [Verificação de E-mail](./authentication-email-verification.md)
5. [Proteção de Rotas](./authentication-route-protection.md)
6. [Autenticação API com Laravel Sanctum](./authentication-api-sanctum.md)
7. [Autenticação via Redes Sociais](./authentication-social-auth.md)
8. [Two-Factor Authentication (2FA)](./authentication-two-factor-auth.md)
9. [Guardiões Personalizados](./authentication-custom-guards.md)
10. [Autenticação JWT (JSON Web Token)](./authentication-jwt.md)
11. [Controle de Acesso Avançado](./authentication-advanced-access-control.md)
12. [Controle de Sessão](./authentication-session-control.md)
13. [Exemplo de Multitenant](./authentication-multitenant.md)

## Resumo

### Configuração Inicial

A configuração inicial da autenticação no Laravel é simples, utilizando o comando `php artisan make:auth` para gerar as rotas, controladores e views necessários para autenticação básica. Configurar o banco de dados e executar as migrações são os passos iniciais.

### Registro e Login

O Laravel facilita a implementação de registro e login de usuários com scaffolding automático. As rotas, controladores e views gerados podem ser personalizados conforme necessário para atender aos requisitos específicos da aplicação.

### Recuperação de Senha

A recuperação de senha inclui a criação de formulários para solicitação e redefinição de senha, além do envio de e-mails de recuperação. As configurações de e-mail devem estar corretas no arquivo `.env`.

### Verificação de E-mail

A verificação de e-mail garante que os usuários forneçam um endereço de e-mail válido antes de acessar totalmente a aplicação. O middleware `verified` é usado para proteger rotas que exigem verificação de e-mail.

### Proteção de Rotas

Proteger rotas no Laravel é feito utilizando middlewares como `auth`, `verified` e `can`. Estes middlewares garantem que apenas usuários autenticados e autorizados possam acessar determinadas partes da aplicação.

### Autenticação API com Laravel Sanctum

O Laravel Sanctum oferece uma solução leve para autenticação de APIs, incluindo emissão e revogação de tokens. O middleware `auth:sanctum` é usado para proteger rotas de API.

### Autenticação via Redes Sociais

O Laravel Socialite facilita a implementação de login via redes sociais como Facebook, Google e Twitter. As credenciais das redes sociais devem ser configuradas no arquivo `config/services.php`.

### Two-Factor Authentication (2FA)

A autenticação de dois fatores (2FA) adiciona uma camada extra de segurança, exigindo um segundo fator de verificação além da senha. Pacotes como `pragmarx/google2fa-laravel` podem ser usados para implementar 2FA no Laravel.

### Guardiões Personalizados

Guardiões personalizados permitem definir diferentes métodos de autenticação para diferentes partes da aplicação. Eles são configurados no arquivo `config/auth.php` e podem ser usados para criar guardiões para administradores, por exemplo.

### Autenticação JWT (JSON Web Token)

A autenticação JWT é uma abordagem comum para autenticar APIs, proporcionando uma maneira segura de transmitir informações entre cliente e servidor. O pacote `tymon/jwt-auth` é usado para implementar JWT no Laravel.

### Controle de Acesso Avançado

Gates e Policies oferecem um sistema flexível para controle de acesso avançado, permitindo definir regras de autorização complexas. Gates são closures simples, enquanto Policies são classes que organizam a lógica de autorização em torno de um modelo específico.

### Controle de Sessão

O controle de sessão permite que os usuários visualizem e gerenciem suas sessões de login em diferentes dispositivos, melhorando a segurança ao permitir que desconectem dispositivos não reconhecidos ou saiam de todas as sessões ativas.

### Exemplo de Multitenant

A arquitetura multitenant permite que uma única aplicação sirva a múltiplos clientes, cada um com seus próprios dados e configurações. Isso pode ser feito utilizando colunas de `tenant_id` nas tabelas ou bancos de dados separados.
