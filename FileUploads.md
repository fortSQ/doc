# Конфигурация сервера на загрузку файлов

Пример правок для загрузки одного файла размером до 25 МБ и до 5 файлов за раз - т.е. суммарно 25 * 5 = 125 МБ:

**/etc/nginx/nginx.conf** (по умолч.) или в конкретном конфиге в секции _server_:

```nginx
client_max_body_size 125m;
```

**/etc/php5/fpm/php.ini**:

```ini
upload_max_filesize = 25M
max_file_uploads    = 5
post_max_size       = 125M
```
