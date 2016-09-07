# Репозитории и начальный софт

Отредактировать репозиторий **/etc/apt/sources.list** на свежие релизы (*stable* > *testing* > *unstable*):

```bash
deb http://mirror.yandex.ru/debian unstable main contrib non-free
deb-src http://mirror.yandex.ru/debian unstable main contrib non-free
```

Также *backports* (свежие пакеты, но сама ветка ниже остальных по приоритету):

```bash
deb http://mirror.yandex.ru/debian jessie-backports main contrib
deb-src http://mirror.yandex.ru/debian jessie-backports main contrib
```

(установка пакетов с указанием ветки: `apt-get -t jessie-backports install letsencrypt`)

также можно установить официальную репу сообщества по инструкции:
https://www.dotdeb.org/instructions

Обновить список пакетов:

```bash
apt-get update
```

Обновить приложение по установке пакетов (убрать предупреждение *Ignoring Provides line with DepCompareOp for package*):

```bash
apt-get install apt
```

Обновить все пакеты в системе:

```bash
apt-get upgrade
```

Усовершенствованное обновление пакетов в системе (удалит старые пакеты, разрешит конфликты, доставит необходимые зависимости, доставит новые пакеты, если требуется):

```bash
apt-get dist-upgrade
```

Установить проги:

```bash
apt-get install mc screen htop grc curl siege
```

Перезагружаемся для изменения ядра и другого софта:

```bash
reboot
```

# Настройка системы

Смена часового пояса (проверка командой date):

```bash
rm /etc/localtime
ln -s /usr/share/zoneinfo/Europe/Moscow /etc/localtime
```

или через помощника:

```bash
dpkg-reconfigure tzdata
```

256 цветов (корректное отображение mc) - добавить в **/root/.bashrc**:

```bash
export TERM=xterm-256color
```

и проверить через:

```bash
tput colors
```

Полезные сокращения в **/root/.bashrc**:

