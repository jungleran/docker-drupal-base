FROM drupal:8.5.1-apache

MAINTAINER Jungle Ran <jungle.r@intergreat.com>

# Set apt source mirror.
RUN echo "deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib" > /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/debian/ stretch-proposed-updates main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/debian/ stretch-proposed-updates main non-free contrib" >> /etc/apt/sources.list

# Basic tools.
RUN apt-get update; apt-get install wget git vim -y

# Composer with mirror.
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer --version=1.6.3 \
    && rm composer-setup.php \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

# Set composer home
ENV COMPOSER_HOME /var/www/html/composer

# Modify default apache docroot.
RUN sed -i "s/\/var\/www\/html/\/var\/www\/html\/web/g" /etc/apache2/sites-available/000-default.conf

# Ruby with mirror.
RUN apt-get install ruby ruby-dev -y \
    && gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/

# Install bootstrap and compass package.
RUN gem install bootstrap-sass:3.3.7 compass:1.0.3

# Nodejs with mirror.
RUN apt-get install gnupg -y \
    && curl -sL https://deb.nodesource.com/setup_9.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g cnpm --registry=https://registry.npm.taobao.org

# Extensions: uploadprogress, bcmath, mcrypt
RUN git clone https://github.com/Jan-E/uploadprogress.git /tmp/php-uploadprogress \
    && cd /tmp/php-uploadprogress \
    && phpize \
    && ./configure --prefix=/usr\
    && make \
    && make install \
    && echo 'extension=uploadprogress.so' > /usr/local/etc/php/conf.d/uploadprogress.ini \
    && rm -rf /tmp/* \
    && docker-php-ext-install bcmath \
    && apt install libmcrypt-dev -y \
    && pecl install mcrypt-1.0.1 \
    && docker-php-ext-enable mcrypt
