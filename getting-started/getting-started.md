# Primeiros Passos com Laravel 10

Esta seção fornece uma visão geral do Laravel, como iniciar, seus recursos e o que torna o Laravel 10 especial.

## O que é Laravel?

Laravel é um framework de aplicação web com uma sintaxe expressiva e elegante. Ele visa tornar o processo de desenvolvimento uma experiência agradável para os desenvolvedores sem sacrificar a funcionalidade das aplicações. O Laravel tenta aliviar as tarefas comuns usadas na maioria dos projetos web, como:

- Sistema de Rotas
- Autenticação
- Sessões
- Cache
- E mais

## Principais Recursos do Laravel 10

O Laravel 10 introduz vários novos recursos e melhorias:

- **Melhorias no Sistema de Rotas:** Flexibilidade e desempenho aprimorados nas rotas.
- **Aprimoramentos no Middleware:** Opções de middleware mais poderosas para melhor manipulação de requisições.
- **Eloquent ORM Avançado:** Capacidades de consulta aprimoradas e melhor desempenho.
- **Blade Templating:** Novos recursos e melhorias no motor de templates Blade.
- **Melhorias no Banco de Dados:** Suporte aprimorado para operações e migrações de banco de dados.
- **Recursos de Segurança:** Medidas de segurança atualizadas para proteger suas aplicações.

## Por que usar o Laravel 10?

O Laravel 10 oferece um conjunto robusto de ferramentas e uma arquitetura de aplicação que incorpora muitos dos melhores recursos de frameworks como Ruby on Rails, ASP.NET MVC e outros. O Laravel pode ajudar você a construir qualquer tipo de aplicação web, desde pequenos sites até grandes aplicações empresariais.

## Exemplos de como iniciar um projeto

### 1. Utilizando Composer para um Novo Projeto Laravel 10

```bash
composer create-project --prefer-dist laravel/laravel laravel10app
```

---

### 2. Utilizando Docker com Laravel Sail

Laravel Sail é uma interface de linha de comando leve para interagir com o ambiente Docker padrão do Laravel. Ele é perfeito para começar rapidamente com Docker.

#### Passos para iniciar um projeto Laravel com Sail

**Criar um novo projeto Laravel:**

```bash
composer create-project --prefer-dist laravel/laravel laravel10app
```

**Mudar para o diretório do projeto:**

```bash
cd laravel10app
```

**Instalar Laravel Sail:**

```bash
composer require laravel/sail --dev
```

**Iniciar o ambiente Sail:**

```bash
./vendor/bin/sail up
```

**Acessar a aplicação:**

Abra seu navegador e acesse `http://localhost`.

---

### 3. Utilizando Docker Compose, Dockerfile e Makefile

Para configurações mais avançadas, você pode usar Docker Compose, Dockerfile e Makefile para gerenciar seu ambiente de desenvolvimento local.

#### Exemplo de docker-compose.yml

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: laravel10app
    container_name: laravel10app
    ports:
      - 8000:80
    volumes:
      - .:/var/www
    networks:
      - laravel
    environment:
      - APP_ENV=local
      - APP_DEBUG=true
      - DB_HOST=db
    depends_on:
      - db
    command: sh -c "cp .env.example .env && composer install && php artisan key:generate && npm install && npm run dev && php-fpm"

  db:
    image: mysql:8.0
    container_name: laravel10db
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/mysql
    networks:
      - laravel

networks:
  laravel:
    driver: bridge

volumes:
  dbdata:
    driver: local
```

#### Exemplo de Dockerfile

```dockerfile
FROM php:8.1-fpm

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    curl \
    unzip \
    git \
    nodejs \
    npm \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www-data:www-data . /var/www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
```

#### Exemplo de Makefile

```makefile
up:
    docker-compose up -d

down:
    docker-compose down

restart:
    docker-compose down
    docker-compose up -d

logs:
    docker-compose logs -f

build:
    docker-compose build

setup:
    docker-compose run --rm app sh -c "cp .env.example .env && composer install && php artisan key:generate && npm install && npm run dev"
```

---

### Recursos

- [Documentação Oficial do Laravel](https://laravel.com/docs/10.x)
- [Padrões PSR do PHP-FIG](https://www.php-fig.org/psr/)
- [PHP The Right Way](https://br.phptherightway.com/)
