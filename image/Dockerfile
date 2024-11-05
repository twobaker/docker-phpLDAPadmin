FROM osixia/web-baseimage:release-1.2.0-dev

# phpLDAPadmin version
ARG PHPLDAPADMIN_VERSION=1.2.6.7

# Update package lists and upgrade existing packages
RUN apt-get update && apt-get upgrade -y || { echo "apt-get update/upgrade failed"; exit 1; }

# Add multiple process stack
RUN /container/tool/add-multiple-process-stack || { echo "Failed to add multiple process stack"; exit 1; }

# Add Apache, PHP-FPM, and SSL tools to services
RUN /container/tool/add-service-available :apache2 :php7.3-fpm :ssl-tools || { echo "Failed to add services"; exit 1; }

# Install required packages
RUN LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       ca-certificates \
       curl \
       apache2 \
       php7.3-fpm \
       php7.3-ldap \
       php7.3-readline \
       php7.3-xml \
       ssl-cert \
       openssl \
    || { echo "Package installation failed"; exit 1; }

# Download and set up phpLDAPadmin
RUN curl -o phpldapadmin.tar.gz -SL https://github.com/leenooks/phpLDAPadmin/archive/${PHPLDAPADMIN_VERSION}.tar.gz \
    && mkdir -p /var/www/phpldapadmin_bootstrap /var/www/phpldapadmin \
    && tar -xzf phpldapadmin.tar.gz --strip 1 -C /var/www/phpldapadmin_bootstrap \
    && rm phpldapadmin.tar.gz || { echo "phpLDAPadmin setup failed"; exit 1; }

# Clean up unused packages and temporary files
RUN apt-get remove -y --purge --auto-remove curl ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* || { echo "Cleanup failed"; exit 1; }

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
