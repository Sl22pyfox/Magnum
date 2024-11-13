```
df -h #see disk information

sudo netstat -tuln | grep <номер_порта>  # посмотреть порт в сети
sudo lsof -i :9092  #подробнее о порте и кем используется
nc -zv 10.70.8.44 32572 
grep -ri dns.magnum.kz /etc/nginx
find -t
sed -i 's/старый_текст/новый_текст/g' имя_файла
#создание пода 
kubectl run -i --tty ping-pod --image=busybox --restart=Never -- /bin/sh  kubectl api-resources
```