Раскрашивание shell (http://sudouser.com/ukrashaem-bash.html):

```bash
PS1='\[\033[0;40;1;36m\]\w\[\033[0;40;0;35m\]\$\[\033[0m\] '
```

Раскрашивание команд:

```bash
alias ping="grc ping -c 4"
alias ls="grc ls -lA"
```

и просто синонимы:

```bash
alias nslookup+="nslookup -type=any"
alias r="service php7.0-fpm restart; service nginx restart"
```

Поправить экран приветсвия:

```bash
nano /etc/motd
```

Ускоряем загрузку по SSH, дописав в **/etc/ssh/sshd_config** строчку:

```
UseDNS no
```

и расскоментив:

```
GSSAPIAuthentication no
```

Поддержка русских сиволов:

```bash
dpkg-reconfigure locales
```

обязательно должен стоять `ru_RU.UTF-8` и выбрать его по умолчанию; (! дальше уже неактуально) далее установить:

```bash
apt-get install console-cyrillic
```

донастроить:

```bash
dpkg-reconfigure console-cyrillic
```

со следующими параметрами:

```
What virtual consoles do you use?                           -->  /dev/tty[1-6]
Choose the keyboard layout                                  -->  Russian
Toggling between Cyrillic and Latin characters              -->  Caps Lock
Switching temporarily between Cyrillic and Latin characters -->  No temporary switch
Choose a font for the console.                              -->  UniCyr
What is your favourite font size?                           -->  14
What is your encoding?                                      -->  UNICODE
Do you want to setup Cyrillic on the console at boot-time?  -->  Yes
```

перезагрузить систему и проверить командой:

```bash
locale
```

Для возможного убыстрения cтавим DNS от Яндекса в **/etc/resolv.conf**:

```
nameserver 77.88.8.8
nameserver 77.88.8.1
```

И для того, чтобы не тормозили IPv6, перед дописываем соответвующий DNS (пример от Google):

```
nameserver 2001:4860:4860::8888
```

Также можно установить по умолч. 4-ю версию в /etc/gai.conf раскомментить строчку:

```
precedence ::ffff:0:0/96  100
```

# Тесты производительности

```bash
apt-get install sysbench ioping
```

И запускаем:

диска (копирование файла):

```bash
dd bs=1M count=512 if=/dev/zero of=test conv=fdatasync
```

```
512+0 records in
512+0 records out
536870912 bytes (537 MB) copied, 3.2023 s, 168 MB/s
```

процессора:

```bash
sysbench --test=cpu --cpu-max-prime=20000 run
```

```
total time:                          28.7608s
```

оперативки:

```bash
sysbench --test=memory --memory-total-size=512M run
```

```
total time:                          0.5955s
```

диска (операций чтения/записи):

```bash
ioping . -c 10
```

```
10 requests completed in 9.01 s, 1.22 k iops, 4.76 MiB/s
min/avg/max/mdev = 169 us / 819 us / 5.26 ms / 1.50 ms
```

канала:

```bash
wget cachefly.cachefly.net/100mb.test
```

```
100mb.test          100%[=====================>] 100.00M  3.29MB/s   in 30s
2016-06-28 11:32:18 (3.37 MB/s) - ‘100mb.test’ saved [104857600/104857600]
```

# Серверные приложения

MySQL (и сервер, и клиент):

```bash
apt-get install mysql-server
```

и при установке пароля для root, входить в клиент:

```bash
mysql -p
```

также в файл **/etc/mysql/mysql.conf.d/mysqld.cnf** (мб **/etc/mysql/my.cnf**) после `skip-external-locking` добавить строчку:

```
skip-name-resolve
```

PHP и phpMyAdmin:

```bash
apt-get install php7.0 php7.0-fpm php7.0-mysql php7.0-mbstring phpmyadmin php-gettext
```

В **/etc/php/7.0/fpm/php.ini** раскомментить/заменить строки:

```ini
short_open_tag = On
display_errors = On
display_startup_errors = On
date.timezone = 'Europe/Moscow'
```

то же самое сделать и в **/etc/php/7.0/cli/php.ini** (для запуска из консоли командой php)

Nginx-сервер:

```bash
apt-get install nginx
```

Раскомментить в **/etc/nginx/nginx.conf** (типа когда доменов много):

```nginx
server_names_hash_bucket_size 64;
```

Создать php-сниппет **/etc/nginx/snippets/php.conf**:

```nginx
fastcgi_pass unix:/run/php/php7.0-fpm.sock;
try_files $fastcgi_script_name =404;
include fastcgi.conf;
```

И сам конфиг **/etc/nginx/sites-enabled/romanov-vrn**:

```nginx
server {
        server_name romanov-vrn.ru *.romanov-vrn.ru;
        listen 80 default_server;
        listen 443 default_server ssl;
        root /srv/romanov-vrn.ru/$subdomain;
        index index.html index.php;

        access_log /var/log/nginx/romanov-vrn.access.log;
        error_log /var/log/nginx/romanov-vrn.error.log;

        ssl_certificate /etc/letsencrypt/live/romanov-vrn.ru/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/romanov-vrn.ru/privkey.pem;

        set $subdomain "web";
        if ($host ~* ^([a-z0-9-\.]+)\.romanov-vrn.ru$) {
                set $subdomain $1;
        }
		if ($host ~* ^www.romanov-vrn.ru$) {
                set $subdomain "web";
        }

        set $isRedirectOnHttps 1;
        if ($scheme != http) { set $isRedirectOnHttps 0; }
        if ($subdomain != web) { set $isRedirectOnHttps 0; }
        if ($isRedirectOnHttps = 1) {
                return 307 https://$server_name$request_uri;
        }

        location ~ \.php$ {
                include snippets/php.conf;
        }
}
```

Установка шифрованного соединения SSL - HTTPS:

```bash
apt-get install letsencrypt
service nginx stop
letsencrypt certonly -d benzo36.ru -d www.benzo36.ru
```

выбрать пункт 2 (*Automatically use a temporary webserver (standalone)*)

дописать в nginx-конфиг сайта следующие строки:

```nginx
listen 443 ssl;
ssl_certificate /etc/letsencrypt/live/benzo36.ru/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/benzo36.ru/privkey.pem;
if ($scheme = http) {
    return 307 https://$server_name$request_uri;
}
```

Установка NodeJS:

```bash
apt-get install npm
```

Установка кеша Redis:

```bash
apt-get install redis-server
```

зайти - команда `redis-cli`

Установка БД mongo:

```bash
apt-get install mongodb
```

создать каталог **/data/db** и запустить сервер командой `mongod`

зайти - команда `mongo`

# Сопровождение

Обновление SSL-сертификатов:

```bash
service nginx stop
letsencrypt renew
service nginx stop
```
