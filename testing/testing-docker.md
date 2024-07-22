# Testes com Docker

Docker é uma plataforma que facilita a criação, implantação e execução de aplicações em contêineres. Utilizar Docker para testes permite isolar o ambiente de testes, garantindo consistência e reprodutibilidade.

## Importância dos Testes com Docker

### Benefícios

- **Isolamento:** Garante que os testes sejam executados em um ambiente isolado, evitando interferências externas.
- **Consistência:** Permite que os testes sejam executados em qualquer ambiente com a mesma configuração.
- **Reprodutibilidade:** Facilita a reprodução de bugs e problemas, garantindo que o ambiente de testes seja sempre o mesmo.

## Configuração Básica para Testes com Docker

### Instalação do Docker

Instale o Docker seguindo as instruções oficiais do [site do Docker](https://docs.docker.com/get-docker/).

### Configuração do Docker Compose

Crie um arquivo `docker-compose.yml` para configurar os serviços necessários para a aplicação e os testes.

#### Exemplo de Arquivo docker-compose.yml

```yaml
version: '3.8'

services:
  app:
    image: php:8.0
    volumes:
      - .:/var/www
    working_dir: /var/www
    environment:
      - APP_ENV=testing
      - DB_HOST=db
      - DB_DATABASE=testing
      - DB_USERNAME=root
      - DB_PASSWORD=root
    depends_on:
      - db
    networks:
      - laravel

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=testing
    ports:
      - 3306:3306
    networks:
      - laravel

networks:
  laravel:
    driver: bridge
```

### Configuração do Dockerfile

Crie um arquivo `Dockerfile` para definir o ambiente de desenvolvimento e testes.

#### Exemplo de Dockerfile

```Dockerfile
FROM php:8.0

# Instala extensões do PHP e dependências
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpq-dev \
    && docker-php-ext-install pdo_mysql

# Instala Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www

# Copia os arquivos do projeto
COPY . .

# Instala dependências do Composer
RUN composer install --prefer-dist --no-scripts --no-autoloader

# Executa o autoload do Composer
RUN composer dump-autoload --optimize

# Executa migrations e seeds
RUN php artisan migrate --seed

# Define o comando padrão
CMD ["php-fpm"]
```

## Executando Testes com Docker

### Passo 1: Construir e Iniciar os Contêineres

Construa e inicie os contêineres com Docker Compose:

```bash
docker-compose up -d --build
```

### Passo 2: Executar os Testes

Execute os testes dentro do contêiner da aplicação:

```bash
docker-compose exec app vendor/bin/phpunit
```

## Práticas Recomendadas

### Usar Docker para Todos os Ambientes

Utilize Docker para desenvolvimento, testes e produção para garantir consistência em todos os ambientes.

### Automatizar o Processo de Construção

Automatize a construção e inicialização dos contêineres no pipeline de CI para garantir que os testes sejam executados em um ambiente consistente.

### Monitorar o Desempenho dos Contêineres

Monitore o desempenho dos contêineres durante os testes para identificar possíveis gargalos e otimizar o ambiente de testes.

## Exemplo Completo

### Arquivo docker-compose.yml

Crie o arquivo `docker-compose.yml` com o seguinte conteúdo:

```yaml
version: '3.8'

services:
  app:
    image: php:8.0
    volumes:
      - .:/var/www
    working_dir: /var/www
    environment:
      - APP_ENV=testing
      - DB_HOST=db
      - DB_DATABASE=testing
      - DB_USERNAME=root
      - DB_PASSWORD=root
    depends_on:
      - db
    networks:
      - laravel

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=testing
    ports:
      - 3306:3306
    networks:
      - laravel

networks:
  laravel:
    driver: bridge
```

### Arquivo Dockerfile

Crie o arquivo `Dockerfile` com o seguinte conteúdo:

```Dockerfile
FROM php:8.0

# Instala extensões do PHP e dependências
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpq-dev \
    && docker-php-ext-install pdo_mysql

# Instala Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www

# Copia os arquivos do projeto
COPY . .

# Instala dependências do Composer
RUN composer install --prefer-dist --no-scripts --no-autoloader

# Executa o autoload do Composer
RUN composer dump-autoload --optimize

# Executa migrations e seeds
RUN php artisan migrate --seed

# Define o comando padrão
CMD ["php-fpm"]
```

### Executando os Testes

1. Construa e inicie os contêineres:

```bash
docker-compose up -d --build
```

2. Execute os testes dentro do contêiner da aplicação:

```bash
docker-compose exec app vendor/bin/phpunit
```

## Resumo

Utilizar Docker para testes garante que o ambiente de testes seja isolado, consistente e reprodutível. Com Docker e Docker Compose, você pode configurar facilmente um ambiente de testes que simula o ambiente de produção, garantindo a qualidade e a confiabilidade da sua aplicação Laravel.
