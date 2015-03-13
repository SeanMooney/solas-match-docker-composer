FROM ubuntu-upstart

#install apache and php
RUN apt-get update && apt-get install git wget apache2 php5 libapache2-mod-php5 curl crudini  -y
RUN a2enmod rewrite
RUN apt-get install libapache2-mod-xsendfile sudo subversion php5-dev php-apc php5-mysql php5-mcrypt php5-curl php5-cli re2c -y
RUN apt-get install sysv-rc-conf -y && sysv-rc-conf apache2 on && sysv-rc-conf --list apache2
COPY 000-default.conf /etc/apache2/sites-available/000-default.conf

#clone git repos
RUN mkdir /repo -m 777
WORKDIR /repo
RUN git clone https://github.com/TheRosettaFoundation/SOLAS-Match.git
RUN git clone https://github.com/chobie/php-protocolbuffers.git

#add basic config #TODO update to be generic
WORKDIR /repo/SOLAS-Match
RUN cp Common/conf/conf.template.ini Common/conf/conf.ini
RUN chmod a+rw /repo/SOLAS-Match/Common/conf/conf.ini
RUN crudini --set /repo/SOLAS-Match/Common/conf/conf.ini site location http://192.168.1.5/
RUN crudini --set /repo/SOLAS-Match/Common/conf/conf.ini site api http://192.168.1.5/api/
RUN crudini --set /repo/SOLAS-Match/Common/conf/conf.ini database server "db"
RUN crudini --set /repo/SOLAS-Match/Common/conf/conf.ini database password ""

#install composer dependecies
WORKDIR /repo/SOLAS-Match/ui
RUN curl -s https://getcomposer.org/installer | php
RUN php composer.phar install
WORKDIR /repo/SOLAS-Match/api
RUN curl -s https://getcomposer.org/installer | php
RUN php composer.phar install

#install non composer dependecies
WORKDIR /repo/php-protocolbuffers
RUN phpize && ./configure && make && make install
RUN echo "extension=protocolbuffers.so" >>  /etc/php5/apache2/php.ini
RUN echo "[browscap]"  >>  /etc/php5/apache2/php.ini     >>  /etc/php5/apache2/php.ini
RUN echo "browscap = /etc/php5/apache2/php_browscap.ini"  >>  /etc/php5/apache2/php.ini    
RUN wget http://browscap.org/stream?q=PHP_BrowsCapINI -O /etc/php5/apache2/php_browscap.ini
RUN wget https://raw.githubusercontent.com/nathansmith/960-Grid-System/master/code/css/960.css -O  /repo/SOLAS-Match/resources/css/960.css

#create sysmlinks and direcorties.
RUN sudo ln -s /repo/SOLAS-Match/* /var/www/
RUN mkdir -p  /repo/SOLAS-Match/uploads -m 777
RUN mkdir -p  /repo/SOLAS-Match/ui/templating/templates_compiled -m 777
RUN mkdir -p  /repo/SOLAS-Match/ui/templating/cache -m 777
RUN chmod 777 /repo/SOLAS-Match/uploads
RUN chmod 777 /repo/SOLAS-Match/ui/templating/templates_compiled
RUN chmod 777 /repo/SOLAS-Match/ui/templating/cache

#expose webeserver port
EXPOSE 80
CMD ["/sbin/init"]
