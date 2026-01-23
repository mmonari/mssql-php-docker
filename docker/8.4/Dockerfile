# ------------------------------------------------------
# Base stage: system, PHP, Node, ODBC, build tooling
# ------------------------------------------------------
FROM ubuntu:24.04 AS base

LABEL maintainer="Taylor Otwell"

ARG WWWGROUP
ARG NODE_VERSION=22
ARG MYSQL_CLIENT="mysql-client"
ARG POSTGRES_VERSION=17

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

WORKDIR /var/www/html

# Timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# ------------------------------------------------------
# Core system deps + repositories
# ------------------------------------------------------
RUN apt-get update && apt-get upgrade -y \
 && apt-get install -y \
    ca-certificates curl gnupg gosu git unzip zip supervisor \
    libcap2-bin libpng-dev python3 dnsutils librsvg2-bin \
    sqlite3 ffmpeg nano wget fswatch \
 \
 && mkdir -p /etc/apt/keyrings

# Ondrej PHP PPA (Ubuntu Noble – correct way)
RUN apt-get update \
 && apt-get install -y software-properties-common \
 && add-apt-repository ppa:ondrej/php \
 && apt-get update


# NodeSource
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key \
 | gpg --dearmor > /etc/apt/keyrings/nodesource.gpg \
 && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_${NODE_VERSION}.x nodistro main" \
 > /etc/apt/sources.list.d/nodesource.list

# PostgreSQL
RUN curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc \
 | gpg --dearmor > /etc/apt/keyrings/pgdg.gpg \
 && echo "deb [signed-by=/etc/apt/keyrings/pgdg.gpg] http://apt.postgresql.org/pub/repos/apt noble-pgdg main" \
 > /etc/apt/sources.list.d/pgdg.list

# Microsoft ODBC Driver 18
RUN curl -fsSL https://packages.microsoft.com/keys/microsoft.asc \
 | gpg --dearmor > /etc/apt/keyrings/microsoft.gpg \
 && echo "deb [signed-by=/etc/apt/keyrings/microsoft.gpg] https://packages.microsoft.com/ubuntu/24.04/prod noble main" \
 > /etc/apt/sources.list.d/microsoft-prod.list

# ------------------------------------------------------
# Install PHP, Node, DB clients, ODBC
# ------------------------------------------------------
RUN apt-get update \
 && ACCEPT_EULA=Y apt-get install -y \
    php8.4-cli php8.4-dev php8.4-pgsql php8.4-sqlite3 php8.4-gd php8.4-curl \
    php8.4-mysql php8.4-mbstring php8.4-xml php8.4-zip php8.4-bcmath \
    php8.4-intl php8.4-readline php8.4-ldap php8.4-redis php8.4-xdebug \
 \
    nodejs \
    ${MYSQL_CLIENT} \
    postgresql-client-${POSTGRES_VERSION} \
 \
    unixodbc unixodbc-dev msodbcsql18 mssql-tools18 g++ \
 \
 && curl -sLS https://getcomposer.org/installer \
    | php -- --install-dir=/usr/bin --filename=composer \
 \
 # Corepack for pnpm (NO yarn)
 && corepack enable \
 \
 # Bun only (pnpm already provided by Corepack)
 && npm install -g bun \
 \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Allow PHP to bind to port 80
RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.4

# ------------------------------------------------------
# SQL Server PHP extensions
# ------------------------------------------------------
RUN pecl channel-update pecl.php.net \
 && pecl install sqlsrv pdo_sqlsrv \
 && echo "extension=sqlsrv.so" > /etc/php/8.4/mods-available/sqlsrv.ini \
 && echo "extension=pdo_sqlsrv.so" > /etc/php/8.4/mods-available/pdo_sqlsrv.ini \
 && phpenmod sqlsrv pdo_sqlsrv

# ------------------------------------------------------
# Final stage (runtime)
# ------------------------------------------------------
FROM base AS final

ARG WWWGROUP

ENV SUPERVISOR_PHP_COMMAND="/usr/bin/php -d variables_order=EGPCS /var/www/html/artisan serve --host=0.0.0.0 --port=80"
ENV SUPERVISOR_PHP_USER="sail"

# Sail user
RUN userdel -r ubuntu || true \
 && groupadd --force -g ${WWWGROUP} sail \
 && useradd -ms /bin/bash --no-user-group -g ${WWWGROUP} -u 1337 sail

# Startup
COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /etc/php/8.4/cli/conf.d/99-sail.ini

RUN chmod +x /usr/local/bin/start-container

EXPOSE 80

ENTRYPOINT ["start-container"]
