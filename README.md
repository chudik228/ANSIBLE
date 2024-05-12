ВАРИАНТ 1

Гайд на вланы
на 3
sudo ip route add 172.19.0.0/16 via 10.10.0.1

на 1
sudo ip route add 10.10.0.0/16 via 172.19.0.1

sudo ip link add eth0.10 link eth0 type vlan id 10
sudo ip addr add 172.19.0.2/16 dev eth0.10
sudo ip link set dev eth0.10 up

sudo ip link add enp0s3.10 link enp0s3 type vlan id 10
sudo ip addr add 172.19.0.1/16 dev enp0s3.10
sudo ip link set dev enp0s3.10 up

sudo ip link add enp0s8.20 link enp0s8 type vlan id 20
sudo ip addr add 10.10.0.1/16 dev enp0s8.20
sudo ip link set dev enp0s8.20 up

sudo ip link add eth0.20 link eth0 type vlan id 20
sudo ip addr add 10.10.0.2/16 dev eth0.20
sudo ip link set dev eth0.20 up
sudo ip route add 10.10.0.0/16 via 172.19.0.1

sudo sysctl net.ipv4.ip_forward=1(все)

ВАРИАНТ 2

Подготовка 
nano /etc/selinux/config
SELINUX=enforcing на SELINUX=permissive
setenforce 0
Начинаем 
dnf update
dnf install httpd zabbix-apache-conf zabbix-sql-scripts
systemctl enable httpd

dnf install mariadb mariadb-server zabbix-server-mysql zabbix-agent
systemctl start mariadb
systemctl enable mariadb.service
mysql_secure_installation

mysql -uroot -pmasterkey
> create   database zabbix character set utf8 collate utf8_bin;
> grant all privileges on zabbix.* to zabbix@localhost identified by 'zabpassword';
> FLUSH PRIVILEGES;
> quit;

zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -pzabpassword zabbix

nano /etc/zabbix/zabbix_server.conf
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabpassword

nano /etc/php.ini
date.timezone = Europe/Moscow      # обязательно укажите свой часовой пояс! 
post_max_size = 16M
max_execution_time = 300
max_input_time = 300

systemctl restart httpd
systemctl restart zabbix-server
Теперь агент
apt-get install zabbix-agent
/etc/zabbix/zabbix_agentd.conf
Server=<ip-сервера>
ServerActive=<ip-сервера>
Hostname=zabzab

ВАРИАНТ 3

Гайд на хелпдеск
1. Установка Apache:
dnf install httpd
systemctl enable --now httpd
2. Установка PHP:
dnf install php php-mysqli php-fpm php-json php-gd php-imap
3. Установка MySQL (MariaDB):
dnf install mariadb-server
systemctl enable --now mariadb
4. Обеспечение безопасности MySQL:
mysql_secure_installation
5. Создание базы данных и пользователя для HESK:
mysql -u root -p
Затем выполните следующие SQL-запросы:
CREATE DATABASE hesk CHARSET utf8mb4 COLLATE utf8mb4_unicode_ci;
GRANT ALL PRIVILEGES ON hesk.* TO 'hesk'@'localhost' IDENTIFIED BY 'ваш_пароль';
FLUSH PRIVILEGES;
EXIT;
6. Загрузка и распаковка HESK:
cd /var/www/html/
unzip %hesk_archive%
7. Настройка прав:
chown -R apache:apache /var/www/html
8. Запуск установки HESK:
Перейдите в браузере на страницу http://site-adress/install и следуйте инструкциям на экране для установки HESK.
9. Удаление каталога установки:
После завершения установки удалите каталог install:
rm -rf /var/www/html/install/

Патчи и фиксы ошибок:
Chmod 666 
Chmod 777 
setenforce 0


