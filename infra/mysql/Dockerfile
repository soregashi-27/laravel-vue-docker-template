FROM mysql/mysql-server:5.7

ENV MYSQL_DATABASE=laravel_local \
  MYSQL_USER=soregashi27 \
  MYSQL_PASSWORD=secret \
  MYSQL_ROOT_PASSWORD=secret \
  TZ=Asia/Tokyo

COPY ./my.cnf /etc/my.cnf
RUN chmod 644 /etc/my.cnf