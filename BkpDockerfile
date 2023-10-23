# DEFINE IMAGEM BASE
FROM ubuntu:22.04

# CONFIGURA TIMEZONE
RUN ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone

# ATUALIZA PACOTES & INSTALA UTILITÁRIOS
RUN apt-get update && \
    apt-get install -y tzdata \
    nano \
    curl \
    zip \
    unzip \
    git

# LIMPA CACHE
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# DEFINE EDITOR PADRÃO
RUN export EDITOR=/usr/bin/nano

# GIT CONFIG
RUN git config --global http.sslVerify false

# COPIA CODIGO DA APLICAÇÃO PARA CONTAINER
COPY ../ /var/www/html/

# CONFIGURA VARIÁVEIS DE AMBIENTE PARA APACHE
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2/apache2.pid
# ENV APACHE_SERVER_NAME localhost

# ATUALIZA PACOTES & INSTALA APACHE
RUN apt-get update -y && \
    apt-get install -y apache2

# COPIA VIRTUAL HOST
COPY apache/sites-available/app.conf /etc/apache2/sites-available/app.conf
RUN ln -s /etc/apache2/sites-available/app.conf /etc/apache2/sites-enabled/app.conf

# INSTALA PHP7.1 & DEPÊNCIAS NECESSÁRIAS
RUN apt-get install software-properties-common -y && \
    add-apt-repository ppa:ondrej/php

RUN apt-get update && \
    apt-get install -y php7.1 \
    libapache2-mod-php7.1 \
    php7.1-cli \
    php7.1-common \
    php7.1-mbstring \
    php7.1-xml \
    php7.1-gd \
    php7.1-opcache \
    php7.1-pgsql \
    php7.1-mysql \
    php7.1-json \
    php7.1-mysql \
    php7.1-zip 

RUN a2enmod rewrite
RUN a2dissite 000-default.conf
RUN a2ensite app.conf
RUN chown -R www-data:www-data /var/www/html/
CMD ["/usr/sbin/apache2ctl", "reload"]

# LIMPA CACHE
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# INSTALA COMPOSER GLOBALMENTE
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# EXPÕE APACHE
EXPOSE 80

# EXECUTA APACHE APÓS INICIALIZAÇÃO DE CONTAINER
COPY docker-entrypoint.sh .
RUN chmod +x docker-entrypoint.sh

# DEFINE DIRETÓRIO PADRÃO DE TRABALHO
WORKDIR /var/www/html