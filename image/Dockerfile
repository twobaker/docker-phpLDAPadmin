FROM ubuntu:20.04

# phpLDAPadmin version - Test
ARG PHPLDAPADMIN_VERSION=1.2.6.7

# Set environment variables to make apt-get non-interactive
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Berlin

# Update package lists and install required packages
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
       tzdata \
       ca-certificates \
       apache2 \
       php7.4 \
       php7.4-fpm \
       php7.4-ldap \
       php7.4-readline \
       php7.4-xml \
       curl \
       openssl \
    && update-ca-certificates \
    && ln -fs /usr/share/zoneinfo/${TZ} /etc/localtime \
    && dpkg-reconfigure --frontend noninteractive tzdata \
    && curl -o phpldapadmin.tar.gz -SL https://github.com/leenooks/phpLDAPadmin/archive/${PHPLDAPADMIN_VERSION}.tar.gz \
    && mkdir -p /var/www/phpldapadmin_bootstrap /var/www/phpldapadmin \
    && tar -xzf phpldapadmin.tar.gz --strip 1 -C /var/www/phpldapadmin_bootstrap \
    && rm phpldapadmin.tar.gz \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Configure Apache
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf \
    && echo "SSLUseStapling off" >> /etc/apache2/sites-available/default-ssl.conf || { echo "Apache configuration failed"; exit 1; }

# Add service directory to /container/service
ADD service /container/service

# Use baseimage install-service script
RUN /container/tool/install-service || { echo "Install-service script failed"; exit 1; }

# Add default env variables
ADD environment /container/environment/99-default

# Set phpLDAPadmin data directory in a data volume
VOLUME ["/var/www/phpldapadmin"]

# Expose http and https default ports
EXPOSE 80 443
