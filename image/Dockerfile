FROM osixia/web-baseimage:release-1.2.0-dev

# phpLDAPadmin version
ARG PHPLDAPADMIN_VERSION=1.2.6.7

# Update and install necessary packages with PHP 7.3
RUN apt-get update && apt-get upgrade -y \
    && /container/tool/add-multiple-process-stack \
    && /container/tool/add-service-available :apache2 :php7.3-fpm :ssl-tools \
    && LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       ca-certificates \
       curl \
       apache2 \
       php7.3-fpm \
       php7.3-ldap \
       php7.3-readline \
       php7.3-xml \
       ssl-cert \
       openssl \
    && curl -o phpldapadmin.tar.gz -SL https://github.com/leenooks/phpLDAPadmin/archive/${PHPLDAPADMIN_VERSION}.tar.gz \
    && mkdir -p /var/www/phpldapadmin_bootstrap /var/www/phpldapadmin \
    && tar -xzf phpldapadmin.tar.gz --strip 1 -C /var/www/phpldapadmin_bootstrap \
    && apt-get remove -y --purge --auto-remove curl ca-certificates \
    && rm phpldapadmin.tar.gz \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Configure Apache
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf \
    && echo "SSLUseStapling off" >> /etc/apache2/sites-available/default-ssl.conf

# Add service directory to /container/service
ADD service /container/service

# Use baseimage install-service script
RUN /container/tool/install-service

# Add default env variables
ADD environment /container/environment/99-default

# Set phpLDAPadmin data directory in a data volume
VOLUME ["/var/www/phpldapadmin"]

# Expose http and https default ports
EXPOSE 80 443
