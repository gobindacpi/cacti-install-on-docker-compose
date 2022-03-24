# cacti-install-on-docker-compose
~~~
Cacti Install by Docker Compose
*********************************************

Requirement:
Docker and Docker-Compose
yum update -y &&  reboot
yum install docker
curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
~~~
~~~
mkdir LAB
cd LAB/
vim docker-compose.yaml
~~~

# Just Copy below line in docker-compose.yaml
~~~
version: '3.5'
services:


  cacti:
    image: "gobindacpi/cacti:1"
    container_name: cacti
    domainname: example.com
    hostname: cacti
    ports:
      - "80:80"
      - "443:443"
    environment:
      - DB_NAME=cacti_master
      - DB_USER=cactiuser
      - DB_PASS=cactipassword
      - DB_HOST=db
      - DB_PORT=3306
      - DB_ROOT_PASS=rootpassword
      - INITIALIZE_DB=1
      - TZ=Asia/Dhaka
    volumes:
      - cacti-data:/cacti
      - cacti-spine:/spine
      - cacti-backups:/backups
    links:
      - db
    restart: always


  db:
    image: "mariadb:10.3"
    container_name: cacti_db
    domainname: example.com
    hostname: db
    ports:
      - "3306:3306"
    command:
      - mysqld
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
      - --max_connections=200
      - --max_heap_table_size=128M
      - --max_allowed_packet=32M
      - --tmp_table_size=128M
      - --join_buffer_size=128M
      - --innodb_buffer_pool_size=1G
      - --innodb_doublewrite=ON
      - --innodb_flush_log_at_timeout=3
      - --innodb_read_io_threads=32
      - --innodb_write_io_threads=16
      - --innodb_buffer_pool_instances=9
      - --innodb_file_format=Barracuda
      - --innodb_large_prefix=1
      - --innodb_io_capacity=5000
      - --innodb_io_capacity_max=10000
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - TZ=Asia/Dhaka
    volumes:
      - cacti-db:/var/lib/mysql
    restart: always

volumes:
  cacti-db:
  cacti-data:
  cacti-spine:
  cacti-backups:
~~~
Then Save and Exit
~~~
Docker-compose up -d

docker logs cacti
~~~
Then check browser with IP , UserName is admin and Password Admin
~~~

Best Mysql Tunning for belows
~~~
vim /etc/mysql/my.cnf file

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mariadb/mariadb.log
pid-file=/run/mariadb/mariadb.pid
collation-server=utf8mb4_unicode_ci
character-set-server=utf8mb4
max_heap_table_size=2048M
tmp_table_size=2048M
join_buffer_size=2048M
# 25% Of Total System Memory
innodb_buffer_pool_size=6GB
# pool_size/128 for less than 1GB of memory
innodb_buffer_pool_instances=18
innodb_flush_log_at_timeout=3
innodb_read_io_threads=2048
innodb_write_io_threads=2048
innodb_io_capacity=50000
innodb_file_format=Barracuda
innodb_large_prefix=1
innodb_io_capacity_max=100000


~~~

