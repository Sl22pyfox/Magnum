настроить проксирование через @magnginx
[https://test-alihan-kibana.magnum.kz/](https://test-alihan-kibana.magnum.kz/ "Пройти по ссылке") --> kirill-elastic-master-1:5601

Вот сюда добавляем строку ----->

```bash
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    include /etc/nginx/conf.d/parts/ssl.conf;
    include /etc/nginx/conf.d/kirill-elastic-master-1/main.conf;
}

```

Сюда  /nginx/conf.d/kirill-elastic-master-1/main.conf  добавляем -----> 
``` bash
location / {
             proxy_pass http://kirill-elastic-master-1.magnum.local:5601;
            }

```
Добавляем запись днс  https://test-alihan-kibana.magnum.kz/app/home 172.16.11.108   

We done it successufelly 