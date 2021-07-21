FROM mysql/mysql-server:8.0

ENV MYSQL_DATABASE=laravel_local \
  MYSQL_USER=soregashi27 \
  MYSQL_PASSWORD=soregashidegozaru \
  MYSQL_ROOT_PASSWORD=soregashidegozaru \
  TZ=Asia/Tokyo

COPY ./my.cnf /etc/my.cnf
RUN chmod 644 /etc/my.cnf