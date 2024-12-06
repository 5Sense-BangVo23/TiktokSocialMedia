FROM php:8.2-apache

# Install dependencies and required PHP extensions
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    unzip \
    git \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql zip

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Copy application files to the Apache document root
COPY . /var/www/html

# Set the appropriate permissions for the application files
RUN chown -R www-data:www-data /var/www/html

# Configure Apache to allow .htaccess overrides and enable URL rewriting
RUN echo '<Directory /var/www/html/public>' > /etc/apache2/sites-available/000-default.conf && \
    echo '    AllowOverride All' >> /etc/apache2/sites-available/000-default.conf && \
    echo '    Require all granted' >> /etc/apache2/sites-available/000-default.conf && \
    echo '</Directory>' >> /etc/apache2/sites-available/000-default.conf

# Set the DocumentRoot to /public directory if needed
RUN echo 'DocumentRoot /var/www/html/public' >> /etc/apache2/sites-available/000-default.conf

# Enable mod_rewrite module
RUN a2enmod rewrite

# Set the working directory
WORKDIR /var/www/html

# Install Composer dependencies
RUN composer install --no-interaction --prefer-dist

# Expose the default HTTP port
EXPOSE 80

# Restart Apache to apply configuration changes
RUN service apache2 restart

# Start Apache in the foreground
CMD ["apache2-foreground"]
