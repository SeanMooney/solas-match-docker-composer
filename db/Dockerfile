FROM ubuntu-upstart

#install apt-packages
RUN apt-get update && apt-get install git wget curl  php5-cli php5-curl -y
RUN apt-get install sysv-rc-conf mysql-server mysql-client -y && sysv-rc-conf mysql on && sysv-rc-conf --list mysql
RUN cat /etc/mysql/my.cnf
RUN sed -e "s/bind-address            = 127.0.0.1/bind-address            = 0.0.0.0/g" -i /etc/mysql/my.cnf
RUN service mysql start

#clone git repos
RUN mkdir /repo -m 777
WORKDIR /repo
RUN git clone https://github.com/TheRosettaFoundation/SOLAS-Match.git

WORKDIR /repo/SOLAS-Match/api
RUN curl -s https://getcomposer.org/installer | php
RUN php composer.phar install

WORKDIR /repo/SOLAS-Match
RUN netstat -l --numeric-ports
#RUN sg mysql mysqld
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root -e "create database solas;" 
RUN service mysql start &&  mysql -h 127.0.0.1 -P 3306 -u root -e "CREATE USER 'tester'@'%.%.%.%';"
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root solas  -e "GRANT ALL ON solas TO 'tester'@'%.%.%.%';"
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root solas < api/vendor/league/oauth2-server/sql/mysql.sql 
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root solas < db/schema.sql 
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root solas < db/languages.sql 
RUN service mysql start && mysql -h 127.0.0.1 -P 3306 -u root solas < db/country_codes.sql  
#RUN service mysql start && mysql -h 127.0.0.1 -P 3306 solas -e "GRANT EXECUTE, PROCESS, SELECT, SHOW DATABASES, SHOW VIEW, DELETE, INSERT, UPDATE, LOCK TABLES ON . TO 'tester'@'%.%.%.%'; FLUSH PRIVILEGES; SHOW GRANTS FOR 'tester'@'%.%.%.%'; "




#expose webeserver port
EXPOSE 3306
CMD ["/sbin/init"]
