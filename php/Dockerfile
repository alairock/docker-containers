# I use phusion instead of Ubuntu, as it is a stripped down optimized ubuntu package optimized for docker.
FROM phusion/baseimage:0.9.16
MAINTAINER alairock

# Ensure UTF-8
RUN locale-gen en_US.UTF-8
ENV LANG       en_US.UTF-8
ENV LC_ALL     en_US.UTF-8

ENV HOME /root

RUN /etc/my_init.d/00_regen_ssh_host_keys.sh

CMD ["/sbin/my_init"]

# Nginx-PHP Installation
RUN apt-get update && \
  DEBIAN_FRONTEND="noninteractive" apt-get install -y vim curl wget build-essential python-software-properties && \
  add-apt-repository -y ppa:ondrej/php5-5.6 && \
  add-apt-repository -y ppa:nginx/stable && \
  apt-get update && \
  DEBIAN_FRONTEND="noninteractive" apt-get install -y --force-yes php5 php5-dev php5-pgsql php5-xdebug php5-curl php5-mcrypt php5-fpm php5-imagick && \
  DEBIAN_FRONTEND="noninteractive" apt-get install -y --force-yes imagemagick phpunit beanstalkd pdftk && \
  DEBIAN_FRONTEND="noninteractive" apt-get install -y nginx && \
  sudo curl -sS https://getcomposer.org/installer | sudo php && \
  sudo mv ./composer.phar /usr/local/bin/composer

RUN chown -R www-data:www-data /var/www

VOLUME /var/www:/var/www
ADD configs/sites-enabled/default.conf   /etc/nginx/sites-available/default
ADD configs/nginx.conf  				/etc/nginx/nginx.conf

ADD configs/php/www.conf /etc/php5/fpm/pool.d/www.conf
ADD configs/php/php-fpm.conf /etc/php5/fpm/php-fpm.conf
ADD configs/php/php.ini /etc/php5/fpm/php.ini

RUN mkdir           		/etc/service/nginx
ADD build/nginx.sh  		/etc/service/nginx/run
RUN chmod +x        		/etc/service/nginx/run
RUN mkdir           		/etc/service/phpfpm
ADD build/phpfpm.sh 		/etc/service/phpfpm/run
RUN chmod +x        		/etc/service/phpfpm/run


# Clean up APT when done.
RUN sudo apt-get clean && sudo rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/
WORKDIR /var/www
EXPOSE 80
