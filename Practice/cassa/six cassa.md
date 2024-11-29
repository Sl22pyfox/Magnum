arch@test-six-cassa:~$ sudo cat /etc/netplan/00-installer-config.yaml
[sudo] password for arch: 
Sorry, try again.
[sudo] password for arch: 
network:
  ethernets:
    enp2s0:
      addresses:
        - 10.152.6.67/26
      dhcp4: false
      match:
        name: e*
      nameservers:
        addresses:
          - 172.16.10.10
          - 172.16.10.13
      routes:
        - to: default
          via: 10.152.6.65
  version: 2
arch@test-six-cassa:~$ 
