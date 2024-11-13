```
stream {
    log_format basic '$remote_addr [$time_local] '
                    '$protocol $status $bytes_sent $bytes_received '
                    '$session_time "$upstream_addr" '
                    '"$upstream_bytes_sent" "$upstream_bytes_received" "$upstream_connect_time"';

    access_log  /var/log/nginx/access.log basic;
    error_log  /var/log/nginx/error.log;

    # Настройка upstream для мастер-нод Kubernetes API-сервера
    upstream kubernetes_apiserver {
        server 10.90.90.57:6443;  # test-kuber-master-n1
        server 10.90.90.58:6443;  # test-kuber-master-n2
        server 10.90.90.59:6443;  # test-kuber-master-n3
    }

    server {
        listen 6443;
        proxy_pass kubernetes_apiserver;
    }
}
# код для nginx tcp проксирование на порт 644

                          
```
ping telnet netcat nmap tracepath nslookup