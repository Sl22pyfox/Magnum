```
vagrant init afreisinger/centos-7.9 \
  --box-version 1.0.0
```
Подключаемся к нашей виртуальной машине 
`vagrant ssh`

Скачиает ssh для подключения с vagrant

```
sudo apt update
sudo apt install openssh-server
sudo systemctl start ssh
sudo systemctl enable ssh

```
Открываем порты если заблокированы

```
sudo ufw allow 22
sudo ufw enable
```


```
service firewalld stop  
chkconfig firewalld off  
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config  
setenforce 0
swapoff -a  
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
