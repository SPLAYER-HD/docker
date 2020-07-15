FROM ubuntu

WORKDIR /var/www/html/
RUN apt update \
  && DEBIAN_FRONTEND="noninteractive" apt -y install tzdata curl php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip apache2\
  && curl -O https://wordpress.org/wordpress-5.4.2.tar.gz \
  && tar -xvf wordpress-5.4.2.tar.gz \
  && rm wordpress-5.4.2.tar.gz

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

